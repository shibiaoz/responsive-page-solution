# 适配讨论

- 贴吧 width=device-width scale=1
    - 居中留白宽度自适应
    - 定高不定宽

## 业界适配方案
   - zoom
   - viewport
    - 静态viewport，网易的最佳实践 ,ue稿640，完全按照ue稿进行切图
    - 动态viewport，页面scale，按照某种尺寸的ue图进行切图，动态缩放viewport
   - rem 实现缩放
     viewport + dpr + rem + mediaquery


## 固定viewport
1. viewport 设置一个具体值, change viewport initial-scale
```
 <meta name="viewport" content="width=320,initial-scale=1.171875,maximum-scale=3,user-scalable=no">

页面css layout = 320px
js get deviceWidth => document.documentElement.clientWidth
scale = deviceWidth/320;
initial-scale = scale
这种方式审查元素的时候元素的尺寸不变，只是展现的进行了缩放
`横屏怎么处理呢？` 在更改scale 貌似行不通过
实现方式：
    <meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=3,user-scalable=no">
    <script type="text/javascript">
        console.log(document.documentElement.clientWidth);
        var metaEl =  document.querySelector('meta[name="viewport"]');
        var width = document.documentElement.clientWidth;
        var baseLayoutWidth = 750;
        var scale = width/baseLayoutWidth;
        console.log('scale =>  ' + scale);
        metaEl.setAttribute('content', 'width=750' + ',initial-scale=' + scale + ',maximum-scale=' + scale + ', minimum-scale=' + scale + ',user-scalable=no');
        console.log(document.documentElement.clientWidth);
    </script>


```




## 结合贴吧的尝试





1.定宽度
定宽有个坑，就是如果你的页面要嵌入到App中时，App是以webview的形式渲染页面的。webview实际上也是webkit内核，而最新的webkit内核对定宽支持不是很好，他默认是以device-width来渲染的。这样一来你的页面就被放大了，窗口还有滚动条，解决办法是让app开发帮忙设置两个属性：
WebSettings webSettings = view.getSettings(); webSettings.setLayoutAlgorithm(LayoutAlgorithm.NARROW_COLUMNS);
webSettings.setUseWideViewPort(true);
可是App开发说这样影响性能。所以我觉得用device-width是最靠谱的


refers

1.用REM做单位与百分比做单位有什么优势？

主要优势在于能更好的控制元素大小。（一般百分比应用在布局层，一般常见设置为50%，33.3%，25%之类的整数居多，难以运用在复杂的页面小部件内）。
但是相比百分比布局，需要借助JS或media query实现，略有一点瑕疵
[参考](http://www.ghugo.com/mobile-h5-fluid-layout-for-iphone6/)

2.图片自适应
那么可以用padding-top设置百分比值来实现自适应。
公式如下：
padding-top = (Image Height / Image Width) * 100%
原理：padding-top值为百分比时，取值是是相对于宽度的
[参考](http://www.ghugo.com/mobile-h5-fluid-layout-for-iphone6/)

3. [perfect](http://qipengliu.com/2015/09/25/%E8%87%AA%E5%8A%A8%E5%8C%96%E7%8E%AF%E5%A2%83%E5%8F%8A%E7%A7%BB%E5%8A%A8%E7%AB%AF%E5%A4%9A%E5%B1%8F%E9%80%82%E9%85%8D(grunt)/)

4.[viewport] (https://www.zybuluo.com/gongzhen/note/170557)
5.[viewport 下面的讨论比较有意思](http://segmentfault.com/q/1010000002551392)


