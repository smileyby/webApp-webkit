webkit webApp 开发技术要点总结

如果你是一名前端er，又想在移动设备上开发自己的应用，那么怎么实现呢？型号，webkit内核的浏览器能帮助我们完成着一切。接触webkit webApp的开发已经有一段时间了，现在把一些技巧分享给大家。

## 1.viewport

也就是可视区域，对于桌面浏览器，我们很清楚viewport是什么，就是除去所有工具栏、状态栏、滚动条等等之后用于网页的区域，这是真正有效的区域。由于移动设备屏幕宽度不同于传统web，因此我们需要改变viewport；

实际上我们可以操作的属性有：

```

width -             //  viewport 的宽度 （范围从200 到10,000，默认为980 像素）
height -            //  viewport 的高度 （范围从223 到10,000）
 
initial-scale -     //  初始的缩放比例 （范围从>0 到10）
 
minimum-scale -    //   允许用户缩放到的最小比例
maximum-scale -    //   允许用户缩放到的最大比例
 
user-scalable -    //   用户是否可以手动缩 (no,yes)

```

那么到底这些设置如何让Safari知道？其实很简单，就是一个meta，形如：

```html

<meta http-equiv="Content-Type" content="text/html; charset=utf-8">   //编码
<meta id="viewport" name="viewport" content="width=320; initial-scale=1.0;maximum-scale=1.0; user-scalable=no;"/>
<meta name=”apple-mobile-web-app-capable” content=”yes” />  // 离线应用的另一个技巧     
<meta name=”apple-mobile-web-app-status-bar-style” content=black” />  // 隐藏状态栏        
<meta content="black" name="apple-mobile-web-app-status-bar-style" /> //指定的iphone中safari顶端的状态条的样式        
<meta content="telephone=no" name="format-detection" />       //告诉设备忽略将页面中的数字识别为电话号码      
<meta name="Author" contect="Mr.He"/ >  

```

