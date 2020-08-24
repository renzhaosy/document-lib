# Nginx 负载均衡

**upstream** 模块主要负责负载均衡的配置。

Ngxin 提供两种负载均衡的策略：内置策略和扩展策略。
内置策略：

- 轮询
- 加权轮询
- Ip hash
  Ï

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

### 其他配置

- down， 表示 当前 server 不参与负载
- backup，预留的备份机器。当其他所有的非 backup 机器出现故障或者忙的时候，才会请求 backup 机器
- max_fails，允许请求失败的次数。默认为 1。当超过最大次数时，返回 proxy_next_upstream 模块定义的错误。
- fail_timeout，在经历了 max_fails 次失败后，暂停服务的时间。max_fails 可以和 fail_timeout 一起使用。
