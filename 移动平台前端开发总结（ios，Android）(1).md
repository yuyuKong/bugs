

## 移动平台前端开发总结（ios，Android）

#### 首先我们来看看webkit内核中的一些私有的meta标签，这些meta标签在开发webapp时起到非常重要的作用

- <meta content="width=device-width; initial-scale=1.0; maximum-scale=1.0; user-scalable=0" name="viewport" />
  - 强制让文档的宽度与设备的宽度保持1:1，并且文档最大的宽度比例是1.0，且不允许用户点击屏幕放大浏览；尤其要注意的是content里多个属性的设置一定要用分号+空格来隔开，如果不规范将不会起作用。
- <meta content="yes" name="apple-mobile-web-app-capable" />
  - iphone设备中的safari私有meta标签，它表示：允许全屏模式浏览
- <meta content="black" name="apple-mobile-web-app-status-bar-style" />
  - iphone的私有标签，它指定的iphone中safari顶端的状态条的样式
- <meta content="telephone=no" name="format-detection" />
  - 告诉设备忽略将页面中的数字识别为电话号码

#### 相关知识点

- 移动端、 适配（兼容）、 ios点击事件300ms延迟、 点击穿透、 定位失效......

  ​

#### 问题&解决方案



- 手机浏览器独有的三个事件？

```
onTouchmove，ontouchend，ontouchstart，ontouchcancel

```

- 为什么要用Zepto?

```
jquery适用于PC端桌面环境，桌面环境更加复杂，jquery需要考虑的因素非常多，尤其表现在兼容性上面，相对于PC端，移动端的发杂都远不及PC端,手机上的带宽永远比不上pc端。pc端下载jquery到本地只需要1~3秒（90+K），但是移动端就慢了很多，2G网络下你会看到一大片空白网页在加载，相信用户第二次就没打开的欲望了。zepto解决了这个问题，只有不到10K的大小，2G网络环境下也毫无压力，表现不逊色于jquery。所以移动端开发首选框架，个人推荐zepto.js。

```

- IOS移动端click事件300ms的延迟响应

```
移动设备上的web网页是有300ms延迟的，玩玩会造成按钮点击延迟甚至是点击失效。这是由于区分单击事件和双击屏幕缩放的历史原因造成的,
2007年苹果发布首款iphone上IOS系统搭载的safari为了将适用于PC端上大屏幕的网页能比较好的展示在手机端上，使用了双击缩放(double tap to zoom)的方案，比如你在手机上用浏览器打开一个PC上的网页，你可能在看到页面内容虽然可以撑满整个屏幕，但是字体、图片都很小看不清，此时可以快速双击屏幕上的某一部分，你就能看清该部分放大后的内容，再次双击后能回到原始状态。
双击缩放是指用手指在屏幕上快速点击两次，iOS 自带的 Safari 浏览器会将网页缩放至原始比例。
原因就出在浏览器需要如何判断快速点击上，当用户在屏幕上单击某一个元素时候，例如跳转链接<a href="#"></a>，此处浏览器会先捕获该次单击，但浏览器不能决定用户是单纯要点击链接还是要双击该部分区域进行缩放操作，所以，捕获第一次单击后，浏览器会先Hold一段时间t，如果在t时间区间里用户未进行下一次点击，则浏览器会做单击跳转链接的处理，如果t时间里用户进行了第二次单击操作，则浏览器会禁止跳转，转而进行对该部分区域页面的缩放操作。那么这个时间区间t有多少呢？在IOS safari下，大概为300毫秒。这就是延迟的由来。造成的后果用户纯粹单击页面，页面需要过一段时间才响应，给用户慢体验感觉，对于web开发者来说是，页面js捕获click事件的回调函数处理，需要300ms后才生效，也就间接导致影响其他业务逻辑的处理。
解决方案：
fastclick可以解决在手机上点击事件的300ms延迟
zepto的touch模块，tap事件也是为了解决在click的延迟问题
触摸事件的响应顺序为 touchstart --> touchmove --> touchend --> click,也可以通过绑定ontouchstart事件，加快对事件的响应，解决300ms延迟问题

```

