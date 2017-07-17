#1.后台分页（异步）
jq Ajax

	  $(document).ready(function () {
	    $.ajax({
	      url: url, //必需。规定把请求发送到哪个 URL
	      type: 'GET',  //提交类型：GET POST
	      dataType: 'dataType',  //可选。规定预期的服务器响应的数据类型。默认执行智能判断（xml、json、script 或 html）。
		  data:data,  //可选。规定连同请求发送到服务器的数据。
	      success: function (data) {
	
	
	      },
	
	      error: function (error) {
				alert(error);
	      }
	
	    });
	  })

> 他那边的数据是一个个写好了的（比如page=1），比如一个页面20个图片，然后第一页就是20个图片地址，我们传入地址url（url+page=1）获取里面的图片地址，再把原来div中的图片地址用js替换（像推书那样）。

#2.js实现加载
> 首先，在页面中准备一个id为div1的div,在这个div中放一个ul,ul中准备了一些li,然而这些li都有一个data-src的属性，准备着图片的地址，具体结构如下：

	<div id="div1">
		<ul>
			<li data-src="img/w.png"></li>
			<li data-src="img/w.png"></li>
			<li data-src="img/w.png"></li>
			<li data-src="img/w.png"></li>
			<li data-src="img/w.png"></li>
		</ul>
	</div>

> 图片的动态加载就是通过读取li元素，然后获得li元素的data-src属性的值赋予动态创建的图片的src，从而实现了图片的创建。

	function setImg(index){
	                var oDiv=document.getElementById("div1")
	                var oUl=oDiv.children[0];
	                var aLi=oUl.children;
	                if (aLi[index].dataset) {
	                    var src=aLi[index].dataset.src;
	                } else{
	                    var src=aLi[index].getAttribute('data-src');
	                }
	                var oImg=document.createElement('img');
	                oImg.src=src;
	                if (aLi[index].children.length==0) {
	                    aLi[index].appendChild(oImg); 
	                }
	            }

> 那么怎么识别是否在可视区域呢？这里需要一个函数，能够实现获得当前元素距离网页顶部的距离！

	//获得对象距离页面顶端的距离  
	function getH(obj) {  
	    var h = 0;  
	    while (obj != window.document.body&&obj != null) {  
	        h += obj.offsetTop;  
	        obj = obj.offsetParent;  
	    }  
	    return h;  
	}  //就是沿着dom树一层层往上找，把相对于offsetParent的偏移累加，直到body，这样就可以得到当前元素相对于顶端的偏移量。

> 当网页的滚动条滚动时要时时判断当前li的位置！

    window.onscroll = function () {
        var oDiv = document.getElementById('div1');
        var oUl = oDiv.children[0];
        var aLi = oUl.children;

        for (var i = 0, l = aLi.length; i < l; i++) {
            var oLi = aLi[i];
            //检查oLi是否在可视区域
            var t = document.documentElement.clientHeight + (document.documentElement.scrollTop || document.body.scrollTop);
            var h = getH(oLi);
            if (h < t) {
                setTimeout("setImg(" + i + ")", 500);
            }
        }
    }; // document.documentElement.clientHeight ==> 可见区域高度

> 当页面加载完成以后要主动运行一下window.onscroll，从而获得当前可视范围内的图片

> 另外，像这样的页面，障眼法和美化都是必须的，比如给每个li一个loading的图片作为背景，从而实现了当前图片正在加载的效果，美化工作就是做好合理的布局。

    * {
        margin: 0;
        padding: 0;
    }

    #div1 {
        width: 520px;
        margin: 30px auto;
        border: 1px solid red;
        overflow: hidden;
    }

    li {
        width: 160px;
        height: 160px;
        float: left;
        list-style: none;
        margin: 5px;
        background: url(loading.gif) center center no-repeat;
        border: 1px dashed green;
    }
    img{
    width:100%
    }

> data- ,可以设置我们需要的自定义属性。如果想获取某个属性的值：

	如 data-src:dataset.src
	注意：带连字符连接的名称在使用的时候需要命名驼峰化： data-meal-time ：dataset.mealTime

#3.jq插件 lazyload

###使用方法
1. 引用jquery和jquery.lazyload.js到你的页面

	<script src="jquery-1.11.0.min.js"></script>
	<script src="jquery.lazyload.js?v=1.9.1"></script>

1. 为图片加入样式lazy  图片路径引用方法用data-original
	
	<img class="lazy" data-original="img/bmw_m1_hood.jpg">
	<img class="lazy" data-original="img/bmw_m1_side.jpg">
	<img class="lazy" data-original="img/viper_1.jpg">
	<img class="lazy" data-original="img/viper_corner.jpg">
	<img class="lazy" data-original="img/bmw_m3_gt.jpg">
	<img class="lazy" data-original="img/corvette_pitstop.jpg">

1. js出始化lazyload并设置图片显示方式

	<script type="text/javascript" charset="utf-8">
	  $(function() {
	      $("img.lazy").lazyload({effect: "fadeIn"});
	  });
	</script>

在图片中也可以不使用 class="lazy"，初始化时使用：

	$("img").lazyload({effect: "fadeIn"});

这样就可以对全局的图片都有效！

###参数
	
	$("img.lazy").lazyload({
	  placeholder : "img/grey.gif", //用图片提前占位
	    // placeholder,值为某一图片路径.此图片用来占据将要加载的图片的位置,待图片加载时,占位图则会隐藏
	  effect: "fadeIn", // 载入使用何种效果
	    // effect(特效),值有show(直接显示),fadeIn(淡入),slideDown(下拉)等,常用fadeIn
	  threshold: 200, // 提前开始加载
	    // threshold,值为数字,代表页面高度.如设置为200,表示滚动条在离目标位置还有200的高度时就开始加载图片,可以做到不让用户察觉
	  event: 'click',  // 事件触发时才加载
	    // event,值有click(点击),mouseover(鼠标划过),sporty(运动的),foobar(…).可以实现鼠标莫过或点击图片才开始加载,后两个值未测试…
	  container: $("#container"),  // 对某容器中的图片实现效果
	    // container,值为某容器.lazyload默认在拉动浏览器滚动条时生效,这个参数可以让你在拉动某DIV的滚动条时依次加载其中的图片
	  failurelimit : 10 // 图片排序混乱时
	     // failurelimit,值为数字.lazyload默认在找到第一张不在可见区域里的图片时则不再继续加载,但当HTML容器混乱的时候可能出现可见区域内图片并没加载出来的情况,failurelimit意在加载N张可见区域外的图片,以避免出现这个问题.
	});
