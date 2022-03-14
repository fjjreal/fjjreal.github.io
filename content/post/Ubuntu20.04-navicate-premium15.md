---
title: "ubuntu navicate 安装"
date: 2021-09-23T10:10:02+08:00
draft: false

tags: [
	"navicate"
]

categories: [
    "soft",
]

---

<!--more-->


# 软件

- sudo apt-get install libcapstone-dev

- sudo apt-get install cmake

```
sudo apt-get install build-essential
sudo apt-get install gdb
```

- sudo apt-get install rapidjson-dev

```
sudo apt-get install openssl
sudo apt-get install libssl-dev 
```

- git clone https://github.com/keystone-engine/keystone.git

- cd keystone & mkdir build & cd build

- ../make-share.sh

- sudo make install

- sudo ldconfig

# 挂载解压image [下载](https://navicat.com.cn/download/navicat-premium)

- mkdir your-path/navicate15 

- sudo mount -o loop navicat15-premium-cs.AppImage your-path/navicate15

- mkdir your-path/navicate15-patcher

- cp -r your-path/navicate15/* your-path/navicate15-patcher

- sudo umount your-path/navicate15

- sudo rm -rf your-path/navicate15

# navicate keygen tools

- git clone git://github.com/HeQuanX/navicat-keygen-tools.git
```
// linux 直接下一步make all
// mac | windows 切换分支
```

- cd navicat-keygen-tools & make all
```
// navicat-keygen-tools/bin 下生成 navicat-keygen  navicat-patcher 
// navicat-patcher // 生成 RegPrivateKey.pem 用
// navicat-keygen  // 生成 序列号&激活码 用
```

- navicat-keygen-tools/bin/navicat-patcher your-path/navicate15-patcher
```
// 会在navicat-keygen-tools/bin生成RegPrivateKey.pem
// 注意：
// your-path/navicate15-patcher
// libcc.so 找不到等问题
// your-path/navicate15-patcher目录下有usr文件夹 icon desktop等
```

# 打包文件

- wget 'https://github.com/AppImage/AppImageKit/releases/download/continuous/appimagetool-x86_64.AppImage'

- chmod +x appimagetool-x86_64.AppImage

- ./appimagetool-x86_64.AppImage your-path/navicate15-patcher your-path/navicate-patched.AppImage
```
// 生成换过公钥的AppImage
```

- chmod +x your-path/navicate-patched.AppImage

# 激活

- ./your-path/navicate-patched.AppImage

- 点注册 & 命令行： sudo ifconfig 你的网卡 down

- navicat-keygen-tools/navicat-keygen --text navicat-keygen-tools/RegPrivateKey.pem
```
// 1.选 premium 
// 2.选 simple chinese
// 3.版本填 15,名称组织名称随便填 (填完Enter后,别直接按两下Enter,不要关闭命令行AA,需要填入请求码的)
// 4.0有一个 16位秘钥
// 4.1填入软件注册界面 永久许可证 & 点激活,提示激活服务器找不到,点手动激活
// 4.2复制 手动激活 请求码 黏贴到命令行AA,按两下Enter,出现Activation Code
// 4.3复制base64的激活码 黏贴到注册界面,点击激活
```

