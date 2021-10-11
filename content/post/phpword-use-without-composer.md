---
title: "phpword使用"
date: 2021-10-11T10:10:02+08:00
draft: false

tags: [
	"phpword",
    "php"
]

categories: [
    "notebook",
]

---

- php5.6
- yii1.1
- phpword master v0.18 TemplateProcessor中无setLinkValue方法

<!--more-->

# 文件 protected/extensions/phpword

- src/Phpword中的文件放入vendor

    ```
    ├── vendor
    │   ├── Autoloader.php // 后面加入
    │   ├── Collection
    │   ├── ComplexType
    │   ├── Element
    │   ├── Escaper
    │   ├── Exception
    │   ├── IOFactory.php
    │   ├── Media.php
    │   ├── Metadata
    │   ├── PhpWord.php
    │   ├── Reader
    │   ├── resources
    │   ├── Settings.php
    │   ├── Shared
    │   ├── SimpleType
    │   ├── Style
    │   ├── Style.php
    │   ├── Template.php
    │   ├── TemplateProcessor.php
    │   └── Writer
    └── XPHPWord.php // 后面加入
    ```

# 引入

- [vendor/Autoloader.php](https://gitee.com/nbnat/phpoffice/blob/master/phpoffice/common/Autoloader.php)
    ```php

    namespace PhpOffice\PhpWord;

    /**
     * Autoloader
     */
    class Autoloader
    {
        /** @const string */
        const NAMESPACE_PREFIX = 'PhpOffice\\PhpWord\\';

        /**
         * Register
         *
         * @return void
         */
        public static function register()
        {
            spl_autoload_register(array(new self, 'autoload'));
        }

        /**
         * Autoload
         *
         * @param string $class
         */
        public static function autoload($class)
        {
            $prefixLength = strlen(self::NAMESPACE_PREFIX);
            if (0 === strncmp(self::NAMESPACE_PREFIX, $class, $prefixLength)) {
                $file = str_replace('\\', DIRECTORY_SEPARATOR, substr($class, $prefixLength));
                $file = realpath(__DIR__ . (empty($file) ? '' : DIRECTORY_SEPARATOR) . $file . '.php');
                if (file_exists($file)) {
                    /** @noinspection PhpIncludeInspection Dynamic includes */
                    require_once $file;
                }
            }
        }
    }

    ```

- [XPHPWord](https://github.com/socialmind/yii-phpword/blob/master/XPHPWord.php) 引入phpword
    ```php

    spl_autoload_register(function ($class) {
        $class = ltrim($class, '\\');
        $prefix = 'PhpOffice\PhpWord';
        if (strpos($class, $prefix) === 0) {
            $class = str_replace('\\', DIRECTORY_SEPARATOR, $class);
            $class = implode(DIRECTORY_SEPARATOR, array('phpword','PhpWord')) . substr($class, strlen($prefix));
            $file = __DIR__ . DIRECTORY_SEPARATOR . $class . '.php';
            if (file_exists($file)) {
                require_once $file;
            }
        }
    });

    require_once __DIR__ . "/vendor/Autoloader.php";
    \PhpOffice\PhpWord\Autoloader::register();

    /**
     * Wrapper for the PHPWord library.
     * @see README.md
     */
    class XPHPWord extends CComponent
    {
        private static $_isInitialized = false;

        /**
         * Register autoloader.
         */
        public static function init()
        {
            if (!self::$_isInitialized) {
                spl_autoload_unregister(array('YiiBase', 'autoload'));
                require(dirname(__FILE__) . DIRECTORY_SEPARATOR . 'vendor' . DIRECTORY_SEPARATOR . 'PhpWord.php');
                spl_autoload_register(array('YiiBase', 'autoload'));
                self::$_isInitialized = true;
            }
        }

        /**
         * Returns new PHPWord object. Automatically registers autoloader.
         * @return PhpWord
         */
        public static function createPHPWord()
        {
            self::init();
            return new PhpWord();
        }
    }
    ```

# 使用

- yii
    ```php
        Yii::import('application.extensions.phpword.XPHPWord');
        XPHPWord::init();

        $tpl = new PhpOffice\PhpWord\TemplateProcessor($tpl_path);
        // code
    ```

- 图表

    ```php
    //曲线图
    $line_chart = new PhpOffice\PhpWord\Element\Chart(
                    'line',
                    $categories, // X轴值
                    $series1,    // Y轴值1
                    array(
                        'width'  => \PhpOffice\PhpWord\Shared\Converter::cmToEmu(15),
                        'height' => \PhpOffice\PhpWord\Shared\Converter::cmToEmu(12),
                        'categoryAxisTitle' => '日期',       // X轴 - 备注
                        'valueAxisTitle' => '平台数|次数', // Y轴 - 备注
                        'showAxisLabels' => true,
                        'showLegend' => true,
                        '3d' => false
                    ),'平台数'    // Y轴值1 - 名称
                );
    $line_chart->getStyle()->setLegendPosition('b');
    $line_chart->getStyle()->setDataLabelOptions(array(
        'showCatName'      => false, // category name
    ));
    $line_chart->addSeries(
        $categories, // X轴值
        $series2,    // Y轴值2
        '次数'        // Y轴值2 - 名称
    );
    $tpl->setChart('aa_chart',$line_chart);
    ```

- [超链](https://github.com/PHPOffice/PHPWord/pull/1887) develop分支应该已有

    ```php
    // 单个
    $link = new PhpOffice\PhpWord\Element\Link($url,$name,array('color'=>self::BLUE),null,false);
    $tpl->setLinkValue('${need_replace_link_str}',$link);
    // 动态个数做法
    // 1.先添加等待替换的字符
    // 2.保存到对象中 ['等待替换的字符'=>['name'=>'','url'=>''],...]
    // 3.文件保存前，循环 setLinkValue 等待替换的字符
    ```

- * 例子:

![phpword表格中插入超链](/images/phpword_table_temp.png)

```php

$links = [];
$cols = ['area','link_languages'];
$rows = [
    [
        'area' => '1',
        'link_languages' => [
            'en'=>['name'=>'英语','url'=>'https://baidu.com/s?wd=en'],
            'zh_CN'=>['name'=>'中文','url'=>'https://baidu.com/s?wd=zh_CN'],
        ],
        'link_a' => 'https://baidu.com/s?wd=a',
        'name_link_a' => '超链A',
    ],
    [
        'area' => '2',
        'link_languages' => [
            'en'=>['name'=>'英语','url'=>'https://baidu.com/s?wd=en'],
            'zh_CN'=>['name'=>'中文','url'=>'https://baidu.com/s?wd=zh_CN'],
        ],
        'link_a' => 'https://baidu.com/s?wd=b',
        'name_link_a' => '超链B',
    ],
];
$table_name = "aa";
$table = new PhpOffice\PhpWord\Element\Table($styles);
//header
//content
foreach ($rows as $row){
    $table->addRow();
    foreach ($cols as $col){
        if(substr($col,0,4) == "link"){
            $cell = $table->addCell()->addTextRun();
            // 处理链接
            if(is_array($row[$col])){
                foreach($row[$col] as $index=>$lk){
                    $t = '${'.$table_name .'_'. $col . '_' . $index . '}';
                    // 1.
                    $cell->addText($t.',');
                    // 2.
                    $links[$t] = [
                        'name' => $lk['name'],
                        'url' => $lk['url']
                    ];
                }
            }else{
                $t = '${'.$table_name .'_'. $col . '}';
                // 1.
                $cell->addText($t);
                // 2.
                $links[$t] = [
                    'name' => $row['name_'.$col],
                    'url' => $row[$col]
                ];
            }
        }else{
            // 处理普通文本
        }
    }
}
// 先把table添加上去
$tpl->setComplexBlock('aa_table',$table);
// 3.
foreach($links as $t=>$lk){
    $link = new PhpOffice\PhpWord\Element\Link($lk['url'],$lk['name'],array('color'=>self::BLUE),null,false);
    $tpl->setLinkValue($t,$link);
}

```


# PHPWord文档

- [官方文档](https://phpword.readthedocs.io/)
- [中文](https://phpword-zh.readthedocs.io/zh_CN/latest/index.html)
- [PHPWord中文手册整理](https://segmentfault.com/a/1190000019479817?utm_source=tag-newest)
- [使用PHPWord对Word文件做模板替换](https://segmentfault.com/a/1190000007847203)


