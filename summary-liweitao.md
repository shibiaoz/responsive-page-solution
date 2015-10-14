# 移动端适配方案总结
> 移动端开发总是存在很多的挑战，兼容性、性能、更高要求的用户体验，都是前端攻城狮需要一一攻破的壁垒。而大屏、高分辨率的手机的出现，又为攻城狮们立下了一个新的制高点，如何适配不同尺寸，不同分辨率的机器，如何在高清分辨率的屏幕上为用户呈现更精致的作品，是前端攻城狮新的挑战。


### 利用zoom适配

在进行拍拍小店的开发中我们发现页面在高清分辨率的手机如iPhone6 Plus上表现不尽如人意，有些元素还有文案在这些手机上显示得太小，视觉体验非常不好。咋办类？为了快速解决适配更高分辨率手机的问题，我们天马行空地想到，直接将页面按照一定比例缩放不就好啦，这样在更高分辨率的手机上将页面适当放大，不就能达到适配高清分辨率手机的目的。

适配的代码如下

```javascript
(function (global) {
  var width = global.innerWidth;
  var ratio = (width / 320).toFixed(2);
  ratio = Math.min(ratio, 2);
  document.documentElement.style.zoom = ratio;
  global.zoom = function (ratio) {
    if (ratio === undefined) {
      return document.documentElement.style.zoom;
    } else {
      document.documentElement.style.zoom = ratio;
    }
  }
})(window);
```

原理就是以宽320这一值作为基准，读取页面大小，计算出比例然后设置给根元素的``zoom``属性，以达到缩放的目的。

这样可以快速暴力地将整个页面进行缩放，并且能适配大部分手机屏，在iPhone6 Plus上看起来效果棒棒哒，问题似乎解决了呢！

但是细细想下，这样做对于一些简单的活动页面来说可以满足需求，而对于一个布局比较复杂的网站来说，这样做非常不合适，因为这样将页面所有元素简单缩放，会改变元素原来的**top/height**等值，让已有组件如**lazyload**失效而需要重新计算，而且有些使用背景图的元素可能显示会有问题，可能需要重新切图；同时对于使用圆角边框元素，使用zoom变换后会导致边框的宽度不一致，影响视觉体验，虽然将``border``换成``box-shadow``后能解决这个问题，但是大量地使用``box-shadow``又会是另一个灾难；同时而在一些Android机器上会出现兼容性问题，比如元素被过度放大，这些都需要花时间去解决。

所以，使用``zoom``去暴力缩放是极力不推荐的一种做法。

### 通过动态设置viewport来缩放

看来**zoom**是一次失败的尝试啦，但是缩放页面是达到适配效果的最佳捷径，我们不想放弃，所以我们探索了另一种缩放方式，通过修改viewport来进行缩放。

Viewport是指浏览器用于展示内容的区域，但是在移动端viewport的大小不一定和浏览器的可视区域一样大，因为移动设备相对于PC来说太小，为了能显示那些为PC设计的网站，一般移动设备的浏览器通常会将自己默认的viewport设置为980px或者其他。

我们可以通过meta viewport标签来设置viewport，通过标签的**width**来设置viewport的大小，通过**initial-scale**、**minimum-scale**、**maximum-scale**来设置页面缩放，所以通过要达到我们的目的依然十分简单。

祭出神器：

```
<meta name="viewport" content="width=320,maximum-scale=1.3,user-scalable=no">
```
我们目前的设计稿是都是基于320的，所以我们将width设为320，然后在这个基础上进行一定比例的缩放，这个比例是**当前设备宽度/320**计算出来动态修改的。利用这一方式可以非常快捷实现适配的目的，简单粗暴、非常高效。

但是，看起来美好的东西总是存在但是，经测试在某些Android手机上会存在页面元素模糊的问题，这又是一悲剧，不过如果对Android手机的适配要求不高的话，这一方式还是可以考虑的，暂时收为备胎方案吧。

