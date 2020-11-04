## 如何设计

### 开发文档目录

### 设计模式

> 常见的JS设计模式主要有以下几种

- 单例模式：一个类只返回一个实例，一旦创建再次调用就直接返回（如第三方库jQuery，lodash，moment等）
- 构造函数模式
- 混合模式：原型模式+构造函数模式
- 工厂模式
- 发布订阅模式

> 由于抽象工厂模式适用系统中有多个产品类族的前提，而每次只是用其中某一产品类族的情况，比较适合通过使用抽象工厂模式来封装KSDK

抽象工厂设计模不直接生成实例，而是用于对产品类簇的创建。SDK使用不同的第三方JS-SDK进行注册的，如：钉钉，企业微信，云之家等。那么这三类工具就是对应的类簇。在抽象工厂中，它其实是由几种子类型组成的，当创建SDK类实例时候，它先判断一下应该用哪个子类，然后创建该子类的实例返回给你

```
//工厂设计模式

 function SDKFactory(type) {
    if(this instanceof SDKFactory) {
      return new this[type]();
    } else {
      return new SDKFactory(type);
    }
  }
  SDKFactory.prototype = {
    YUNZHIJIA: function() {
      this.jsApi = 'https://static.yunzhijia.com/public/js/qing/latest/qing.js'
      this.init = function () {
        qing.config({});
      };
      //
      this.getInfo = function (fn) {
        qing.call('getPersonInfo', {
          success: function (res) {
            fn();
          },
        });
      };
      this.getVersion = function () {
        return qing.version;
      };
    },
     DINGDING: function() {
      this.jsApi = 'https://g.alicdn.com/dingding/dingtalk-jsapi/2.7.13/dingtalk.open.js'
      this.init = function (fn) {
        dd.ready(function () {
          fn();
        });
      };
      this.getInfo = function (fn) {
  
      };
      this.getVersion = function () {
        return dd.version;
      };
    },
     WEIXINWORKSDK: function() {
    this.jsApi = 'http://res.wx.qq.com/open/js/jweixin-1.2.0.js'
    this.init = function (fn) {
      wx.config({beta: true, debug: true});
      wx.ready(function () {
        fn();
      });
    };
    this.getVersion = function () {
    
    };
  }
 }

```

> KSDK方法使用前，需要先成功加载完相应的第三方js资源，加载方式使用异步加载

这里考虑到当你使用异步加载的时候，将会出现，页面中的函数无法正常调用SDK方法的情况，也就是当调用发生在脚本加载之前执行了，那如何包装让SDK正常执行？这里聊一下script的同步与异步的关系。

- 同步模式： `script`标签不带defer或async属性，脚本获取与执行都是同步进行，页面会被阻塞，浏览器都会按照``元素在页面中出现的先后顺序对他们依次进行解析加载
- 异步模式：``标签打开defer或async属性，脚本就会异步加载。渲染引擎执行后就会开始下载外部脚本，但不会等它下载和执行完成，而是直接执行后面的命令。
- defer与async的区别 : defer 是“渲染完再执行”，async 是“下载完就执行" async和defer不同之处是async加载完成后会自动执行脚本，defer加载完成后需要等待页面也加载完成才会执行代码

```
 function initScript(src, params = {}, fn) {
    const script = document.createElement('script');
    script.type = 'text/javascript';
    script.src = src;
    if (script.addEventListener) {
      script.addEventListener('load', function () {
        fn();
      }, false);
    }
    document.head.appendChild(script);
  }

```

> KSDK 构造函数，支持传入类型后识别应该用哪个子类，然后异步加载第三方SDK的JS文件，加载成功最后通过原型链继承，完成初始化。

```
 (function () {
 
  'use strict';
  //获取手动配置类型
const scriptDOM = document.getElementsByTagName("script"), [scriptDetail] = scriptDOM, URL = scriptDetail.src,
  type = URL.match(new RegExp("(?:\\?|&)" + 'type' + "=(.*?)(?=&|$)"))[1], domain = document.domain;

//集成工具对象
function KSDK() {
  this.domain = domain;
  this.ready = function (fn) {
    //动态加载
    const that = this;
    let sdkFac;
    try {
      sdkFac = SDKFactory(type || this.domain);
    } catch (e) {
      console.log('不支持该类型的SDK')
    }
    if (sdkFac) {
      initScript(sdkFac.jsApi, {
        async: true,
        defer: true,
      }, function () {
        that.__proto__ = sdkFac;
        that.init(function () {
          fn();
        });
      });
    }
  };
}
    
   window.ksdk = new KSDK();
   
 })(window);


```

### 如何使用

引入SDK

```
<script src="kdsk.min.js?type=DINGDING"></script>
<script type="text/javascript">
  window.ksdk.ready(function () {
    window.ksdk.getVersion(function () {

    })
  })
</script>

```

### 版本管理

> 通常情况下，我们会有不同方式来声明SDK的版本，这取决于具体针对的业务和设计。常见的版本管理有以下三种：

- 使用查询字符串路径: `http://xxx.com/sdk.js?v=1.0.0`
- 使用文件夹命名: `http://xxx.com/v1.0.0/sdk.js`
- 使用主机名或者子域名 `http://v1.xxx.com/sdk.js`

该项目使用的是使用文件夹命名的方式，结合aliyun对象存储服务OSS（具备CDN加速）

## 如何打包

> 打包是sdk上线必不可少的流程，项目用了Rollup作为工具打包，Rollup 将所有资源放在同一个地方，一次性加载，利用tree-shake特性来剔除项目中未使用的代码，减少冗余，相比其他JavaScript打包工具，Rollup总能打出更小，更快的包 官方链接：[www.rollupjs.com](https://www.rollupjs.com)

### Rollup

rollup比较适合打包js的sdk或者封装的框架,对比大家熟悉的webpack，比较适合打包一些应用，例如SPA或者同构项目

###  Rollup 配置

- 写一个 rollup.config.js

```
import resolve from 'rollup-plugin-node-resolve';
import json from 'rollup-plugin-json';
import commonjs from "rollup-plugin-commonjs";
import babel from 'rollup-plugin-babel';

export default {
  input: 'src/index.js',
  format: 'iife',
  output: {
    file: 'dist/kdsk.min.js',
    format: 'iife'
  },
  plugins: [
    resolve(), //rollup-plugin-node-resolve 插件可以告诉 Rollup 如何查找外部模块
    json(),
    commonjs() //插件就是用来将 CommonJS 转换成 ES2015 模块的。
    babel({
      exclude: 'node_modules/**',
    }), //需要创建 .babelrc文件编写规则
  ]
}

```

打包后的文件支持的运行环境（format）

- amd – 异步模块定义，用于像RequireJS这样的模块加载器
- cjs – CommonJS，适用于 Node 和 Browserify/Webpack
- iife – 一个自动执行的功能，适合作为``标签
- umd – 通用模块定义，以amd，cjs 和 iife 为一体

这里目前只支持script引入，采用了 iife （即立即执行函数表达式模式）格式打包 ,这个模式下打包会在函数作用域添加闭包，避免变量污染

```
(function () {
  
}());

```

### 运行

```
rollup -c
```