- 一些情况下对非可点击元素如(label,span)监听click事件，ios下不会触发

```
解决方案：css增加cursor:pointer;

```

- 三星手机遮罩层下的input、select、a等元素可以被点击和focus(点击穿透)

```
问题发现于三星手机，这个在特定需求下才会有，因此如果没有类似问题的可以不看。首先需求是浮层操作，在三星上被遮罩的元素依然可以获取focus、click、change)，有两种解决方案：
1.是通过层显示以后加入对应的class名控制，截断显示层下方可获取焦点元素的事件获取
2.是通过将可获取焦点元素加入的disabled属性，也可以利用属性加dom锁定的方式（disabled的一种变换方式）

```

- 安卓浏览器看背景图片，有些设备会模糊。

```
//用同等比例的图片在PC机上很清楚，但是手机上很模糊，原因是什么呢？
经过研究，是devicePixelRatio作怪，因为手机分辨率太小，如果按照分辨率来显示网页，这样字会非常小，所以苹果当初就把iPhone 4的960*640分辨率，在网页里只显示了480*320，这样devicePixelRatio＝2。现在android比较乱，有1.5的，有2的也有3的。
想让图片在手机里显示更为清晰，必须使用2x的背景图来代替img标签（一般情况都是用2倍）。例如一个div的宽高是100*100，背景图必须得200*200，然后background-size:contain;，这样显示出来的图片就比较清晰了。
//代码可以如下：
background:url(../images/icon/all.png)  no-repeat center center;
-webkit-background-size:50px 50px;
background-size: 50px 50px;
display:inline-block; 
width:100%; 
height:50px; 

```

- h5页面有个很蛋疼的问题就是，当输入框在最底部，点击软键盘后输入框会被遮挡。

```
//可采用如下方式解决
var oHeight = $(document).height(); //浏览器当前的高度
 $(window).resize(function(){ 
      if($(document).height() < oHeight){ 
            $("#footer").css("position","static"); 
      }else{ 
            $("#footer").css("position","absolute");
      } 
});

```

