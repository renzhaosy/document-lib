# Nginx

## Ngixn 优点

- 高并发高性能
- 可扩展性好
- 高可靠性
- 热部署
- 开源许可证

## 应用场景

- 静态资源服务，通过本地文件系统提供服务
- 反向代理服务、负载均衡

## Nginx 负载均衡

**upstream** 模块主要负责负载均衡的配置。

Ngxin 提供两种负载均衡的策略：内置策略和扩展策略。
内置策略：

- 轮询
- 加权轮询
- Ip hash

### 轮询

Nginx 默认就是轮询其权重都默认为 1，服务器处理请求的顺序：ABABABABAB….

```nginx

upstream mysvr {
    server 127.0.0.1:7878;
    server 192.168.10.121:3333;
}
```

### 加权轮询

跟据配置的权重的大小而分发给不同服务器不同数量的请求。如果不设置，则默认为 1。下面服务器的请求顺序为：ABBABBABBABBABB….

```nginx
upstream mysvr {
    server 127.0.0.1:7878 weight=1;
    server 192.168.10.121:3333 weight=2;
}
```

### ip_hash

Nginx 会让相同的客户端 ip 请求相同的服务器。
Ip hash 算法，对客户端请求的 ip 进行 hash 操作，然后根据 hash 结果将同一个客户端 ip 的请求分发给同一台服务器进行处理，可以解决 session 不共享的问题。

```nginx
upstream mysvr {
    server 127.0.0.1:7878;
    server 192.168.10.121:3333;
    ip_hash;
}
```

### url_hash 第三方

按照访问 URL 的 hash 结果来分配请求，使得每个 URL 定向到同一个后端服务器上，主要应用于后端服务器为缓存时的场景。

### 其他配置

- down， 表示 当前 server 不参与负载
- backup，预留的备份机器。当其他所有的非 backup 机器出现故障或者忙的时候，才会请求 backup 机器
- max_fails，允许请求失败的次数。默认为 1。当超过最大次数时，返回 proxy_next_upstream 模块定义的错误。
- fail_timeout，在经历了 max_fails 次失败后，暂停服务的时间。max_fails 可以和 fail_timeout 一起使用。

## 防盗链

防盗链的原理就是根据请求头中 referer 得到网页来源，从而实现访问控制。这样可以防止网站资源被非法盗用，从而保证信息安全，减少带宽损耗，减轻服务器压力。

```nginx
# 匹配防盗链资源的文件类型
location ~ .*\.(jpg|png|gif)$ {
    # 通过 valid_referers 定义合法的地址白名单 $invalid_referer 不合法的返回403
    valid_referers none blocked test.com;
    if ($invalid_referer) {
        return 403;
    }
}
```
