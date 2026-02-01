---
title: 利用阿里云免费部署openai的Chatgpt国内直接用
date: 2023-04-15 11:34:55
tags:
---
# 背景
![ChatGPT访问限制](/images/chatgpt-aliyun/img1.png)
	国内无法直接访问ChatGPT，一访问就显示 code 1020。而且最近OpenAI查的比较严格，开始大规模对亚洲地区开始封号，对于经常乱跳IP的、同一个ip一堆账号的、之前淘宝机刷账号的，账号被封的可能性极大。  
	那么有没有符合openai规定，个人独享且==免费==，可以在国内也稳定使用ChatGPT呢？有！本文教你如何使用阿里云，搭建我们自己的服务器。


# openai账号

首先你要有一个openai账号，如果没有对应的账号下面的内容就不用看了。  
首先我们要记录下来账号的api key，首先打开网页
[openai platform](https://platform.openai.com/account/api-keys)，点击右上角的个人，点击"View API keys",点击"Create new secret key"，记录下来Secret key，留着后续使用。**注意这个key千万不要给别人**
![OpenAI API Keys](/images/chatgpt-aliyun/img2.png)

# 阿里云云函数
前往[阿里云官网](https://www.aliyun.com/product/fc?source=5176.11533457&userCode=s7xej6va),选择云函数，进入管理控制台。每一个用户均有3个月的免费时长。
![阿里云云函数入口](/images/chatgpt-aliyun/img3.png)
这里首先要选择机房，选择==美国硅谷==，这个是符合openai运营区域的，然后点击==服务及函数==，点击创建服务。
![创建服务](/images/chatgpt-aliyun/img4.png)
此时随便写一个名字，例如chatgpt（这个不重要）。再点击这个名字，进入到里面。选择函数管理，==创建函数==。
![函数管理](/images/chatgpt-aliyun/img5.png)

![创建函数配置](/images/chatgpt-aliyun/img6.png)
在这里按如下要求填写，没有提到的就**保持默认**：

请选择一种创建函数的方式：使用自定义运行时创建
函数名称：随意填写
请求处理程序类型：处理HTTP请求
运行环境：Node.js 16
代码上传方式: 通过zip包上传代码， 代码下载地址[openai-proxy](https://github.com/tianmu/openai-proxy/releases/download/1.0/openai-proxy.zip)，然后托上去即可。
启动命令：node app.js
监听端口： 9000
规格方案：选择最小的就够用了。vCPU 规格 0.05 内存规格 128
实例并发度：2
执行超时时间：900 （一般一句话不会超过900秒）
![函数配置详情](/images/chatgpt-aliyun/img7.png)
现在你的openai成功创建好了！！！
点击触发器管理，就可以看到你的url，配置到各种openai工具里就能用咯。
![触发器URL](/images/chatgpt-aliyun/img8.png)
# gpt客户端
此时可以使用各种支持自定义网址的客户端，直接在本地畅玩。
例如：
BetterChatGPT：[https://github.com/ztjhz/BetterChatGPT/releases/tag/v1.0.2](https://github.com/ztjhz/BetterChatGPT/releases/tag/v1.0.2)
![BetterChatGPT配置](/images/chatgpt-aliyun/img9.png)
注意：https://xxx.fcapp.run/v1/chat/completions 是我们刚才在阿里云函数里的公网地址，不同的软件可能需要配置为https://xxx.fcapp.run或https://xxx.fcapp.run/v1/

# 开发者提醒
官网文档：[https://platform.openai.com/docs/api-reference](https://platform.openai.com/docs/api-reference)
使用官网node开发：
```js
const openai = require("openai");
const Configuration = openai.Configuration;
const OpenAIApi = openai.OpenAIApi;

const configuration = new Configuration({
    apiKey: process.env.OPENAI_API_KEY,
    basePath: "https://xxx.fcapp.run/v1",
});

const openaiApi = new OpenAIApi(configuration);
const response = openaiApi.listModels();
```
使用官网python开发：
```python
import os
import openai

openai.api_key = "OPENAI_API_KEY"
# python开发必须有v1
openai.api_base = "https://xxx.fcapp.run/v1"
openai.Model.list()
```

`
版权声明：本文为CSDN博主「fsafs168」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/fsafs168/article/details/130166098
`