在设置了initial-scale=1之后，我们终于可以1:1的比例进行页面设计了。关于viewport，还有一个很重要的概念：iphone的safari浏览器完全没有滚动条，而且不是简单的“隐藏滚动条”，是根本没有这个功能。ipphone的safari浏览器实际上从一开始就完整显示了这个网页，然后用viewport查看其中的一部分。当你用手指拖动时，其实拖得不是页面，而是viewport。浏览器行为的改变不止是滚动条，交互事件也跟普通桌面不一样。（请参考：[指尖下的JS系列文章](http://www.cnblogs.com/pifoo/archive/2011/05/23/webkit-touch-event-1.html)）

## 2.link

```html

<link rel=”apple-touch-startup-image” href=”startup.png” /> // 设置开始页面图片
<link rel=”apple-touch-icon” href=”iphon_tetris_icon.png”/> // 在设置书签的时候可以显示好看的图标
<link rel="stylesheet" media="all and (orientation:portrait)" href="portrait.css">    // 肖像模式样式       
<link rel="stylesheet" media="all and (orientation:landscape)" href="landscape.css"   // 风景模式样式
 
//竖屏时使用的样式 
<style media="all and (orientation:portrait)" type="text/css">
#landscape { display: none; }
</style>
 
//横屏时使用的样式 
<style media="all and (orientation:landscape)" type="text/css">
#portrait { display: none; }
</style> 

```

## 3.事件

```

// 手势事件
touchstart            //当手指接触屏幕时触发
touchmove           //当已经接触屏幕的手指开始移动后触发
touchend             //当手指离开屏幕时触发
touchcancel
 
// 触摸事件
gesturestart          //当两个手指接触屏幕时触发
gesturechange      //当两个手指接触屏幕后开始移动时触发
gestureend
 
// 屏幕旋转事件   
onorientationchange     
 
// 检测触摸屏幕的手指何时改变方向       
orientationchange       
 
// touch事件支持的相关属性
touches         
targetTouches       
changedTouches              
clientX　　　　// X coordinate of touch relative to the viewport (excludes scroll offset)       
clientY　　　　// Y coordinate of touch relative to the viewport (excludes scroll offset)       
screenX　　　 // Relative to the screen        
screenY 　　  // Relative to the screen       
pageX　　 　　// Relative to the full page (includes scrolling)     
pageY　　　　 // Relative to the full page (includes scrolling)     
target　　　　 // Node the touch event originated from      
identifier　　   // An identifying number, unique to each touch event

```

## 4.屏幕旋转：onorientationchange

添加屏幕旋转事件监听，可随时发现屏幕旋转状态（左旋、右旋还是没旋）。例子：

```js


// 判断屏幕是否旋转
function orientationChange() { 
    switch(window.orientation) { 
    　　case 0:  
            alert("肖像模式 0,screen-width: " + screen.width + "; screen-height:" + screen.height); 
            break; 
    　　case -90:  
            alert("左旋 -90,screen-width: " + screen.width + "; screen-height:" + screen.height); 
            break; 
    　　case 90:    
            alert("右旋 90,screen-width: " + screen.width + "; screen-height:" + screen.height); 
            break; 
    　　case 180:    
        　　alert("风景模式 180,screen-width: " + screen.width + "; screen-height:" + screen.height); 
        　　break; 
    };<br>};
// 添加事件监听 
addEventListener('load', function(){ 
    orientationChange(); 
    window.onorientationchange = orientationChange; 
});

```

## 5.隐藏地址栏&处理事件的时候，放置滚动条出现：

```js


// 隐藏地址栏  & 处理事件的时候 ，防止滚动条出现
addEventListener('load', function(){ 
        setTimeout(function(){ window.scrollTo(0, 1); }, 100); 
});

```

## 6.双手指滑动事件

```js


// 双手指滑动事件
addEventListener('load',　　function(){ window.onmousewheel = twoFingerScroll;}, 
     false              // 兼容各浏览器，表示在冒泡阶段调用事件处理程序 (true 捕获阶段)
); 
function twoFingerScroll(ev) { 
    var delta =ev.wheelDelta/120;              //对 delta 值进行判断(比如正负) ，而后执行相应操作 
    return true; 
}; 

```

## 7.特殊连接

如果你关闭自动识别后，游戏王某些电话号码能够连接到iPhone的拨号功能，那么可以通过这样的声明电话连接：

```html


<a href="tel:12345654321">打电话给我</a>
<a href="sms:12345654321">发短信</a>

// 或用于单元格：
<td onclick="location.href='tel:122'">

```

## 8.自动大写与自动修正

要关闭这两项功能，可以通过autocapitalize与autocorrect这两个选项：

```html

<input type="text" autocapitalize="off" autocorrect="off">

```

## 9.锁定viewport

```

ontouchmove="event.preventDefault()" //锁定viewport，任何屏幕操作不移动用户界面（弹出键盘除外）。

```

## 10.被点击元素的外观变化，可以使用样式来设定：

```css

-webkit-tap-highlight-color: #333;

```

## 11.侦测iPhone/iPod

开发待定设备的移动网站，首先要做的就是设备侦测了。下面是使用javascript侦测iPhone/iPod的UA，然后转向专属的URL

```js

if((navigator.userAgent.match(/iPhone/i)) || (navigator.userAgent.match(/iPod/i))) {
　　if (document.cookie.indexOf("iphone_redirect=false") == -1) {
　　　　window.location = "http://m.example.com";
　　}
}

```

虽然Javascript是可以在水果设备上运行的，但是用户还是可以禁用。它也会造成客户端刷新和额外的数据传输，所以下面是服务器端侦测和转向：

```js


if(strstr($_SERVER['HTTP_USER_AGENT'],'iPhone') || strstr($_SERVER['HTTP_USER_AGENT'],'iPod')) {
　　header('Location: http://yoursite.com/iphone');
　　exit();
}

```

## 12.阻止旋转屏幕时自动调整字体大小

```css

html, body, form, fieldset, p, div, h1, h2, h3, h4, h5, h6 {-webkit-text-size-adjust:none;}

```

## 13.iPhone才识别的CSS

如果不想设备侦测，可以用CSS媒体查询来转为iphone/ipod定义样式

```css

@media screen and (max-device-width: 480px) {}

```

## 13.缩小图片

网站的大图通常宽度都超过480像素，如果用前面的代码限制了缩放，这些图片在iPhone版显示显然会超过屏幕。好在我们可以用CSS让iPhone自动将大图片缩小显示。

```css

@media screen and (max-device-width: 480px){
　　img{max-width:100%;height:auto;}
}

```

## 14.模拟hover伪类

因为iPhone并没有鼠标指针，所以没有hover事件。那么CSS :hover伪类就没用了。但是iPhone有Touch事件，onTouchStart 类似 onMouseOver，onTouchEnd 类似 onMouseOut。所以我们可以用它来模拟hover。使用Javascript：

```js

var myLinks = document.getElementsByTagName('a');
for(var i = 0; i < myLinks.length; i++){
　　myLinks[i].addEventListener(’touchstart’, function(){this.className = “hover”;}, false);
　　myLinks[i].addEventListener(’touchend’, function(){this.className = “”;}, false);
}

```

然后用CSS增加hover效果：

```css

a:hover, a.hover { /* 你的hover效果 */ }

```

