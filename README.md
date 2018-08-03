#### 一、使用CSS3动画代替JS动画
* JS动画频繁操作DOM导致效率非常低
* 在频繁的操作DOM和CSS时，浏览器会不停的执行重排（reflow）和重绘（repaint）
* 可以避免占用JS主线程

> 这边就不细说这两者具体的低优缺点。一般做2D动画的话,可以使用CSS3的transition或animation

#### 二、CSS3动画animation属性(可直接跳转<a href="#example" target="_self">三、实例</a>)
* 简写:
```
//模板
animation: name duration timing-function delay iteration-count direction fill-mode play-state;

//实例
animation: myAnimationName 2s linear 1s infinite forward;
```

* 细说
```
animation-name(必填)              //规定动画名称

animation-duration(必填)          //完成动画的时间
//值：time(1s.2s...)                默认值为 0，意味着没有动画效果
    
animation-timing-function(常用)   //动画运动的速度
/*
  值： linear                       匀速
       ease(默认)                   变速(先慢后快,再由快变慢) 
       ease-in                      低速开始
       ease-out                     低速结束
       ease-in-out                  低速开始和结束
       cubic-bezier(n,n,n,n)        在 cubic-bezier 函数中自己的值。可能的值是从 0 到 1 的数值
*/

animation-delay                     //动画在启动前的延迟间隔
//值：time   默认值为 0

animation-iteration-count(常用)     //动画的播放次数
/*
  值：number                        默认值1
      infinite                      动画无限次播放
*/

animation-direction                 //是否轮流反向播放动画
/*
  值:  normal                       正常
       reverse                      反向播放
       alternate                    在奇数次（1、3、5...）正向播放，在偶数次（2、4、6...）反向播放
       alternate-reverse            在奇数次（1、3、5...）反向播放，在偶数次（2、4、6...）正向播放
*/

animation-fill-mode(常用)           //当动画不播放时当动画完成时.要处于什么状态
/*
值：    none                        默认，播放完动画后，画面停在起始位置
       forwards                    播放完动画，停在animation定义的最后一帧(保持最后一个属性值)
       backwards                   如果设置了animation-delay，在开始到delay这段时间，画面停在第一帧。如果没有设置delay，画面是元素设置的初始值
*/

animation-play-state                //动画是否正在运行或已暂停
/*
值：   paused                       指定暂停动画
       running                      指定正在运行的动画，默认
*/
```

