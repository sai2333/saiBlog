---
home: true
heroText: 小程序专栏
sidebar: auto
tags: 
- 小程序组件
- 编辑图片
- poster
---
组件名： **editImg.vue**
功能：可以点击替换图片，触摸移动，缩放。
## 功能介绍
### 1. 点击替换图片
```javascript
_tap(e,item,index){
    //当前的图片未选中，点击后显示选中状态
   if (this.active !== index) {
        this.$emit("update:active", index);
        if(this.lastCurrent >= 0) {
          this.value[this.lastCurrent].current = 0;
        }
        this.lastCurrent = index;
    }
    //更改提示文字
    if(item.round){
       this.$emit("update:tipsText",'点击更换头像');
    }else if(Number(item.r) === 1){
        this.$emit("update:tipsText",'点击更换二维码');
    }else{
        this.$emit("update:tipsText",'点击更换图片');
    }
    this.tipsHandle(item,index);
}
```
#### tap事件
* 通过props.active表示被选中的元素。
* 用户点击图片元素，记录被点击的元素位置。
* 如果点击的不是被选中的元素，那么更新active，当前元素current加1，并且把上一个记录着的元素current属性归0。
* 如果点击的是被选中的元素，则执行替换图片函数
#### touchStart事件
* 记录手指触摸的位置（这个位置是相对于canvasWrap，而不是屏幕的）
#### touchMove事件
* 是否可以移动的条件，取决于：当前页面是不是自定义页面和当前元素是不是被选中
* 获取当前第一个手指的位置（x,y轴）
* 移动后的位置为：当前的图片位置 + （当前的手指位置 - 上一次记录的位置）
* 通知父组件更新border-box的位置
* 记录图片的imgCenter(用于缩放的坐标)
* 记录触摸结束后的手指位置
#### _iconTouchStart
在icon上，是通过两指缩放的原理来进行缩放的。比如说，上面的imgCenter坐标，实际上就是一只手指在上面的，另外一只手指在icon，通过这两个坐标的距离来计算要缩放的倍数。
```javascript
this.recordPreTouchPosition(touches, item);
item.preTouchesClientx1y1x2y2 = [item.imgCenter.x,item.imgCenter.y,touches.clientX,touches.clientY];
```
* 记录双指位置。
#### _iconTouchMove
缩放公式： 缩放倍率 = 当前的双指距离 / 上次的双指距离 * 上次的缩放倍率
双指距离可以使用勾股定理公式计算。
* 计算当前滑动后的缩放倍率
* 通知父组件更新border-box的缩放倍率
* 记录双指位置