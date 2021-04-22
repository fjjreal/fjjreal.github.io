---
title: "laravel use"
date: 2021-04-22T05:25:02+08:00
description: "laravel 使用笔记"
tags: [
    "notebook",
]
categories: [
    "Notebook",
]

---

<!--more-->


# 常用插件

- 客户端信息

	- composer require jenssegers/agent

	> [jenssegers/agent](https://github.com/jenssegers/agent)

- sql 日志

	- composer require mnabialek/laravel-sql-logger --dev

	> [mnabialek/laravel-sql-logger](https://github.com/mnabialek/laravel-sql-logger)

- jwt

	- composer require tymon/jwt-auth:dev-develop --prefer-source

	> [tymondesigns/jwt-auth](https://github.com/tymondesigns/jwt-auth)

	- php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"

	- php artisan jwt:secret

	```

		// AppServiceProvide->register();
		// 添加一个自定义jwt auth parser
        $parser = new \Tymon\JWTAuth\Http\Parser\AuthHeaders();
        $parser->setHeaderName('token');
        $parser->setHeaderPrefix('');
        $this->app['tymon.jwt.parser']->addParser([
            $parser,
        ]);
        $this->app->make('auth')->provider('xauth', function($app, $config) {
            return new \Work\Auth\AuthUserProvider($app->get(\Work\Apps\AuthApp::class), $app['hash']);
        });
        // config/jwt.php
	    'defaults' => [
	        'guard' => 'api',
	        'passwords' => 'users',
	    ],
	    'guards' => [
	        'api' => [
	            'driver' => 'jwt',
	            'provider' => 'accounts',
	        ],
	    ],
	    'providers' => [
	        'accounts' => [
	            'driver' => 'xauth',
	        ],
	    ],
	```

- model
	- composer require reliese/laravel --dev	

	> [reliese/laravel](https://github.com/reliese/laravel)

- 图片

	- composer require intervention/image

	> [intervention/image](http://image.intervention.io/getting_started/installation)

- doc

	- composer require zircote/swagger-php

	> [zircote/swagger-php](https://github.com/zircote/swagger-php)

# laravel

- appkey: php artisan key:generate

- seeder:
  ```
    php artisan make:seeder AbcSeeder
    php artisan db:seed --class=AbcSeeder
  ```

- data
	- 数据迁移
	
	  - php artisan migrate
	
	  - php artisan migrate:refresh --seed
	
	- seed: php artisan db:seed

- provider

  * php artisan make:provider ***Provider
    ```php
    <?php
    
    namespace Fjjreal\Weather;
    
    use Illuminate\Support\ServiceProvider;
    
    class WeatherManagerProvider extends ServiceProvider
    {
    
        protected $defer = true; // 延迟加载服务
    
        /**
         * Register services.
         *
         * @return void
         */
        public function register()
        {
            $this->app->singleton('WeatherManager', function ($app) {
                return new WeatherManager($app);
            });
        }
    
        /**
         * Bootstrap services.
         *
         * @return void
         */
        public function boot()
        {
            $this->loadViewsFrom(__DIR__ . '/views', 'Weather'); // 视图目录指定
    
            $this->publishes([
    
                __DIR__.'/views' => base_path('resources/views/vendor/weather'),  // 发布视图目录到resources 下
    
                __DIR__.'/config/weather.php' => config_path('weather.php'), // 发布配置文件到 laravel 的config 下
    
            ]);
        }
    
        public function provides()
        {
            return ['WeatherManager'];
        }
    
    }
    
    ```
  * php artisan vendor:publish --provider="***Provider"

