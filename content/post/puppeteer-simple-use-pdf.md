---
title: "Puppeteer Simple Use Pdf"
date: 2021-03-09T15:52:54+08:00
draft: false

cover: /images/puppeteer-pdf-use.png

tags: [
	"puppeteer"
]

categories: [
    "soft",
]

---


<!--more-->

## 项目初始化

```
npm init
npm i puppeteer
```

## [puppeteer](https://github.com/puppeteer/puppeteer/blob/v8.0.0/docs/api.md) PDF官方示例（指令参数改动）[pdf参数](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v8.0.0&show=api-pagepdfoptions)

```

var url  = process.argv[2];
var path = process.argv[3];

if(!url || !path) return;

var format = process.argv[4] ? process.argv[4]:'A4';

const puppeteer = require('puppeteer');

(async () => {
	const browser = await puppeteer.launch();
	const page = await browser.newPage();
	await page.goto(url, {
		waitUntil: 'networkidle2',
	});
	await page.pdf({
		path: path,
		format: format,
	});

  	await browser.close();
})();

// node index.js https://www.oschina.net/ C:/works/aa.pdf

```

## ts的使用参考[这里](https://www.jianshu.com/p/97eeffa3bf3a)

```

import * as puppeteer from 'puppeteer'
import * as chalk from 'chalk'

async function main(url:string,path:string,format:any) {
    const browser = await puppeteer.launch();
    try {
        const page = await browser.newPage();
        await page.goto(url, {
        waitUntil: 'networkidle2',
        });

        await page.pdf({
            path: path,
            format: format
            // headerTemplate // 页眉的html模板
            // footerTemplate // 页脚的html模板
        });
        await browser.close();
    } catch (error) {
        console.log(error)
        console.log(chalk.red('服务意外终止'))
        await browser.close();
    } finally {
        process.exit(0)
    }
    
}

var url:string  = process.argv[2];
var path:string = process.argv[3];
if(url && path){
    var format:string = process.argv[4] ? process.argv[4]:'A4';
    main(url,path,format)
}

// tsc use.js
// node use.js https://www.oschina.net/ C:/works/aa.pdf

```
## 膜拜大佬

- [Node：使用Puppeteer完成一次复杂的爬虫](https://www.jianshu.com/p/97eeffa3bf3a)