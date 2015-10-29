## zoom 缩放
- 暴力简单
- 不适用于复杂页面，整体缩放容易导致问题
- uc支持性不好，有些
- 目前在android 和 ios 自带的webView 上是没有问题的，在UC下会有字体不随zoom样式缩放的bug，会导致展示混乱！
- 伟涛说的`会改变元素原来的top/height等值，让已有组件如lazyload失效而需要重新计算`我测试了发现top,设置缩放后，当滚动页面的时候，在活动对应元素的offset() 里面的top值会变，这样容易导致混乱



```
经过这三步就暴力的实现了一套伪自适应方案。

下来我们再将它封装成一个适合放在我们所有活动页面的footer中，这次我们只是动态的在head里面创建了一个style 并追加了一个.zoom的样式， 只需在页面上添加一个zoom的class。

(function(){
    var _w,
        _zoom,
        _hd,
        _orientationChange,
        _doc = document,
        _style = _doc.getElementById("_zoom");
    _style || (
        _hd = _doc.getElementsByTagName("head")[0],
        _style=_doc.createElement("style"),
        _hd.appendChild(_style)
    );
    _orientationChange = function(){
       _w    = _doc.documentElement.clientWidth || _doc.body.clientWidth;
       _zoom = _w / 640;
       _style.innerHTML = ".zoom {zoom:" + _zoom + ";-webkit-text-size-adjust:auto!important;}";
    };
    _style.setAttribute("zoom",_zoom);
    _orientationChange();
    window.addEventListener("resize",_orientationChange,false);
})();
```