关于Web移动端Fixed布局的解决方案，这篇文章也不错 [http://efe.baidu.com/blog/mobile-fixed-layout/](https://link.jianshu.com?t=http://efe.baidu.com/blog/mobile-fixed-layout/)

- 不让 Android 手机识别邮箱

```
<meta content="email=no" name="format-detection" />

```

- 禁止 iOS 识别长串数字为电话

```
<meta content="telephone=no" name="format-detection" />

```

- 禁止 iOS 弹出各种操作窗口

```
-webkit-touch-callout:none

```

- 消除 transition 闪屏

```
-webkit-transform-style: preserve-3d; /*设置内嵌的元素在 3D 空间如何呈现：保留 3D*/
-webkit-backface-visibility: hidden; /*(设置进行转换的元素的背面在面对用户时是否可见：隐藏)*/

```

- iOS 系统中文输入法输入英文时，字母之间可能会出现一个六分之一空格

```
可以通过正则去掉     
 this.value = this.value.replace(/\u2006/g, '');

```

- 禁止ios和android用户选中文字

```
-webkit-user-select:none

```

- 在ios和andriod中,audio元素和video元素无法自动播放

```
//解决方案：触屏即播
$('html').one('touchstart',function(){ audio.play()})

```

- ios下取消input在输入的时候英文首字母的默认大写

```
<input autocapitalize="off" autocorrect="off" />

```

- android下取消输入语音按钮

```
input::-webkit-input-speech-button {display: none}

```

- CSS动画页面闪白,动画卡顿

```
//解决方法:
1.尽可能地使用合成属性transform和opacity来设计CSS3动画，不使用position的left和top来定位
2.开启硬件加速
  -webkit-transform: translate3d(0, 0, 0);
     -moz-transform: translate3d(0, 0, 0);
      -ms-transform: translate3d(0, 0, 0);
          transform: translate3d(0, 0, 0);

```

- fixed定位缺陷

```
ios下fixed元素容易定位出错，软键盘弹出时，影响fixed元素定位
android下fixed表现要比iOS更好，软键盘弹出时，不会影响fixed元素定位
ios4下不支持position:fixed
解决方案： 可用iScroll插件解决这个问题

```

- 阻止旋转屏幕时自动调整字体大小

```
html, body, form, fieldset, p, div, h1, h2, h3, h4, h5, h6 {-webkit-text-size-adjust:none;}

```

- Input 的placeholder会出现文本位置偏上的情况

```
input 的placeholder会出现文本位置偏上的情况：
PC端设置line-height等于height能够对齐，而移动端仍然是偏上，解决是设置line-height：normal;

```

- 往返缓存问题

```
点击浏览器的回退，有时候不会自动执行js，特别是在mobile safari中。这与*往返缓存(bfcache)*有关系。
解决方法 ：window.onunload = function(){};

```

- calc的兼容性处理

```
CSS3中的calc变量在iOS6浏览器中必须加-webkit-前缀，目前的FF浏览器已经无需-moz-前缀。
Android浏览器目前仍然不支持calc，所以要在之前增加一个保守尺寸：
div { width: 95%; width: -webkit-calc(100% - 50px); width: calc(100% - 50px); }

```

- iOS6下伪类:hover

```
除了<a>之外的元素无效；
在Android下则有效。类似
div#topFloatBar_l:hover  #topFloatBar_menu { display:block; }
这样的导航显示在iOS6点击没有点击效果，只能通过增加点击侦听器给元素增减class来控制子元素。

```

- 在移动端修改难看的点击的高亮效果，iOS和安卓下都有效：

```
*{-webkit-tap-highlight-color:rgba(0,0,0,0);}
不过这个方法在现在的安卓浏览器下，只能去掉那个橙色的背景色，点击产生的高亮边框还是没有去掉，有待解决！

```

- 一个CSS3的属性，加上后，所关联的元素的事件监听都会失效，等于让元素变得“看得见，点不着”。IE到11才开始支持，其他浏览器的当前版本基本都支持。

```
pointer-events: none;

```

详细介绍见这里：[https://developer.mozilla.org/zh-CN/docs/Web/CSS/pointer-events](https://link.jianshu.com?t=https://developer.mozilla.org/zh-CN/docs/Web/CSS/pointer-events)

- Zepto点透的解决方案

```
zepto的tap是通过兼听绑定在document上的touch事件来完成tap事件的模拟的,及tap事件是冒泡到document上触发的,在点击完成时的tap事件(touchstart\touchend)需要冒泡到document上才会触发，而在冒泡到document之前，用户手的接触屏幕(touchstart)和离开屏幕(touchend)是会触发click事件的,因为click事件有延迟触发(这就是为什么移动端不用click而用tap的原因)(大概是300ms,为了实现safari的双击事件的设计)，所以在执行完tap事件之后，弹出来的选择组件马上就隐藏了，此时click事件还在延迟的300ms之中，当300ms到来的时候，click到的其实不是完成而是**隐藏之后的下方的元素，如果正下方的元素绑定的有click事件此时便会触发，如果没有绑定click事件的话就当没click，但是正下方的是input输入框(或者select选择框或者单选复选框)，点击默认聚焦而弹出输入键盘，也就出现了上面的点透现象。
//(1)引入fastclick.js，在页面中加入如下js代码
 window.addEventListener( "load", function() {
    FastClick.attach( document.body );
 }, false );
//(2)或者有zepto或者jQuery的js里面加上
$(function() {
    FastClick.attach(document.body);
});
//(3)当然require的话就这样：
var FastClick = require('fastclick');
FastClick.attach(document.body, options);
//(4)用touchend代替tap事件并阻止掉touchend的默认行为preventDefault()
$("#cbFinish").on("touchend", function (event) {
    //很多处理比如隐藏什么的
    event.preventDefault();
});
//(5)延迟一定的时间(300ms+)来处理事件
$("#cbFinish").on("tap", function (event) {
    setTimeout(function(){
     //很多处理比如隐藏什么的
    },320);
});

```

- 图片加载
  若您遇到图片加载很慢的问题，对这种情况，手机开发一般用canvas方法加载：
  具体的canvas API 参见：[http://javascript.ruanyifeng.com/htmlapi/canvas.html](https://link.jianshu.com?t=http://javascript.ruanyifeng.com/htmlapi/canvas.html)

```
下面举例说明一个canvas的例子：
<li><canvas></canvas></li>
js动态加载图片和li 总共举例17张图片！
vartotal=17; 
varzWin=$(window); 
varrender=function(){
  varpadding=2; 
  varwinWidth=zWin.width(); 
  varpicWidth=Math.floor((winWidth-padding*3)/4); 
  vartmpl ='';
  for(vari=1;i<=totla;i++){ 
     varp=padding; 
     varimgSrc='img/'+i+'.jpg';
     if(i%4==1){
        p=0;
     }
  tmpl +='<li style="width:'+picWidth+'px;height:'+picWidth+'px;padding-left:'+p+'px;padding-top:'+padding+'px;"><canvas id="cvs_'+i+'"></canvas></li>';
  varimageObj = newImage(); 
  imageObj.index = i; 
  imageObj.onload = function(){
    varcvs =$('#cvs_'+this.index)[0].getContext('2d');
    cvs.width = this.width;
    cvs.height=this.height;
    cvs.drawImage(this,0,0);
  }
  imageObj.src=imgSrc;
  }
}
render();

```

- 防止手机中网页放大和缩小

```
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=0" />

```

- apple-mobile-web-app-capable

```
apple-mobile-web-app-capable是设置Web应用是否以全屏模式运行。
语法：<meta name="apple-mobile-web-app-capable"content="yes">
说明：如果content设置为yes，Web应用会以全屏模式运行，反之，则不会。
content的默认值是no，表示正常显示。你可以通过只读属性window.navigator.standalone来确定网页是否以全屏模式显示。

```

- html5调用安卓或者ios的拨号功能

```
html5提供了自动调用拨号的标签，只要在a标签的href中添加tel:就可以了。
如下：
<ahref="tel:4008106999,1034">400-810-6999 转 1034</a>
拨打手机直接如下:
 <a href="tel:15677776767">点击拨打15677776767</a>

```

- html5GPS定位功能  具体请看：[http://www.jb51.net/post/html5_GPS_getCurrentPosition](https://link.jianshu.com?t=http://www.jb51.net/post/html5_GPS_getCurrentPosition)
- 上下拉动滚动条时卡顿、慢

```
body {
    -webkit-overflow-scrolling:touch; 
    overflow-scrolling: touch;
}

```

##### Android3+和iOS5+支持CSS3的新属性为overflow-scrolling

- 禁止复制、选中文本

```
Element {
	-webkit-user-select:none;
    -moz-user-select:none;
    -khtml-user-select:none;
    user-select:none;
}

```

- iphone及ipad下输入框默认内阴影

```
Element{
  -webkit-appearance:none;
}

```

- 13、ios和android下触摸元素时出现半透明灰色遮罩

```
Element {
  -webkit-tap-highlight-color:rgba(255,255,255,0)
}
设置alpha值为0就可以去除半透明灰色遮罩，备注：transparent的属性值在android下无效。

```

一篇文章有详细介绍，地址：[http://www.jb51.net/post/phone_web_ysk](https://link.jianshu.com?t=http://www.jb51.net/post/phone_web_ysk)

- active兼容处理 即 伪类 :active 失效

```
方法一：body添加ontouchstart
<body ontouchstart="">
方法二：js给 document 绑定 touchstart 或 touchend 事件
<style>
   a {
     color:#000;
   }
   a:active {
      color:#fff;
    }
    </style>
    <a herf=foo >bar</a>
 <script>
    document.addEventListener('touchstart',function(){},false);
</script>

```

- 动画定义3D启用硬件加速

```
Element {
  -webkit-transform:translate3d(0,0,0)
  transform: translate3d(0,0,0);
}
注意：3D变形会消耗更多的内存与功耗

```

- Retina屏的1px边框

```
Element{
  border-width:thin;
}

```

- webkit mask 兼容处理

```
某些低端手机不支持css3 mask，可以选择性的降级处理。
比如可以使用js判断来引用不同class：
if('WebkitMask'indocument.documentElement.style){
  alert('支持mask');
}else{
  alert('不支持mask');
}

```

- 圆角bug

```
某些Android手机圆角失效
解决方案：background-clip: padding-box;

```

- 顶部状态栏背景色

```
<meta name="apple-mobile-web-app-status-bar-style"content="black"/>
说明：除非你先使用apple-mobile-web-app-capable指定全屏模式，否则这个meta标签不会起任何作用。
如果content设置为default，则状态栏正常显示。
如果设置为blank，则状态栏会有一个黑色的背景。
如果设置为blank-translucent，则状态栏显示为黑色半透明。
如果设置为default或blank，则页面显示在状态栏的下方，即状态栏占据上方部分，页面占据下方部分，二者没有遮挡对方或被遮挡。
如果设置为blank-translucent，则页面会充满屏幕，其中页面顶部会被状态栏遮盖住（会覆盖页面20px高度，而iphone4和itouch4的Retina屏幕为40px）。
默认值是default。

```

- 设置缓存

```
<meta http-equiv="Cache-Control"content="no-cache"/>
手机页面通常在第一次加载后会进行缓存，然后每次刷新会使用缓存而不是去重新向服务器发送请求。
如果不希望使用缓存可以设置no-cache。

```

- 桌面图标

```
<link rel="apple-touch-icon"href="touch-icon-iphone.png"/>
<link rel="apple-touch-icon"sizes="76x76"href="touch-icon-ipad.png"/>
<link rel="apple-touch-icon"sizes="120x120"href="touch-icon-iphone-retina.png"/>
<link rel="apple-touch-icon"sizes="152x152"href="touch-icon-ipad-retina.png"/>
iOS下针对不同设备定义不同的桌面图标。如果不定义则以当前屏幕截图作为图标。
上面的写法可能大家会觉得会有默认光泽，下面这种设置方法可以去掉光泽效果，还原设计图的效果！
   <link rel="apple-touch-icon-precomposed"href="touch-icon-iphone.png"/>
图片尺寸可以设定为5757（px）或者Retina可以定为114114（px），ipad尺寸为72*72（px)

```

- 启动画面

```
<link rel="apple-touch-startup-image"href="start.png"/>
iOS下页面启动加载时显示的画面图片，避免加载时的白屏。
可以通过madia来指定不同的大小：
<!--iPhone-->
<link href="apple-touch-startup-image-320x460.png"media="(device-width: 320px)" rel="apple-touch-startup-image"/>
<!-- iPhone Retina -->
<link href="apple-touch-startup-image-640x920.png"media="(device-width: 320px) and (-webkit-device-pixel-ratio: 2)" rel="apple-touch-startup-image"/>
<!-- iPhone 5-->
<link rel="apple-touch-startup-image"media="(device-width: 320px) and (device-height: 568px) and (-webkit-device-pixel-ratio: 2)" href="apple-touch-startup-image-640x1096.png">
<!-- iPad portrait-->
<link href="apple-touch-startup-image-768x1004.png"media="(device-width: 768px) and (orientation: portrait)" rel="apple-touch-startup-image"/>
<!-- iPad landscape-->
<link href="apple-touch-startup-image-748x1024.png"media="(device-width: 768px) and (orientation: landscape)" rel="apple-touch-startup-image"/>
<!-- iPad Retina portrait-->
<link href="apple-touch-startup-image-1536x2008.png"media="(device-width: 1536px) and (orientation: portrait) and (-webkit-device-pixel-ratio: 2)" rel="apple-touch-startup-image"/>
<!-- iPad Retina landscape-->
<link href="apple-touch-startup-image-1496x2048.png"media="(device-width: 1536px) and (orientation: landscape) and (-webkit-device-pixel-ratio: 2)"rel="apple-touch-startup-image"/>

```

- 浏览器私有及其它meta

```
以下属性在项目中没有应用过，可以写一个demo测试以下！
//QQ浏览器私有
全屏模式
<meta name="x5-fullscreen"content="true">
强制竖屏
<meta name="x5-orientation"content="portrait">
强制横屏
<meta name="x5-orientation"content="landscape">
应用模式
<meta name="x5-page-mode"content="app">
//UC浏览器私有
全屏模式
<meta name="full-screen"content="yes">
强制竖屏
<meta name="screen-orientation"content="portrait">
强制横屏
<meta name="screen-orientation"content="landscape">
应用模式
<meta name="browsermode"content="application">
//其它
针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓
<meta name="HandheldFriendly" content="true">
微软的老式浏览器
<meta name="MobileOptimized" content="320">
windows phone 点击无高光
<meta name="msapplication-tap-highlight" content="no">

```

- IOS中input键盘事件keyup、keydown、keypress支持不是很好

```
问题是这样的，用input search做模糊搜索的时候，在键盘里面输入关键词，会通过ajax后台查询，然后返回数据，然后再对返回的数据进行关键词标红。用input监听键盘keyup事件，在安卓手机浏览器中是可以的，但是在ios手机浏览器中变红很慢，用输入法输入之后，并未立刻相应keyup事件，只有在通过删除之后才能相应！
解决办法：
可以用html5的oninput事件去代替keyup
<input type="text"id="testInput">
<script type="text/javascript">
  document.getElementById('testInput').addEventListener('input',function(e){
    varvalue = e.target.value;
  });
</script>
然后就达到类似keyup的效果！

```

- h5网站input 设置为type=number的问题

```
- h5网页input 的type设置为number一般会产生三个问题，一个问题是maxlength属性不好用了。
另外一个是form提交的时候，默认给取整了。三是部分安卓手机出现样式问题。

```

//问题一解决，我目前用的是js。如下
<input type="number"oninput="checkTextLength(this ,10)">
functioncheckTextLength(obj, length) {
if(obj.value.length > length)  {
obj.value = obj.value.substr(0, length);
}
}
//问题二，是因为form提交默认做了表单验证，step默认是1,要设置step属性，假如保留2位小数，写法如下：
<input type="number"step="0.01"/>
关于step，我在这里做简单的介绍，input 中type=number，一般会自动生成一个上下箭头，点击上箭头默认增加一个step，点击下箭头默认会减少一个step。number中默认step是1。也就是step=0.01,可以允许输入2位小数，并且点击上下箭头分别增加0.01和减少0.01。
假如step和min一起使用，那么数值必须在min和max之间。
看下面的例子：
<input type="number"step="3.1"min="1"/>
输入框可以输入哪些数字？
首先，最小值是1，那么可以输入1.0，第二个是可以输入（1+3.1）那就是4.1,以此类推，每次点击上下箭头都会增加或者减少3.1，输入其他数字无效。这就是step的简单介绍。
//问题三，去除input默认样式
input[type=number] {
-moz-appearance:textfield;
}
input[type=number]::-webkit-inner-spin-button,
input[type=number]::-webkit-outer-spin-button {
-webkit-appearance:none;
margin:0;
}

```
- ios 设置input 按钮样式会被默认样式覆盖

```

解决方式如下：
input,
textarea {
border: 0;
-webkit-appearance: none;
}
设置默认样式为none

```
- select 下拉选择设置右对齐

```

设置如下：
select option {
direction: rtl;
}

```
- 通过transform进行skew变形，rotate旋转会造成出现锯齿现象

```

可以设置如下：
-webkit-transform: rotate(-4deg) skew(10deg) translateZ(0);
transform: rotate(-4deg) skew(10deg) translateZ(0);
outline: 1px solid rgba(255,255,255,0)

```
- 关于 iOS 与 OS X 端字体的优化(横竖屏会出现字体加粗不一致等)

```

iOS 浏览器横屏时会重置字体大小，设置 text-size-adjust 为 none 可以解决 iOS 上的问题，
但桌面版 Safari 的字体缩放功能会失效，因此最佳方案是将 text-size-adjust 为 100% 。
-webkit-text-size-adjust:100%;
-ms-text-size-adjust:100%;
text-size-adjust:100%;

```
- 移动端 HTML5 audio autoplay 失效问题

```

这个不是 BUG，由于自动播放网页中的音频或视频，会给用户带来一些困扰或者不必要的流量消耗，所以苹果系统和安卓系统通常都会禁止自动播放和使用 JS 的触发播放，必须由用户来触发才可以播放。
解决方法思路：先通过用户 touchstart 触碰，触发播放并暂停（音频开始加载，后面用 JS 再操作就没问题了）。
document.addEventListener('touchstart',function() {
document.getElementsByTagName('audio')[0].play();
document.getElementsByTagName('audio')[0].pause();
});

```
- 移动端 HTML5 input date 不支持 placeholder 问题

```

这个我感觉没有什么好的解决方案，用如下方法
<input placeholder="Date" class="textbox-n" type="text" onfocus="(this.type='date')"  id="date">

```
- 部分机型存在type为search的input，自带close按钮样式修改方法

```

有些机型的搜索input控件会自带close按钮（一个伪元素），而通常为了兼容所有浏览器，我们会自己实现一个，此时去掉原生close按钮的方法为:

# Search::-webkit-search-cancel-button{

display:none;
}
如果想使用原生close按钮，又想使其符合设计风格，可以对这个伪元素的样式进行修改。

```
- 唤起select的option展开

```

//zepto方式:
$(sltElement).trigger("mousedown");
//原生js方式:
functionshowDropdown(sltElement) {
varevent;
event = document.createEvent('MouseEvents');
event.initMouseEvent('mousedown',true,true, window);
sltElement.dispatchEvent(event);
};



页面滑动失去惯性，即当我们滑动页面后松开手指，页面会立即停止

**-webkit-overflow-scroll:touch解决滑动无惯性**  





### js处理img标签加载图片失败，显示默认图片



1.第一种方法： 
如果已经引入了jquery插件，就很好办。没有的话，如果实在需要，可以附上代码：

```
    script(type='text/javascript', src="http://ajax.googleapis.com/ajax/libs/jquery/1.8.0/jquery.min.js")
    //这是jade文件的写法，可以自行转换为html



handle error
    $('img').error(function(){
            $(this).attr('src', "default.jpg(默认图片的url地址)");
      });123456789

```

2.第二种方法：如果img标签是少量的话，可以用这个： 
img的onerror事件

```
<img src='test.jpg' alt='test' onerror="this.src='default.jpg'">
//alt属性的意思是在图片为加载成功时显示的文字
```




### 字体颜色渐变设置

```
background: linear-gradient(to right, dodgerBlue, skyBlue);
-webkit-background-clip: text;
```



### CSS 强制不换行，多出的文字显示省略号

```
{
  white-space: nowrap; //文本强制不换行；
  text-overflow:ellipsis; //文本溢出显示省略号；
  overflow:hidden; //溢出的部分隐藏；
}
```

