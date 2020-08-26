# 前端监控方案 - 埋点 SDK

## 背景

在此方案之前，公司采用的是私有化部署的 `sentry`，来满足项目的错误监控、业务事件的埋点等功能的。

但是

1. 由于 渐渐不能满足业务的需求，二次开发受限。(主要原因)
2. 资金投入比较大。

所以就有了前端公共埋点方案的出现。

### 整体流程

![前端监控流程](http://cdn.renzhaosy.cn/daily/前端监控流程.png)

其中包括了：

- 前端统一 SDK： 用于 H5、微信小程序、支付宝小程序的前端埋点
- 埋点 node 服务：统一处理埋点请求，并发送 kafka 事件，用于监控平台/数据平台/广告回传平台消费。

### 前端 SDK

前端监控 SDK 主要负责，前端本身产生的一些行为数据以及异常信息，主要包括以下部分：

- 埋点数据 （业务数据）
- 异常数据 （前端报错等）
- 指标数据 （性能指标、自定义业务指标） - 性能指标： FP/FCP、FMP、TTI

### SDK

整个 SDK 为了维护、扩展和数据的处理，采用责任链模式，每一个事件的数据流都会依据整个责任链流转，并在每一个阶段被处理。包括了自动采集的数据、手动埋点上报数据。

#### 初始化

![初始化](http://cdn.renzhaosy.cn/daily/sdk-init.jpg)
初始化过程包括 - 初始化 多个自动采集 collector - 初始化 数据处理链路 - 根据当前环境初始化 runtime

#### runtime

runtime 提供了 当前环境可用的 ，环境 env、存储 store、请求方法等 API 和全局属性。

#### 数据流

![SDK数据流](http://cdn.renzhaosy.cn/daily/前端SDK流程.png)

SDK 中基于 pipeline 来实现对数据的处理以及上报。

- SampleRate, 采样处理，当前默认为 1，后续可根据数据类型进行采样
- PresetProperty, 预留数据处理，主要用于将上报的原始数据完善一些预置字段，例如: clientId 等
- OfflineStorage, 对数据进行离线存储
- ReportStrategy, 上报策略，满足两个条件即上报：缓存区溢出、timeout 到期
- RootReport, 上报处理，每次上报，如失败默认重试两次，上报成功后，触发定时器检查

#### 离线存储

为了防止埋点数据的丢失，数据都会先备份在 localStorage（小程序会存在 storage）中，每次上报时会取出备份数据的前 5 条发送，上报成功后删除。

#### 日志上报

上报的请求是基于**xhr** 和 **Navigator.sendBeacon**
(用于发送 leavePage 事件)进行简单的封装。
为了节省请求数量，储存和上报是一个异步的过程，储存完之后并不会立即上报，而是会延迟几秒。

#### 用户唯一标识

为了统计用户，每次数据处理时都会带上唯一的标识符 `clientId`， 并且对于登录的用户会带上用户的 `token`,用户数据平台绑定用户信息。

#### 异常分类

前端异常种类较多，按照类型以及采集方式可以分为以下几种：

##### JS 代码异常

JS 代码运行时异常， js 发生运行时错误时候，window 会出发 ErrorEent 接口的 error 时间，通过 window.onerror 可以捕获全局的异常

##### Promise 异常

论 try-catch 还是 on-error 都无法捕获 promise 中抛出的错误，需要通过监听全局 unhandlerejection 来捕获此类错误。
从业务的角度来讲，不一定所有的 promise reject 都属于异常行为，故对于不需要抛出异常的情况，还是尽量通过 promise 来 catch

##### 静态资源加载异常

onerror 不会捕获资源加载异常，需要通过 window.addEventListener(‘error’， callback, true )的方式捕获异常

##### Ajax 请求异常

可考虑代理原声的 xmlhttprequest 或者 axios 对象来实现对 http 请求调用的拦截。

## 标准

### 性能指标

性能指标的采集，在实现上需要保持对页面较低的侵入性。
随着前端相关规范的推进， [Performance 系列 API ](https://developer.mozilla.org/en-US/docs/Web/API/Performance_API) 功能越来越强大，我们在实现的时候，考虑优先通过原生 API 来采集。

#### **FP/FCP**

FP ( first paint):：首次绘制时间，理解为浏览器首个像素的绘制时间
FCP (first contentfull)： 首个内容绘制，首个可见元素的绘制时间，包括文本、图片、canvas 等。

从用户的感知来讲，fp 时间接近白屏时间，fcp 主要受 index.html dom 结构以及 loading 等影响。故我们主要以**fp**为参考指标。fp/fcp 的采集，可以直接使用 paint timing api 来采集，从测试数据来看，**fp 都比较接近白屏时间**。

FP 指标，根据浏览器的兼容性，依次按照如下方式进行采集

window.performance.getEntries
window.performance.timing

#### FMP (fitst meanningful paint)

首个有效绘制，也就是页面的核心元素渲染完成时间点。不同网站，何核心元素不一样。比较接近首屏屏时间。

FMP 目前来看并没有标准的 API 支持，google lighthouse 中采集的 fmp 的准确率仅为 70%左右。但是可以看到主流的前端监控，基本都将 FMP 作为一项重要的性能指标。我们可以根据自身的业务特点，做关键的特征提取，然后调整权重算法，达到较为接近真实数据的指标。

FMP 算法主要基于 Element Timing 以及权重

## 总结

这章主要是介绍了 前端监控中 前端埋点 SDK 相关的东西。
