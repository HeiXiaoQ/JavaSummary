# ElementUI的基本使用

## 1、基本介绍

ElementUI的由饿了么团队基于Vue框架进行开发的，它是一个后台管理系统的模板。
[ElementUI的访问路径](https://element.eleme.cn/#/zh-CN)

## 2、开始使用

- 首先在官网中将elementui下载好
- 由于该框架是基于Vue进行开发的，所以使用该框架时，代码分为三个部分（css、JS（Vue）、html（模型））

**导入资源：**

``` html
<script src="js/vue.js"></script>
<script src="element-ui/lib/index.js"></script>
<link rel="stylesheet" href="element-ui/lib/theme-chalk/index.css">
```

**使用组件：**
**html的代码：**
![html部分的代码](https://shopping-mlk.oss-cn-beijing.aliyuncs.com/JavaNote/ElementUI/html%E9%83%A8%E5%88%86%E4%BB%A3%E7%A0%81.jpg)

**css的代码：**
![css部分的代码](https://shopping-mlk.oss-cn-beijing.aliyuncs.com/JavaNote/ElementUI/css%E9%83%A8%E5%88%86%E4%BB%A3%E7%A0%81.jpg)

**js的代码：**
![js的代码](https://shopping-mlk.oss-cn-beijing.aliyuncs.com/JavaNote/ElementUI/js%E9%83%A8%E5%88%86%E4%BB%A3%E7%A0%81.jpg)

**三个部分的代码分别放入html文件的相应位置。使用该框架的主要难度点是Vue的数据双向绑定，以及编写相关的方法进行事件监听。**