而在这时，搜遍网络资料，发现了网易前端的一个[解决方案](https://github.com/unbug/generator-webappstarter/blob/master/app/templates/app/src/util/MetaHandler.js)，通过设置viewport，将width直接设置为UE图的宽度，然后按UE图的尺寸去编写页面，比如UE图的宽度为640，那么针对iOS设备，meta为

```
<meta content="width=640,user-scalable=no" name="viewport">
```
但是对于Android设备，由于只设置宽度它并不会主动进行缩放，所以我们需要通过一些设置来达到缩放的目的，缩放的值是通过**当前设备宽度/设计稿的宽度**来计算

```
<meta content="target-densitydpi=device-dpi,width=640,initial-scale=0.5625,maximum-scale=0.5625" name="viewport">
```
据传这是网易前端实践多年总结出的方案，效果应该还是不错哒！

附上[例子](http://c.3g.163.com/CreditMarket/default.html)。


### 基于REM的解决方案


在CSS3中引入了一个新单位``rem``，这是个啥嘞？在[W3C官方文档](http://www.w3.org/TR/css3-values/)中是这样定义的**font size of the root element**，也就是说它是以页面根元素的字号大小作为基准的，我们页面中元素的大小设置将会基于根元素的字号大小。

比如我们给根节点设置``font-size: 20px;``，则``1rem = 20px``，那么页面元素``40px``应该是``2rem``，附上px和rem的换算地址 [https://offroadcode.com/prototypes/rem-calculator/](https://offroadcode.com/prototypes/rem-calculator/)。

不要看这是一个新引入的单位，但是各大浏览器厂商的觉悟还是很高的，这个单位的兼容性非常高，在Android 2.X以上、iOS5以上都支持，大家可以看看来自[caniuse](http://caniuse.com/#search=rem)的统计。

可以说这是一个神奇的单位，如果我们为页面根元素设置一个字号大小，并且随着页面页面窗口大小的变化去重置这个字号大小，那么页面元素的大小也会跟随根元素字号大小的变化而变化，这就是基于REM来布局的妙处，这样在大屏手机上元素会合理放大，而在小屏手机上元素也会调整到合适的大小，几乎可以很好地适配大部分机型。

搬运自网上的自适应脚本

```javascript

(function (doc, win) {
    var docEl = doc.documentElement,
        resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
        recalc = function () {
            var clientWidth = docEl.clientWidth;
            if (!clientWidth) return;
            docEl.style.fontSize = 100 * (clientWidth / 320) + 'px';
        };

    // Abort if browser does not support addEventListener
    if (!doc.addEventListener) return;
    win.addEventListener(resizeEvt, recalc, false);
    doc.addEventListener('DOMContentLoaded', recalc, false);
})(document, window);
```

但是基于REM的布局也存在缺点，它需要利用JS来做一些类似hack的操作，而且部分浏览器下，计算时，因为数字类型是int，而不是float，所以会进行四舍五入，导致计算偏差，从而当多个模块横向排列时，会出现“放不下，挤下来”等情况。段落文字的字号大小设置不适合使用``rem``来设置，还是应该使用``px``，这样的话还是会有文字在高分辨率手机上显示过小的问题，这时我们可以考虑使用CSS3中提供的``media query``来做一些适配

```
.test {
    font-size: 12px;
}

@media all and (min-width: 361px) {
    .test {
        font-size: 14px;
    }
}

@media all and (min-width: 415px) {
    .test {
        font-size: 16px;
    }
}
```

[淘宝首页](http://m.taobao.com/)就是使用REM来做布局的，而且它通过给不同dpr的设备上元素设置不同字号大小解决了字号适配的问题

```
div {
    width: 1rem;
    height: 0.4rem;
    font-size: 12px; // 默认写上dpr为1的fontSize
}

[data-dpr="2"] div {
    font-size: 24px;
}

[data-dpr="3"] div {
    font-size: 36px;
}
```

同时他们根据设备的dpr动态设置页面的viewport以达到对页面进行一定缩放的目的，来使得页面能按照物理像素来进行渲染，提高页面的清晰度，同时能愉快地写出1px宽的元素。这算是一种组合式适配方案，整体还是非常棒的，而且适配效果非常棒。

附上淘宝解决方案[传送门](https://github.com/amfe/lib-flexible)。

### 总结

使用改变``zoom``粗暴地缩放页面，看起来非常简单高效，但引出的问题不少，所以不推荐使用。

通过改变``viewport``来进行适配，我们所需做的操作非常少，额外引用一段脚本就能实现，而且效果不错，值得尝试。

纯粹的REM布局还是不够完美，字号的适配需要借助响应式来进行操作，略显麻烦，但是兼容性非常好，也是不错的解决方案。

### 参考

- http://www.w3.org/TR/css3-values/
- http://www.quirksmode.org/blog/archives/2010/09/combining_meta.html
- http://isux.tencent.com/web-app-rem.html
- https://github.com/amfe/lib-flexible
- https://github.com/unbug/generator-webappstarter/blob/master/app/templates/app/src/util/MetaHandler.js
- http://www.cnblogs.com/2050/p/3877280.html
- https://www.icloud.com/keynote/AwBWCAESEJd5uucfBPGt6KPotb3tNfsaKm-Q7fqs2-4ojmPoPJuWZCvjYgKl5jEf1URdRgdgNHe38BTzeF3DK7q1ewMCUCAQEEIJ85mw21ii_AwybOqxoF-V02v51Vdg855ED4qVA_8bXr?redirectReason=notFound#Mobile_Webpage_如何自适应屏幕_2