#### <span id="example">三、两个案例:CSS3实现弹窗、轮播</span>
##### 1. CSS3实现弹窗效果
   * 效果图  
   ![CSS3实现弹窗效果](https://images2018.cnblogs.com/blog/1414709/201808/1414709-20180803102815243-273965852.gif)

   * 代码实现
```
//html
<body>
    <div class="btnDiv">
        <button class="openPopUpBtn" id="openBtn">弹窗</button>     
    </div>

    <div class="popUp">
        <p>我是弹窗内容</p>
        <button class="closePopUpBtn" id="closeBtn">关闭</button>
    </div>
</body>

//css
/*button样式*/
    .btnDiv{
        text-align: center;
    }
    .openPopUpBtn,.closePopUpBtn{
        width:60px;
        height:40px;
        line-height:40px;
        border:1px solid #c9c9c9;
        background-color: #FFF;
        box-shadow: 1px 1px 1px 1px #eee;
    }
    .openPopUpBtn{
        margin-top:50px;
    }
    .openPopUpBtn:hover,.closePopUpBtn:hover{
        background-color: #eee;
        cursor:pointer;
    }

    /*弹框样式*/
    .popUp{
        width:600px;
        height:500px;
        text-align: center;
        background-color: #FFFeee;
        border:1px solid #ccc;
        box-shadow: 1px 1px 1px 1px #eee;            
        position:absolute;
        left:50%;
        margin-left:-300px;
        opacity: 0; /* 透明度为0 */
    }

    .closePopUpBtn{
        position: absolute;
        bottom:20px;
        left:50%;
        transform:translate(-50%);
    }
```
html结构比较简单,一个按钮、一个弹窗。样式大家可以自行选择,这部分不是重点.

**下面是实现的核心！！！！** ⬇️   

我们先看JS部分,仅仅只是实现点击功能,不做JS动画
```
window.onload = function(){
    var popUp = document.getElementsByClassName('popUp'); //获取div.popUp的DOM节点,注意！popUp此时是一个数组
    var openBtn = document.getElementById('openBtn');     //获取点击按钮的DOM节点
    var closeBtn = document.getElementById('closeBtn');   //获取弹框里面关闭按钮的DOM节点

    //点击弹窗按钮触发事件
    openBtn.onclick = function(){
         //给div.popUp添加一个名叫showCont的id,随后该id执行对应动画
         popUp[0].setAttribute('id','showCont');
    }

    //点击关闭按钮触发事件
    closeBtn.onclick = function(){
         //给div.popUp添加一个名叫hiddenCont的id,随后该id执行对应动画   
         popUp[0].setAttribute('id','hiddenCont');
    }

}
```

再来看一下重点CSS3动画部分
```
/*定义CSS样式*/
    /*定义打开弹窗动画*/
    #showCont{
        animation: showPopUp 1s;
        animation-fill-mode: forwards; /*保持动画后的状态*/
    }
    
    /*定义关闭弹窗动画*/
    #hiddenCont{
        animation: hiddenPopUp 1s;
        animation-fill-mode: forwards;
    }

/*定义动画规则*/
    //打开弹窗动画
@keyframes showPopUp{
    0%{
        opacity: 0;
        top:-60%;
        transform: rotateZ(0deg);
        //初始透明度为0,位于顶部-60%位置,旋转Z轴为0
    }
    50%{
        transform: rotateZ(-10deg);
        //动画进行一半的时候,规定旋转Z轴为-10度
        //此时透明度和顶部位置都会自动计算
    }
    100%{
        opacity: 1;
        top:20%;
        transform: rotateZ(0deg);
        //动画结束后的最终参数
    }
}

    //关闭弹窗动画 和打开动画相反即可
@keyframes hiddenPopUp{
    0%{
        opacity: 1;
        top:20%;
        transform: rotateZ(0deg);
    }
    50%{
        transform: rotateZ(-10deg);
    }
    100%{
        opacity: 0;
        top:-60%;
        transform: rotateZ(0deg);
    }
}
```

至此我们第一个案例:CSS3动画实现弹窗就完成啦。

我们接下来看第二个案例:轮播图。这个案例比较简单 

**实现思路:**
1. 定义一个父级,规定宽度和高度只能显示一个轮播图大小,超出部分隐藏
```
//html
<div class="carousel"></div>

//css
.carousel{
    width:100px;
    height:100px;
    overflow: hidden;
}
```

2. 定义存放所有轮播图的盒子
```
//html
<div class="carousel">
    <div class="items"></div>
</div>

//css
.items{
    color:#FFF;
    width:420px;        //存放4个轮播图
    position:absolute;  //动画更改left达到轮播滚动的效果
    left:0px;
    animation:carouselMove 3s infinite; //定义动画相关属性
}
```

3. 定义动画
```
//通过不断改变items的位置,从而显示不同的轮播图,实现效果
//当动画结束之后,又返回初始值,可以无数循环
@keyframes carouselMove{
    0%{
        left:0px;
    }
    35%{
        left:-100px;
    }
    70%{
        left:-200px;
    }
    100%{
        left:-300px;
    }
}

```


完整代码:
```
//html
<div class="carousel">
    <div class="items">
        <div class="item1">轮播1</div>
        <div class="item2">轮播2</div>
        <div class="item3">轮播3</div>
        <div class="item1">轮播4</div>
    </div>
</div>

//css
.carousel{
    width:100px;
    height:100px;
    position:relative;
    left:400px;
    top:100px;
    overflow: hidden;
}
.items{
    color:#FFF;
    width:420px;
    position:absolute;
    left:0px;
    animation:carouselMove 3s infinite;
    animation-direction: normal;
    /*animation-timing-function:easy 规定动画运动的速度 animation-iteration-count:infinite 播放无数次*/
    -webkit-animation:carouselMove 3s infinite;
    -moz-animation-direction: normal;
}
.item1,.item2,.item3{
    width:100px;
    height:100px;
    float:left;
}
.item1{
    background-color: #12B7F5;
}
.item2{
    background-color: #F9B041;
}
.item3{
    background-color: #CCCCCC;
}
```
```
//定义动画
@keyframes carouselMove{
    0%{
        left:0px;
    }
    35%{
        left:-100px;
    }
    70%{
        left:-200px;
    }
    100%{
        left:-300px;
    }
}
```

以上就是CSS3动画的部分知识啦！谢谢！  
需要源码的可以到这里下载:  
https://github.com/soybeanxiaobi/css3-

