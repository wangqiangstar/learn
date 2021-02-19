# [iOS/Android 浏览器(h5)及微信中唤起本地APP](https://www.cnblogs.com/shadajin/p/5724117.html)

在移动互联网，链接是比较重要的传播媒质，但很多时候我们又希望用户能够回到APP中，这就要求APP可以通过浏览器或在微信中被方便地唤起。

这是一个既直观又很好的用户体验，但在实现过程中会遇到各种问题：

1. 如何解决未安装APP时的做好引导页
2. 如何在微信中唤醒APP
3. 在iOS9中如何处理universal link被用户误关的情况
4. 如何解决Android各种机型、各种第三方浏览器导致的兼容问题等
5. 在APP未安装情况下，引导用户下载后打开APP后，如何进入之前唤起时指定的页面或内容，即如何实现场景还原
6. 在微信中唤醒APP时，如何进入指定的页面或内容

下面是我一些个人的经验分享。

## 浏览器中打开

### iOS/Android APP配置

这块内容其实比较简单，在网上都有很多资料可供查阅，就不再赘述。

### 原理说明

首先需要说明，不管iOS还是Android，浏览器都不可能预知本地是否安装了某个APP的。或者更严谨地说，我们不能通过浏览器来预知本地是否安装。因为就算浏览器可以读取本地应用的安装列表，但是目前也没任何一家浏览器提供查询的API，所以这条路是走不通的。

本质上浏览器是通过URL scheme打开APP，一个APP可以设置一个或多个打开自己的URL scheme。比如，Twitter就注册自己能被「twitter://」打开。

其实，如果是做APP间相互跳转是比较简单的。iOS就可以使用 UIApplication 的 canOpenUrl 方法来检测URL scheme 是否能打开对应的APP。比如，如果「twitter://」检测能被打开，也就说明本地安装了 Twitter 。再用 UIApplication 的 openURL 方法，就能打开Twitter了。Android 中的做法类似。

### 实现方案

因为iOS9和之前的iOS系统有区别，所以这里我们也要区别对待。

#### iOS7/iOS8

iOS中默认通过Safari打开URL scheme，方法一般如下两种：

1. 直跳方式：点击链接、修改 window.location 等。
2. iframe 方式：在 body 上添加 iframe，设置src属性为跳转的URL scheme。

第一种情况：

```html
<a href="schemeUrl">唤醒你的APP</a>
```

或者

```javascript
window.location.href = schemeUrl;
```

但在第一种情况，如果APP唤醒失败，或者APP未安装的话，很多时候都会跳到错误页，这很影响用户体验，而我们的要求可能是跳转到其他页面或者下载APP。

后一种方法不会引起页面可见的变化（例如页面内容变成一个新页面），不会导致浏览器历史记录的变化，大致实现如下：

```javascript
<a href="APP下载地址">下载或打开APP</a>
<script>
$('a').click(function() {
    var ifr = document.createElement('iframe');
    ifr.src = '自定义 URL scheme';
    ifr.style.display = 'none';
    document.body.appendChild(ifr);
    setTimeout(function(){
        document.body.removeChild(ifr);
    }, 3000);
});
</script>
```

过程是这样：点击 a 标签时，首先会尝试打开URL scheme，如果成功，就唤起APP；如果失败，则跳转到 href 属性，即下载页。

#### Android

但这个方案在很多安卓机型上有问题，为保证可用，改用第一种方案：

```javascript
$('a').click(function() {
    location.href = '自定义 URL scheme';
    t = Date.now();
    setTimeout(function(){
        if (Date.now() - t < 1200) {
            location.href = 'Android 下载地址';
        }
    }, 1000);
    return false;
}
```

理想过程是这样：浏览器尝试打开 URL scheme，在1秒计时后，检查当前时间，如果实际时间已过 1200 毫秒，说明唤起APP 成功（唤起 APP 会让浏览器的定时器变慢）；如果没超过 1200 毫秒，很可能是没有安装应用，就跳到下载地址。

或者换种方式：

```javascript
var ifr = document.createElement('iframe');
ifr.src = 'com.baidu.tieba://';
ifr.style.display = 'none';
document.body.appendChild(ifr);
var openTime = +new Date();
window.setTimeout(function(){
    document.body.removeChild(ifr);
    if( (+new Date()) - openTime > 2500 ){
        window.location = 'http://exam.com/xxxx.apk';
    }
},2000)
```

但原理都是一样，利用setTimeout。但这其实不稳定，因为Android是基于Linux的分时多任务的，setTimeout的基准偏差可能会没那么大。

但如果设置比较小的运行间隔（<30ms），在浏览器或者webview中，应用切换到后台，`setInterval`会被很明显的延迟执行，比如设置一个运行间隔20ms，总计运行100次的定时器，如果页面一直处于前台，则100次跑完，总耗时与 100x20=2000ms不会有太大差异，但页面在后台运行时，此时间会明显超过2000ms。可以利用这一点来实现是否成功打开APP检测及回调。

