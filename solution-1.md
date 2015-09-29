## viewport 缩放实现响应式
- device-width
- baseLayoutWidth  暂且先这么叫（我起的），这个是ue稿的宽度
- scale

> 开人员只需要按照ue稿给定的宽度和高度去开发即可，不去去考虑2x的问题

```
/**
 * the thinking method
 * var scale = device-width/baseLayoutWidth;
 */
// 在页面顶部修改viewport的值
var metaEl =  document.querySelector('meta[name="viewport"]');
var width = document.documentElement.clientWidth;
var baseLayoutWidth = 640;
var scale = width/baseLayoutWidth;
metaEl.setAttribute('content', 'width=device-width' + ',initial-scale=' + scale + ',maximum-scale=' + scale + ', minimum-scale=' + scale + ',user-scalable=no');
// 注意：头部meta初始化必须这么写
<meta id="viewport" name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
```

> 为什么alet出来的两次document.documentElement.clientWidth 值不一样

## viewport 缩放优点
- 开发简单，只要按照开发稿，px单位即可，不用多想
- 实现响应式

## viewport 缩放缺点
- viewport缩放会影响到所有px单位的元素
- 保证页面所有的开发都是基于baselayoutwidth宽度
- 这个方案不适用于贴吧现有的框架（要调用其他组件）


