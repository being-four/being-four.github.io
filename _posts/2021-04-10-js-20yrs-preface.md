---
layout:       post
title:        "《JavaScript 二十年》推荐语"
author:       "Hux"
header-style: text
catalog:      true
tags:
    - Spider
    - JavaScript
---

###### 服务器渲染（SSR）&&客户端渲染（CSR）
服务器渲染（Server-Side Rendering）
> **服务器渲染是指在服务器端生成完整的HTML页面**，然后将该页面发送给客户端浏览器进行展示。在这种模式下，服务器负责生成页面的内容和结构，包括数据的获取和处理，然后将最终的HTML页面返回给浏览器。这样，浏览器直接接收到可供显示的HTML内容，搜索引擎也能够更好地理解页面内容，有助于SEO（搜索引擎优化）。

客户端渲染（Client-Side Rendering)
>  **客户端渲染是指在客户端浏览器中使用JavaScript动态生成页面内容。**初始加载时，浏览器会加载一个基础的HTML骨架和JavaScript文件，然后通过JavaScript异步请求数据，根据数据生成页面内容，并将其插入到DOM中进行渲染。这种模式下，页面交互性较高，但初始加载可能较慢，不利于搜索引擎爬取。

如果是服务器渲染，可直接通过网页爬取匹配出结果。扇贝是通过客户端渲染，以生词本页面为例子，抓取到具体请求接口如下：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1699335/1691565685880-b69fef67-0e7a-4332-89de-ec87dcf30687.png#averageHue=%23eca755&clientId=u0f181b74-eea2-4&from=paste&height=308&id=ube76e58f&originHeight=1462&originWidth=2856&originalType=binary&ratio=2&rotation=0&showTitle=false&size=405072&status=done&style=none&taskId=uc2c37151-824c-46e5-bee3-b46c9537521&title=&width=601)
之前在爬取3d网站的时候，到这就结束了，通过接口中写死的游客token就可以获取想要的数据了，但是扇贝的接口对返回结果做了加密。
###### Chrome DevTool- initiator工具
功能：**它用于显示哪段 JavaScript 代码、事件或资源加载触发了网络请求。**对于前端开发同学来说，通过了解请求的触发器，你可以更好地分析和优化网页性能，找出潜在的性能瓶颈，以及改进资源加载策略。这在调试和优化网页时非常有用。而我就用这个来分析一下整个调用栈，从而找出浏览器运行解码的代码。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1699335/1691566440462-6d664903-5e1a-448f-ac16-08d4a87e9461.png#averageHue=%23d86127&clientId=u0f181b74-eea2-4&from=paste&height=355&id=u30fc68c2&originHeight=1668&originWidth=2842&originalType=binary&ratio=2&rotation=0&showTitle=false&size=511105&status=done&style=none&taskId=u620c26cf-5425-4dc2-96fb-b304e456f73&title=&width=605)
**好小子，居然连文件名都加密了一下，防止通过文件名猜出。我靠着猜蒙对了在main....中。并按照接口路由搜索，找到了解码的地方。**
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1699335/1691566543187-36747282-f2c7-486b-adf1-b7f0c340647c.png#averageHue=%23fefcfb&clientId=u0f181b74-eea2-4&from=paste&height=353&id=u0cf2d880&originHeight=1074&originWidth=1892&originalType=binary&ratio=2&rotation=0&showTitle=false&size=241584&status=done&style=none&taskId=ub0fdfc40-24aa-44bb-8b6b-7d4b302273a&title=&width=621)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1699335/1691566595292-5a7bab8c-e834-4f98-9ad3-e601b401e6bd.png#averageHue=%23fefbfb&clientId=u0f181b74-eea2-4&from=paste&height=287&id=ua3e63997&originHeight=718&originWidth=1542&originalType=binary&ratio=2&rotation=0&showTitle=false&size=184695&status=done&style=none&taskId=ud7b312dd-3203-47ad-8864-5a837e4f8c5&title=&width=617)
```
Na = function(e) {
            return JSON.parse(window.bays4.d(e))
        }
```
###### 运行解码函数
在chrome中运行bays4.d(接口数据)，拿到结果啦：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1699335/1691566948246-40d05044-bfa2-4560-9b6a-2eeab4c566eb.png#averageHue=%23fef9e1&clientId=u0f181b74-eea2-4&from=paste&height=192&id=uc55878b6&originHeight=442&originWidth=1426&originalType=binary&ratio=2&rotation=0&showTitle=false&size=195687&status=done&style=none&taskId=u53faa2d0-a54f-4d77-98f6-7b53b5b1052&title=&width=618)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1699335/1691566813842-3d47426e-403e-4b74-8d5a-6a682c6c789d.png#averageHue=%23f8e0df&clientId=u0f181b74-eea2-4&from=paste&height=316&id=ua46655eb&originHeight=1188&originWidth=2318&originalType=binary&ratio=2&rotation=0&showTitle=false&size=1036835&status=done&style=none&taskId=uf963e615-2bb9-4148-95ce-a68bdaca74b&title=&width=617)
###### Chrome Console获取解密源代码
打开 Console，输入 bays.d，可以看到这个函数的定义，单击后能跳转到源码：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1699335/1691567572617-92a7faf8-315c-4d6e-9948-fdf62d9ee58a.png#averageHue=%23fefefe&clientId=u499c888c-4e96-4&from=paste&height=42&id=uaf3a1438&originHeight=138&originWidth=2102&originalType=binary&ratio=2&rotation=0&showTitle=false&size=37360&status=done&style=none&taskId=udea29b04-5a69-4ac8-a975-9e0ed869afb&title=&width=636)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1699335/1691567665897-fc656028-76ae-4dfc-a348-040c678ff5b8.png#averageHue=%23fefcfc&clientId=u499c888c-4e96-4&from=paste&height=310&id=u8a73b384&originHeight=1098&originWidth=2276&originalType=binary&ratio=2&rotation=0&showTitle=false&size=278134&status=done&style=none&taskId=uf10dce03-293e-4e57-9c78-9c4ca1264f9&title=&width=643)
好啦，接下来就把这块的源代码拼凑起来，就完成啦，具体的解码/加密思路没有研究过，可以参考：[github他人拼凑的结果](https://gist.github.com/yihong0618/7db0ad82d97c642eb5537d026431739e)；

