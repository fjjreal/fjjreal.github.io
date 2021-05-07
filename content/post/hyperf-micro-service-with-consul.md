---
title: "Hyperf Micro Service With Consul"
date: 2021-05-07T16:35:38+08:00
draft: false
tags: [
    "micro-service",
]
categories: [
	"notebook",
]
---

<!--more-->

> [hyperf 文档](https://hyperf.wiki/2.1/#/)

> [hyperf 文档](https://hyperf.wiki/2.1/#/)

> [hyperf 文档](https://hyperf.wiki/2.1/#/)


# JSON RPC

> [hyperf 文档](https://hyperf.wiki/2.1/#/zh-cn/json-rpc)

## 服务提供者

- composer require hyperf/json-rpc

- composer require hyperf/rpc-server

- composer require hyperf/service-governance

- composer require hyperf/consul

- 配置config/autoload/server.php => servers jsonrpc-http

	```
		[
	        'name' => 'jsonrpc-http',
	        'type' => Server::SERVER_HTTP,
	        'host' => '0.0.0.0',
	        'port' => 9504,
	        'sock_type' => SWOOLE_SOCK_TCP,
	        'callbacks' => [
	            Event::ON_REQUEST => [\Hyperf\JsonRpc\HttpServer::class, 'onRequest'],
	        ],
	    ],
	```

- 服务类

	```php
	<?php
	namespace App\JsonRpc;
	interface CalculatorServiceInterface{
	    public function add(int $a, int $b) :int;
	}
	```

	```php
	<?php
	namespace App\JsonRpc;
	use Hyperf\RpcServer\Annotation\RpcService;
	/**
	 * 注意，如希望通过服务中心来管理服务，需在注解内增加 publishTo 属性
	 * @RpcService(name="CalculatorService", protocol="jsonrpc-http", server="jsonrpc-http", publishTo="consul")
	 */
	class CalculatorService implements CalculatorServiceInterface
	{
	    // 实现一个加法方法，这里简单的认为参数都是 int 类型
	    public function add(int $a, int $b): int
	    {
	        // 这里是服务方法的具体实现
	        return $a + $b;
	    }
	}
	```

- consul 配置 config/autoload/consul.php

	```php
	<?php
	return [
	    'uri' => 'http://127.0.0.1:8500',
	];
	```

## 消费者

- composer require hyperf/json-rpc

- composer require hyperf/rpc-client

- composer require hyperf/consul

- 服务类
	```php
	<?php
	namespace App\JsonRpc;
	interface CalculatorServiceInterface{
	    public function add(int $a, int $b) :int;
	}
	```

- 自动获取服务

	```
	<?php
	return [
	    'consumers' => [
	        [
	            // name 需与服务提供者的 name 属性相同
	            'name' => 'CalculatorService',
	            // 服务接口名，可选，默认值等于 name 配置的值，如果 name 直接定义为接口类则可忽略此行配置，如 name 为字符串则需要配置 service 对应到接口类
	            'service' => \App\JsonRpc\CalculatorServiceInterface::class,
	            // 对应容器对象 ID，可选，默认值等于 service 配置的值，用来定义依赖注入的 key
	            'id' => \App\JsonRpc\CalculatorServiceInterface::class,
	            // 服务提供者的服务协议，可选，默认值为 jsonrpc-http
	            // 可选 jsonrpc-http jsonrpc jsonrpc-tcp-length-check
	            'protocol' => 'jsonrpc-http',
	            // 负载均衡算法，可选，默认值为 random
	            'load_balancer' => 'random',
	            // 这个消费者要从哪个服务中心获取节点信息，如不配置则不会从服务中心获取节点信息
	            'registry' => [
	                'protocol' => 'consul',
	                'address' => 'http://127.0.0.1:8500',
	            ],
	            // 如果没有指定上面的 registry 配置，即为直接对指定的节点进行消费，通过下面的 nodes 参数来配置服务提供者的节点信息
	            // 'nodes' => [
	            //     ['host' => '127.0.0.1', 'port' => 9504],
	            // ],
	            // 配置项，会影响到 Packer 和 Transporter
	            'options' => [
	                'connect_timeout' => 5.0,
	                'recv_timeout' => 5.0,
	                'settings' => [
	                    // 根据协议不同，区分配置
	                    'open_eof_split' => true,
	                    'package_eof' => "\r\n",
	                    // 'open_length_check' => true,
	                    // 'package_length_type' => 'N',
	                    // 'package_length_offset' => 0,
	                    // 'package_body_offset' => 4,
	                ],
	                // 重试次数，默认值为 2，收包超时不进行重试。暂只支持 JsonRpcPoolTransporter
	                'retry_count' => 2,
	                // 重试间隔，毫秒
	                'retry_interval' => 100,
	                // 当使用 JsonRpcPoolTransporter 时会用到以下配置
	                'pool' => [
	                    'min_connections' => 1,
	                    'max_connections' => 32,
	                    'connect_timeout' => 10.0,
	                    'wait_timeout' => 3.0,
	                    'heartbeat' => -1,
	                    'max_idle_time' => 60.0,
	                ],
	            ],
	        ]
	    ],
	];
	```

- 控制器

	```
    $client = ApplicationContext::getContainer()->get(CalculatorServiceInterface::class);
    return [
        'sum' => $client->add(4,5)
    ];
	```

- 路由

	```
	Router::addRoute(['GET', 'POST', 'HEAD'], '/testService', 'App\Controller\IndexController@testService');
	```

# consul 集群

> [docker 安装集群consul](https://www.jianshu.com/p/b12037fa3249)

- 注意join的ip

```
	// ip
	docker inspect --format '{{ .NetworkSettings.IPAddress }}' join目标consul名称

```

