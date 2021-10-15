---
title: Laravel 广播系统
description: Laravel 广播系统前后端解决方案
keywords:
    - Laravel
    - Broadcasting
    - Pusher
    - Redis
summary: Laravel 广播系统前后端解决方案
date: 2021-10-13T06:14:14.000Z
draft: false
---

[中文文档](https://learnku.com/docs/laravel/6.x/broadcasting/5159)

## 技术点

- [WebSocket](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)

- [Socket.IO](https://socket.io/)

- [Pusher](https://pusher.com/)

- [Redis 发布订阅](https://www.runoob.com/redis/redis-pub-sub.html)

## 驱动

### Pusher
>
>- 服务端
>
>   [Pusher](https://dashboard.pusher.com/apps)
>
>- 客户端
>
>   - 后端
>
>       [pusher-http-php](https://github.com/pusher/pusher-http-php)
>
>      `composer require pusher/pusher-php-server "~4.>0"`
>
>       Laravel .env 配置
>
>       ```ENV
>       # 广播驱动
>       BROADCAST_DRIVER=pusher
>       # PUSHER
>       PUSHER_APP_ID=
>       PUSHER_APP_KEY=
>       PUSHER_APP_SECRET=
>       PUSHER_APP_CLUSTER=
>       ```
>
>   - 前端
>
>       [Laravel Echo](https://github.com/laravel/echo)
>
>       [Pusher JS](https://github.com/pusher/pusher-js)
>
>       `npm install --save laravel-echo pusher-js`
>
>       ```JavaScript
>       import Echo from "laravel-echo";
>
>       window.Pusher = require('pusher-js');
>
>       window.Echo = new Echo({
>           broadcaster: 'pusher',
>           key: 'your-pusher-channels-key'
>       });
>       ```

### Redis
>
>- 服务端
>
>   [laravel-echo-server](https://github.com/>tlaverdure/laravel-echo-server)
>
>- 客户端
>
>   - 后端
>
>       `composer require predis/predis`
>
>       Laravel .env 配置
>
>       ```ENV
>       # 广播驱动
>       BROADCAST_DRIVER=redis
>       # REDIS
>       REDIS_HOST=127.0.0.1
>       REDIS_PASSWORD=
>       REDIS_PORT=6379
>       REDIS_PREFIX=
>       ```
>
>   - 前端
>
>       [Laravel Echo](https://github.com/laravel/echo)
>
>       [SocketIo Client](https://github.com/socketio/socket.io-client)
>
>       `npm install --save laravel-echo socket.io-client`
>
>       ```Javascript
>       import Echo from "laravel-echo"
>
>       window.io = require('socket.io-client');
>
>       window.Echo = new Echo({
>           broadcaster: 'socket.io',
>           host: window.location.hostname + ':6001'
>       });
>       ```

## 开发细节

### 自定义广播频道

- 后端

    ```PHP
    public function broadcastOn()
    {
        return new PrivateChannel('user.'.$this->user->id);
    }
    ```

- 前端

    ```JavaScript
    laravelEcho.private(`user.${currentUser.id}`)
    ```

### 自定义事件名称

- 后端

    ```PHP
    public function broadcastAs()
    {
        return 'user.updated';
    }
    ```

- 前端

    ```JavaScript
    // 如果后端通过 broadcastAs 方法自定义广播名称，注册了监听器时要确保使用一个前导的 . 字符，这将指示 Echo 不要将应用程序的命名空间添加到事件中
    laravelEcho
    .private(`user.${currentUser.id}`)
    .listen('.user.updated', (e) => {
        console.log(e);
    })
    ```

### 自定义广播数据

- 后端

    ```PHP
    public function broadcastWith()
    {
        return ['id' => $this->user->id];
    }
    ```

## Issues

- 前端
  - [Presence 频道监听报错（TypeScript ）](https://github.com/laravel/echo/issues/239#issuecomment-550114637)
  - [Pusher 私有频道监听用户认证](https://github.com/laravel/echo/issues/26#issuecomment-912402230)
  - [客户端无法建立到 laravel-echo-server 服务端的 ws 连接](https://github.com/tlaverdure/laravel-echo-server/issues/576#issuecomment-914033576)，socket.io 客户端版本跟 laravel-echo-server 服务端版本必须兼容 [socket.io 客户端和服务端版本对照](https://socket.io/docs/v4/client-installation/index.html)，服务端 laravel-echo-server 1.6.2（socket.io 2.x） 可搭配客户端 socket.io 2.3.1 (2.x)版本
