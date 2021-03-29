---
title: "notebook"
date: 2020-03-19T09:25:02+08:00
description: "笔记"
tags: [
    "notebook",
]
categories: [
    "Notebook",
]

---

<!--more-->

# docker 

- 镜像目录 

# git

- git config --list

- 清除缓存的用户名和密码：git credential-manager uninstall

# laravel

- appkey: php artisan key:generate

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

- data
	- 数据迁移
	
	  - php artisan migrate
	
	  - php artisan migrate:refresh --seed
	
	- seed: php artisan db:seed