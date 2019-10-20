# jQuery实现瀑布流布局

---
### 大概思路：
瀑布流布局的特点是元素等宽不等高。添加图片时，找到最短的那一列，将图片插入，并更新当前列高度，依此类推。

>获取图片方法：  
网址：[http://placekitten.com](http://placekitten.com)  
使用方法：http://placekitten.com/宽/高/（彩色） 或 http://placekitten.com/g/宽/高/ （黑白）  
例如：[http://placekitten.com/200/300](http://placekitten.com/200/300)

---
### 详细代码：
#### 一、变量声明，获取元素
```
var initWidth = 150, // 初始宽度
  winWidth = $(window).width(), //可视区宽度
  num = 0, // 列数
  img = []; // 图片
var left = []; // 每列图片left值
var top = []; // 每列图片top值
var needWidth = [0, 237, 391, 545, 699]; // 列数需要的宽度
var wrapper = $(".wrapper");
```
#### 二、需要用到的方法
##### 1.初始化，根据可视区宽度设置图片列数和宽度
```
function init() {
  winWidth = $(window).width(); // 获取可视区的宽
  // 设置图片列数
  for (var i = 4; i >= 0; i--) {
    if ((winWidth - 16) > needWidth[i]) {
      num = i + 1; // 图片列数
      top = [0, 0, 0, 0, 0]; // 设置每列图片top值
      left[0] = 0; // 第一列图片left
      // 如果winWidth < 782，则重新设置图片宽
      if (winWidth < 782) {
        initWidth = (winWidth - 16 - 4 * (num - 1)) / num; // 重新设置图片宽
      }
      for (var j = 1; j < num; j++) {
        left[j] = left[j - 1] + initWidth + 4;
      }
      wrapper.css("width", (initWidth + 4) * num - 4); // 改变wrapper的宽
      break;
    }
  }
}
```
##### 2.向页面中添加图片
```
function addPic() {
  var len = img.length;
  var width = parseInt(Math.random() * 100 + 100); // 图片的宽，100 - 200 之间随机数
  var height = parseInt(Math.random() * 100 + 200); // 图片的高，200 - 300 之间随机数
  var url = "http://placekitten.com/" + width + "/" + height; // 图片的url
  img[len] = $("<img src='" + url + "'>"); // 创建图片
  img[len].w = width; // 存储图片高
  img[len].h = height; // 存储图片高
  wrapper.append(img[len]); // 插入图片
  setPic(len); // 排列图片
}
```
##### 3.排列图片
```
function setPic(index) {
  var min = 0; // top值最小的列索引
  // 找到top值最小列的索引
  for (var i = 0; i < num; i++) {
    if (top[i] < top[min]) {
      min = i;
    }
  }
  var height = img[index].h * initWidth / img[index].w; // 当图片宽为初始宽时，图片的高
  img[index].width(initWidth); // 设置图片宽
  img[index].height(height); // 设置图片高
  // 设置图片位置
  img[index].css({
    "left": left[min],
    "top": top[min]
  })
  top[min] += height + 4; //修改当前列的top值
}
```
##### 4.像页面中添加20张图片
```
(function () {
  init();
  for (var i = 0; i < 20; i++) {
    addPic();
  }
}())
```
#### 三、事件
##### 1.页面滚动事件，如果文档高度 - 滚动条距离 < 1000，则添加图片
```
$(window).scroll(function () {
  if ($(document).height() - $(window).scrollTop() < 1000) {
    addPic();
  }
})
```
##### 2.窗口可视区宽度改变事件，当可视区宽度改变时，根据可视区宽度改变列数，重新排列图片，并返回页面顶部
```
$(window).resize(function () {
  if (winWidth !== $(window).width()) {
    $(document).scrollTop(0); // 回到页面顶部
    init(); // 初始化
    // 重新排列图片
    for (var i = 0; i < img.length; i++) {
      setPic(i);
    }
  }
})
```