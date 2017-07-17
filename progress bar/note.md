    <div class = "loading"> //覆盖层
        <div class = "pic"> //loading图片/动画
        </div>
    </div>

#一、用定时器的方法实现加载
        $(function(){
            setInterval(function(){
                $(".loading").fadeOut();
            },3000)
        })
获取loading图片的网站：https://preloaders.net/
#二、通过加载状态事件实现加载
        document.onreadystatechange = function(){
            if(document.readyState == "complete"){
                $(".loading").fadeOut();
            }
        }
document.onreadystatechange 页面加载状态改变时的事件

document.readyState 返回当前文档的状态

1. uninitialized 还未开始载入
2. loading 载入中
3. interactive 已加载，文档与用户可以开始交互
4. complete 载入完成

#三、css3进度条小动画
主要依靠 transform,animation,@keyframes实现

可以将代码兼容的网站：http://autoprefixer.github.io/

loading动画直接生成css的网站:https://loading.io/

#四、定位在头部的加载
通过向特定的一段html后面加入

    <script>
        $(".loading .pic").animate({width:"30%"},100)
    </script>

最后

    $(".loading .pic").animate({width:"100%"},100,function(){
        $(".loading").fadeOut();
    })

#五、实时获取加载数据的进度条
    $(function(){
        var img = $("img");
        var num = 0;
        
        img.each(function(i){
            var oImg = new Image(); //建立图像对象

            oImg.onload = function(){
                oImg.onload = null;
                num++;
                $(".loading b").html(parseInt(num/$("img").size()*100)+"%");
                if(num>=i){
                    $(".loading").fadeOut();
                }
            }
            oImg.src = img[i].src; //src属性一定要写到onload的后面，否则程序在IE中会出错
        });
    })