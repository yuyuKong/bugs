## [web前端性能优化总结](https://blog.csdn.net/ma_hoking/article/details/51472697)

### **浏览器访问优化**

##### 浏览器请求处理流程如下图：

![](D:\Pictures\20160521221948295.jpg)

![http://yun.janesi.net/web-test/zhiqu-web/img/20160521221948295.jpg]()





#### **1、减少http请求，合理设置 HTTP缓存**

```
  http协议是无状态的应用层协议，意味着每次http请求都需要建立通信链路、进行数据传输，而在服务器端，每个http都需要启动独立的线程去处理。这些通信和服务的开销都很昂贵，减少http请求的数目可有效提高访问性能。

        减少http的主要手段是合并CSS、合并javascript、合并图片。将浏览器一次访问需要的javascript和CSS合并成一个文件，这样浏览器就只需要一次请求。图片也可以合并，多张图片合并成一张，如果每张图片都有不同的超链接，可通过CSS偏移响应鼠标点击操作，构造不同的URL。
```



#### 2、使用浏览器缓存

```
  对一个网站而言，CSS、javascript、logo、图标这些静态资源文件更新的频率都比较低，而这些文件又几乎是每次http请求都需要的，如果将这些文件缓存在浏览器中，可以极好的改善性能。通过设置http头中的cache-control和expires的属性，可设定浏览器缓存，缓存时间可以是数天，甚至是几个月
```



#### 3、启用压缩

```
在服务器端对文件进行压缩，在浏览器端对文件解压缩，可有效减少通信传输的数据量。如果可以的话，尽可能的将外部的脚本、样式进行合并，多个合为一个。文本文件的压缩效率可达到80%以上，因此HTML、CSS、javascript文件启用GZip压缩可达到较好的效果。但是压缩对服务器和浏览器产生一定的压力，在通信带宽良好，而服务器资源不足的情况下要权衡考虑
```



#### 4、CSS Sprites（图片精灵）

```
合并 CSS图片，减少请求数的又一个好办法。
```



#### **5、LazyLoad Images**(图片懒加载)

```
这条策略实际上并不一定能减少 HTTP请求数，但是却能在某些条件下或者页面刚加载时减少 HTTP请求数。对于图片而言，在页面刚加载的时候可以只加载第一屏，当用户继续往后滚屏的时候才加载后续的图片。这样一来，假如用户只对第一屏的内容感兴趣时，那剩余的图片请求就都节省了
```

- ##### echo.js方式

  ##### 下载地址：<https://github.com/helijun/helijun/tree/master/plugin/echo>

  ```
  <div class="demo">
      <img class="lazy" src="images/blank.gif" data-echo="images/big-1.jpg">
  </div>
  blank.gif是一张背景图片，包含在插件里了。图片的宽高必须设定（提高用户体验），当然，可以使用外部样式对多张图片统一控制大小。data-echo指向的是真正的图片地址
  ```

  ```
  <script src="js/echo.min.js"></script>

  <script>

  Echo.init({
      offset: 0,//离可视区域多少像素的图片可以被加载
  　　 throttle: 0 //图片延时多少毫秒加载
  }); 

  </script>
  ```

  ​

- ##### jqueryLazyload方式

  下载地址：<https://github.com/helijun/helijun/blob/master/plugin/lazyLoad/jquery.lazyload.js>

  ```
  <section class="module-section" id="container">
       <img class="lazy-load" data-original="../static/img/loveLetter/teacher/teacher1.jpg" width="640" height="480" alt="测试懒加载图片"/>
  </section>
  ```

  ```
  require.config({
      baseUrl : "/static",
      paths: {
          jquery:'component/jquery/jquery-3.1.0.min'
          jqueryLazyload: 'component/lazyLoad/jquery.lazyload',//图片懒加载
      },
      shim: {
          jqueryLazyload: {
              deps: ['jquery'],
              exports: '$'
          }
      }
  });
  ```

  ```
  require(
      [
          'jquery',
          'jqueryLazyload'
      ], 
      function($){
          $(document).ready(function() {     
              $("img.lazy-load").lazyload({ 
  　　　　　　　　　　effect : "fadeIn", //渐现，show(直接显示),fadeIn(淡入),slideDown(下拉)
  　　　　　　　　　　threshold : 180, //预加载，在图片距离屏幕180px时提前载入
  　　　　　　　　　　event: 'click',  // 事件触发时才加载，click(点击),mouseover(鼠标划过),sporty(运动的),默认为scroll（滑动）
  　　　　　　　　　　container: $("#container"), // 指定对某容器中的图片实现效果
  　　　　　　　　　　failure_limit：2 //加载2张可见区域外的图片,lazyload默认在找到第一张不在可见区域里的图片时则不再继续加载,但当HTML容器混乱的时候可能出现可见区域内图片并没加载出来的情况
  　　　　　　　　}); 
  　　　　　　});
  　　});　　
  ```


#### **6、CSS放在页面最上部，javascript放在页面最下面**

```
 	浏览器会在下载完成全部CSS之后才对整个页面进行渲染，因此最好的做法是将CSS放在页面最上面，让浏览器尽快下载CSS。
```

```
 Javascript则相反，浏览器在加载javascript后立即执行，有可能会阻塞整个页面，造成页面显示缓慢，因javascript最好放在页面最下面。但如果页面解析时就需要用到javascript，这时放到底部就不合适了。
```



#### **7、异步请求Callback（就是将一些行为样式提取出来，慢慢的加载信息的内容）**



#### **8、减少cookie传输**

```
一方面，cookie包含在每次请求和响应中，太大的cookie会严重影响数据传输，因此哪些数据需要写入cookie需要慎重考虑，尽量减少cookie中传输的数据量。另一方面，对于某些静态资源的访问，如CSS、script等，发送cookie没有意义，可以考虑静态资源使用独立域名访问，避免请求静态资源时发送cookie，减少cookie传输次数
```



#### **9、Javascript代码优化**

- **DOM　**

  ```
  a.HTML Collection（HTML收集器，返回的是一个数组内容信息） 
  　　在脚本中 document.images、document.forms、getElementsByTagName()返回的都是HTMLCollection类型的集合，在平时使用的时候大多将它作为数组来使用，因为它有 length属性，也可以使用索引访问每一个元素。不过在访问性能上则比数组要差很多，原因是这个集合并不是一个静态的结果，它表示的仅仅是一个特定的查询，每次访问该集合时都会重新执行这个查询从而更新查询结果。所谓的“访问集合” 包括读取集合的 length属性、访问集合中的元素。 
  　　因此，当你需要遍历 HTML Collection的时候，尽量将它转为数组后再访问，以提高性能。即使不转换为数组，也请尽可能少的访问它，例如在遍历的时候可以将 length属性、成员保存到局部变量后再使用局部变量。　　 
  b. reflow（回流）和repaint（重绘）　 
  　　DOM操作还需要考虑浏览器的Reflow和Repaint ，因为这些都是需要消耗资源的。
  ```

  ​

- **避免使用 eval和 Function**

  ```
  每次 eval 或Function 构造函数作用于字符串表示的源代码时，脚本引擎都需要将源代码转换成可执行代码。这是很消耗资源的操作 —— 通常比简单的函数调用慢 100倍以上。 
  　　eval 函数效率特别低，由于事先无法知晓传给 eval 的字符串中的内容，eval在其上下文中解释要处理的代码，也就是说编译器无法优化上下文，因此只能有浏览器在运行时解释代码。这对性能影响很大。 
  　　Function 构造函数比 eval略好，因为使用此代码不会影响周围代码 ;但其速度仍很慢。 
  　　此外，使用 eval和 Function也不利于Javascript 压缩工具执行压缩。
  ```

  ​

- **数据访问**

  ```
  Javascript中的数据访问包括直接量 (字符串、正则表达式 )、变量、对象属性以及数组，其中对直接量和局部变量的访问是最快的，对对象属性以及数组的访问需要更大的开销。当出现以下情况时，建议将数据放入局部变量： 
  　　a. 对任何对象属性的访问超过 1次 
  　　b. 对任何数组成员的访问次数超过 1次 
  　　另外，还应当尽可能的减少对对象以及数组深度查找
  ```

  ​

- **CSS选择符优化**

  ```
  在大多数人的观念中，都觉得浏览器对 CSS选择符的解析式从左往右进行的，例如 
  #toc A { color: #444; }这样一个选择符，如果是从右往左解析则效率会很高，因为第一个 ID选择基本上就把查找的范围限定了，但实际上浏览器对选择符的解析是从右往左进行的。如上面的选择符，浏览器必须遍历查找每一个 A标签的祖先节点，效率并不像之前想象的那样高
  ```

  ​

- **CDN加速**

  ```
  CDN（contentdistribute network，内容分发网络）的本质仍然是一个缓存，而且将数据缓存在离用户最近的地方，使用户以最快速度获取数据，即所谓网络访问第一跳
  ```

  ![http://yun.janesi.net/web-test/zhiqu-web/img/20160521222226952.jpg]()

  ![](D:\Pictures\20160521222226952.jpg)

  ```
  由于CDN部署在网络运营商的机房，这些运营商又是终端用户的网络服务提供商，因此用户请求路由的第一跳就到达了CDN服务器，当CDN中存在浏览器请求的资源时，从CDN直接返回给浏览器，最短路径返回响应，加快用户访问速度，减少数据中心负载压力。 
  CDN缓存的一般是静态资源，如图片、文件、CSS、script脚本、静态网页等，但是这些文件访问频度很高，将其缓存在CDN可极大改善网页的打开速度。
  ```

  ​

  ​

  ​

  ​

  ​