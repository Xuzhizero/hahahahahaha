---
title: udp通讯并不特别依赖qt框架中的库吧
slug: oguceoqe9qbt539i
url: https://www.yuque.com/caogongzi/pu6995/oguceoqe9qbt539i
---

# 提问
<font style="color:rgba(0, 0, 0, 0.85);background-color:rgba(0, 0, 0, 0.04);">利用CPP开发udp通讯的功能，具体来说，包含收，发，解析，处理的功能，也包括入Redis库，读数据和改（删）数据的功能。这部分功能并不特别依赖qt框架中的库吧？</font>

# <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">回答</font>
你的理解是正确的，这部分功能**可以完全不依赖Qt框架的库**，通过C++的**标准库**和**第三方库（如Redis客户端库）**即可实现。以下是具体的技术选型和实现思路：



### 一、UDP通信功能（收、发、解析、处理）
可通过C++的**标准网络库（`<sys/socket.h>`等）**或更易用的第三方网络库实现：

#### 1. 基于Linux系统调用（原生Socket）
适用于Linux环境，直接使用系统API：

+ **发送UDP数据**：

```cpp
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <cstring>
#include <iostream>

int main() {
    int sockfd = socket(AF_INET, SOCK_DGRAM, 0); // 创建UDP套接字
    if (sockfd < 0) { perror("socket"); return -1; }

    sockaddr_in dest_addr;
    dest_addr.sin_family = AF_INET;
    dest_addr.sin_port = htons(8888); // 目标端口
    inet_pton(AF_INET, "127.0.0.1", &dest_addr.sin_addr); // 目标IP

    const char* send_buf = "Hello UDP!";
    sendto(sockfd, send_buf, strlen(send_buf), 0, 
           (sockaddr*)&dest_addr, sizeof(dest_addr));

    close(sockfd);
    return 0;
}
```

+ **接收UDP数据**：

```cpp
int sockfd = socket(AF_INET, SOCK_DGRAM, 0);
sockaddr_in local_addr;
local_addr.sin_family = AF_INET;
local_addr.sin_port = htons(8888); // 本地监听端口
local_addr.sin_addr.s_addr = INADDR_ANY; // 绑定所有网卡

bind(sockfd, (sockaddr*)&local_addr, sizeof(local_addr));

char recv_buf[1024] = {0};
sockaddr_in peer_addr;
socklen_t peer_addr_len = sizeof(peer_addr);
int recv_len = recvfrom(sockfd, recv_buf, sizeof(recv_buf)-1, 0, 
                       (sockaddr*)&peer_addr, &peer_addr_len);
if (recv_len > 0) {
    std::cout << "Received from " << inet_ntoa(peer_addr.sin_addr) 
              << ":" << ntohs(peer_addr.sin_port) 
              << ": " << recv_buf << std::endl;
}

close(sockfd);
```

+ **解析与处理**：根据UDP数据的自定义格式（如二进制、JSON、Protobuf等），在接收后解析数据结构，再执行业务逻辑。



#### 2. 基于跨平台网络库（如Boost.Asio）
若需要跨平台（Windows/Linux），推荐使用`Boost.Asio`，它封装了底层Socket，使用更简洁：

```cpp
#include <boost/asio.hpp>
#include <iostream>

using boost::asio::ip::udp;

// 发送示例
void send_udp() {
    boost::asio::io_context io_context;
    udp::socket socket(io_context, udp::endpoint(udp::v4(), 0));
    udp::endpoint receiver_endpoint = 
        udp::endpoint(boost::asio::ip::address::from_string("127.0.0.1"), 8888);
    socket.send_to(boost::asio::buffer("Hello Boost.Asio UDP!"), receiver_endpoint);
}

// 接收示例
void receive_udp() {
    boost::asio::io_context io_context;
    udp::socket socket(io_context, udp::endpoint(udp::v4(), 8888));
    udp::endpoint sender_endpoint;
    char recv_buf[1024];
    size_t len = socket.receive_from(
        boost::asio::buffer(recv_buf), sender_endpoint);
    std::cout << "Received: " << std::string(recv_buf, len) << std::endl;
}
```



### 二、Redis操作（读、写、删数据）
需引入**Redis C++客户端库**，常用选择有：

#### 1. hiredis（官方C客户端，轻量高效）
+ 安装：`sudo apt install libhiredis-dev`（Linux）；Windows需手动编译。
+ 基本操作示例：

```cpp
#include <hiredis/hiredis.h>
#include <iostream>

int main() {
    // 连接Redis
    redisContext* ctx = redisConnect("127.0.0.1", 6379);
    if (ctx == nullptr || ctx->err) {
        if (ctx) std::cerr << "Error: " << ctx->errstr << std::endl;
        else std::cerr << "Failed to allocate context" << std::endl;
        return -1;
    }

    // 写数据（SET）
    redisReply* reply = (redisReply*)redisCommand(ctx, "SET key1 %s", "value1");
    freeReplyObject(reply);

    // 读数据（GET）
    reply = (redisReply*)redisCommand(ctx, "GET key1");
    if (reply->type == REDIS_REPLY_STRING) {
        std::cout << "GET key1: " << reply->str << std::endl;
    }
    freeReplyObject(reply);

    // 删除数据（DEL）
    reply = (redisReply*)redisCommand(ctx, "DEL key1");
    freeReplyObject(reply);

    // 关闭连接
    redisFree(ctx);
    return 0;
}
```



#### 2. redis-plus-plus（C++封装，更易用）
基于hiredis的C++封装，支持现代C++特性（如智能指针、STL）：

+ 安装：`sudo apt install libredis-plus-plus-dev`（Linux）。
+ 示例：

```cpp
#include <sw/redis++/redis++.h>
#include <iostream>

using namespace sw::redis;

int main() {
    ConnectionOptions opts;
    opts.host = "127.0.0.1";
    opts.port = 6379;

    Redis redis(opts);

    // 写数据
    redis.set("key2", "value2");

    // 读数据
    auto val = redis.get("key2");
    if (val) std::cout << "GET key2: " << *val << std::endl;

    // 删除数据
    redis.del("key2");

    return 0;
}
```



### 三、总结
这部分功能（UDP通信、Redis操作）**完全不需要依赖Qt库**，通过C++标准库、系统调用或第三方库（Boost.Asio、hiredis、redis-plus-plus等）即可独立实现。你可以根据项目的**平台需求（是否跨平台）和开发习惯**，选择原生Socket、Boost.Asio作为网络层，hiredis或redis-plus-plus作为Redis客户端，从而完成收、发、解析、Redis数据操作等全流程功能。