```javascript
function openApp(openUrl, appUrl, action, callback) {
    //检查app是否打开
    function checkOpen(cb){
        var _clickTime = +(new Date());
        function check(elsTime) {
            if ( elsTime > 3000 || document.hidden || document.webkitHidden) {
                cb(1);
            } else {
                cb(0);
            }
        }
        //启动间隔20ms运行的定时器，并检测累计消耗时间是否超过3000ms，超过则结束
        var _count = 0, intHandle;
        intHandle = setInterval(function(){
            _count++;        
            var elsTime = +(new Date()) - _clickTime;
            if (_count>=100 || elsTime > 3000 ) {
                clearInterval(intHandle);
                check(elsTime);
            }
        }, 20);
    }
    
    //在iframe 中打开APP
    var ifr = document.createElement('iframe');
    ifr.src = openUrl;
    ifr.style.display = 'none';
    if (callback) {
        checkOpen(function(opened){
            callback && callback(opened);
        });
    }
    
    document.body.appendChild(ifr);      
    setTimeout(function() {
        document.body.removeChild(ifr);
    }, 2000);  
}
```

另外，可以通过 `document.hidden` 或 `document.[webkit|moz|ms]Hidden` 来判断页面是否被置入后台（即应用被唤起），或`visibilitychange`事件，但对于Android 4.4版本一下则不支持。

#### iOS9

在 iOS 9 上，iframe 方案变得不可用。
按不能使用之前Android的代码，因为在打开自定义 URL scheme 时，会弹出对话框，询问是否用 xx 应用来打开。往往用户还没来得及点击打开，定时器又触发了，导致跳到 App Store。

可以在尝试打开URL scheme 后，再加一个页面跳转，这样对话框会被覆盖，再刷新页面，就能无需确认唤起APP：

```javascript
$('a').click(function() {
    location.href = '自定义 URL scheme';
    location.href = '下载页';
    location.reload();
}
```

这里，下载页延时 2 秒跳转到 App Store。

APP已安装这是没问题的，但如果APP未安装，跳 App Store 的请求会失败。
这时可以使用两个定时器：

```javascript
$('a').click(function() {
    location.href = '自定义 URL scheme';
    setTimeout(function() {
        location.href = '下载页';
    }, 250);
    setTimeout(function() {
        location.reload();
    }, 1000);
}
```

不过在iOS9中其实是支持universal link的，就是一个http域名形式，在微信中都可以唤起APP。如果未安装的话，可以直接引导用户去APP store下载。

可以参考这篇文章

http://www.magicwindow.cn/doc/#universal-link-info

#### 没有完美的解决方案

主要是在安卓上，总归会有各种兼容问题，知乎的解决办法是，提供两个按钮，一个下载，一个打开APP，让用户自己选。

## 微信中打开

因为微信将唤起本地APP的接口给禁了，所以微信中是不能直接唤起APP的，一般做法是提示用户在浏览器中打开，之后的流程还是我们上面讲的内容。

但是，在iOS9中，这个限制是可以突破的，也就是说可以直接唤起APP。方法就是使用我们上文提到的universal link。

在Android和iOS8及其以下系统中，我们可以利用腾讯的亲儿子：应用宝。简单讲，就是把你的唤起地址配置成你APP的应用宝地址，微信中跳转到这个地址后，如果用户已经安装了APP，则可直接唤起，如果没有安装，则可直接点击下载，如下图示：

![img](https://images2015.cnblogs.com/blog/1001566/201607/1001566-20160731222546169-1267660519.jpg)

但这里有坑需要注意。

对于使用universal link来说，如下图所示用户在微信中打开APP之后，可能不小心点击右上角的链接（比方说点几分享，却不小心点击了"mlinks.cc"），导致跳到外部浏览器中，如下图所示：

![img](https://images2015.cnblogs.com/blog/1001566/201607/1001566-20160731222558450-472991583.jpg)

这时候再在微信中就打不开APP了，因为universal link已被关闭，这是iOS9的机制，没法改变，这时候用户再在微信中打开，就得需要一个中间页来引导用户在外部浏览器中打开APP，如下图所示：

![img](https://images2015.cnblogs.com/blog/1001566/201607/1001566-20160731222613606-875903973.jpg)

另外，在微信中唤醒APP默认只能到达首页，即不能到达指定页面或内容，如果想要做，则需要额外的处理。

## 拿来主义

从以上内容可以总结出：要做一个兼容性很好的方案，就需要考虑各种情况，在不同的情况适配不同的方案，比方说用户是在手机浏览器打开还是微信中打开，或者是在pc中打开，universal link是否被关闭等，这就使代码实现变得复杂，且容易出错，且还有安卓平台机型众多、浏览器众多等导致的兼容问题。

如果觉得实现难度或者成本太高，你可以考虑使用魔窗的mLink。只要你加了魔窗的sdk，就可以通过类似“https://s.mlinks.cc/AA01”的链接，在任何环境下打开你的APP（如果在pc机上打开，浏览器中将会出现APP下载地址的二维码），上面提到的问题都不复存在，并且魔窗已经兼容超过600台以上安卓机型的第三方主流浏览器。而且关键的是，不管是在手机浏览器中，还是在微信中打开，你可以指定唤起APP后直达APP中的某个页面或内容（某个促销商品等），就算用户没安装APP，点击下载安装之后，再打开，还是跳转到指定的页面，这就是场景还原，或者叫做Deffered Deep Linking。

欢迎访问魔窗官网：http://www.magicwindow.cn/

利益相关：魔窗员工。