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




