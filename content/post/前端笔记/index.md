---
title: "前端笔记"
description: 
date: 2024-07-03T16:22:56+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - react
tags:
    - css
    - flex
    - transition
    - border
---



# 关于前端的知识点

### 撑满屏幕高度

```html
height: 100vh;
```

```css
calc可以动态计算尺寸
style={{ minWidth: 'calc(1920px * 0.8)' }}
```

```css
根据设置调整子元素 child 的宽高比
aspectRatio: '1/1'
```



## border



### borderRadius: 百分比和像素的区别

borderRadius在设置宽高一致的时候50%（像素则是半径）才是正园，否则是椭圆。在设置borderRadius圆角时最好使用像素来设置

~~~ css
 <div style={{borderRadius: 25,}}>
~~~



### 关于border和outline的区别

border是即存在又显示，outline是显示但不存在，在一些情况下可能会影响元素的尺寸或者位置，而outline不会影响

```jsx
//错误例子
<div style={{ display: "flex", flexWrap: "wrap" }}>

    <div style={{ width:'50% ',border: '20px solid red', height: 200 }}></div>
    <div style={{ width:'50% ',border: '20px solid red', height: 200 }}></div>
    
</div>
//以下为效果
```

![image-20240610160603069](img/image-20240610160603069.png)

<div style={{ display: "flex", flexWrap: "wrap" }}>

```jsx
换成outline则不会出现换行，因为outline不占宽高
<div style={{ display: "flex", flexWrap: "wrap" }}>

    <div style={{ width:'50% ',outline: '20px solid red', height: 200 }}></div>
    <div style={{ width:'50% ',outline: '20px solid red', height: 200 }}></div>
    
</div>
```



## 动画

### transition

可以设置动画过渡时间

```jsx
transition: 'all 300ms',动画时间
```



### transform: translate*

 ~~~jsx
 transform: translateX(800px);//实现一个向右移动的800px的效果，translateY同理
 ~~~

### transform在不同位置translateX值的算法不同

```jsx
transform: `translateX(${-counter * 100/images.length}%)` }}>
```

```
translate*关于百分比，它是谁的百分比？他自己本身div的百分比
```

### 延申---相对百分比的计算

width:相对父容器width
height：相对父容器height
margin：相对父容器。。。
padding-*：相对本身width
padding-bottom: 相对本身width
translateX => 相对本身width
translateY =>相对本身 height



### css原生的动画

```css
.jump {
    /*infinite 循环*/
    /*inf */
    backwards为默认值（结束后回到起点）】
    forwards结束后不回，停止在终点
    ease-in-out
    ease*可以改变运动时的加速度（做变速运动）
    linear（做匀速运动）
    //创建一个动画 名为jump（@keyframes jump）
    animation: jump 3000ms infinite ease-in-out;

}

@keyframes jump {
    0% {
        transform: translateX(800px);
    }
    50% {
        transform: translateX(-10px);
    }
    100% {
        transform: translateX(800px);
    }
}
```



### js的原生的动画

关于transform是覆盖的，比如以下代码，只在最后设置了transform则动画是从0~1中间缓缓向translateX(200px)  translateY(100px) scale(2)变化

~~~js
 {
                background: 'black',
              
                offset: 0
            },
            {
              
                offset: 0.3
            },
            {
              
                background: 'red',
                offset: 0.6
            },
            {
                transform: 'translateX(200px)  translateY(100px) scale(2)',
                offset: 1,
                background: 'blu
                e',
            }
~~~



而以下从一开始就设置了transform，则层层覆盖，从0-0.3是向右移动100px大小不变。从0.3-0.6是向下移动100px（因为transform: 'translateX(100px)所以x上不会再该改变。）大小不变，从0.6之后大小会发生改变，并向右再次移动100px

~~~js
{
                background: 'black',
                transform: 'translateX(0)',
                offset: 0
            },
            {
                transform: 'translateX(100px) ',
                offset: 0.3
            },
            {
                transform: 'translateX(100px) translateY(100px) ',
                background: 'red',
                offset: 0.6
            },
            {
                transform: 'translateX(200px)  translateY(100px) scale(2)',
                offset: 1,
                background: 'blue',
            }
~~~



### 例子

```jsx
const AnimationWithJS = () => {
    const ref = useRef<HTMLDivElement>(null)

    const cur = useRef(1)

    // let cur = 1
    const fn = () => {
        const el = ref.current
        cur.current ^= 1
        // transform
        // translateX(100px) => translateX(100px) translateY(100px) => translateX(200px)  translateY(100px) scale(2)
        // '' => red => blue
        el?.animate([
            {
                background: 'black',
                transform: 'translateX(0)',
                offset: 0
            },
            {
                transform: 'translateX(100px) scale(1)',
                offset: 0.3
            },
            {
                transform: 'translateX(100px) translateY(100px) scale(1)',
                background: 'red',
                offset: 0.6
            },
            {
                transform: 'translateX(200px)  translateY(100px) scale(2)',
                offset: 1,
                background: 'blue',
            }
        ], {
            duration: 1000,
            easing: 'linear',
            // delay: 500,
            fill: 'forwards',
            direction: cur.current ? 'reverse' : 'normal'
        })
    }

    return <div>
        <button onClick={fn}>
            PLAY
        </button>
        <div ref={ref} style={{ width: 100, height: 100, background: "black" }}></div>
    </div>
}
```

## display: "flex"和colum

在display: "flex"的情况下justifyContent（主轴）默认是左右，alignItems（交叉轴）是上下

在colum的情况下justifyContent（主轴）默认是上下，alignItems（交叉轴）是左右



## 关于生成多个（div）

```
//关于生成多个（div）
{
Array.from({ length: 6 }).map((_, index) => {
    return <div style={{
        flex: '0 0 calc(100% / 3)',
        aspectRatio: '3/2',
        outline: '1px solid black',
        overflow: "hidden"
    }}>
    </div>
})
}
```



## 关于块居中的两个思路

```
margin: '0 auto'
  <div style={{ display: "flex", gap: 20, width: '80%',margin: '0 auto' }}>

   </div>
```

```jsx
<div style={{ display: "flex", flexDirection: "column", alignItems: "center", marginTop: 20 }}>
    <div style={{ display: "flex", gap: 20, width: '80%'}}>

    </div>
</div>
```



## 关于轮播图大小的细节

```

//如果不设置width: (100 * images.length) + '%'则所有图片的宽度总和为父元素的宽，设置后则是images.length个父元素的宽度大小
<div style={{ width: (100 * images.length) + '%', height: '100%', display: "flex" }}>
```



## 关于图层的定位

```
zIndex: 1,
```



## 关于设置不透明度来控制图片转换的画面流畅

```
//可以渲染出一个画面慢慢出现和慢慢消失的效果，转换更流畅
opacity: current ? 1 : 0}
```



有些操作需要通过套两层div来实现效果

```
<div class="transition" style={{
//做点击时
    width: size, height: size, position: 'relative', transform: `translateX(${current ? 0 : 100}%)`
}}>
    <div style={{height: '100%'}}
    //做预点击时
         class={`center transition ${current ? 'left' : 'right'}`}>
        <div style={{height: scale, width: scale, position: 'relative'}}>
            <img class="transition moon" style={{opacity: current ? 1 : 0}} src="./moon.svg"/>
            <img class="transition sun" style={{opacity: !current ? 1 : 0}} src="./sun.svg"/>
        </div>
    </div>
</div>
```





按钮文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        html, body {
            margin: 0;
            padding: 0;
        }

        .container:hover .left {
            transform: translateX(10px);
        }

        .container:hover .right {
            transform: translateX(-10px);
        }

        //做背景鼠标预点击处理背景的小移动
        .container:hover .top {
            transform: translateY(10px);
        }

        .container:hover .bottom {
            transform: translateY(-10px);
        }


        .container {
            border: 10px solid white;
            overflow: hidden;
            position: relative;
            cursor: pointer;
            user-select: none;
        }

        img {
            -webkit-user-drag: none;
        }

        .moon, .sun {
            width: 100%;
            height: 100%;
            position: absolute;
            z-index: 1;
            left: 0;
            top: 0;
        }

        .transition {
            transition: 300ms all;
        }

        .center {
            display: flex;
            justify-content: center;
            align-items: center;
        }
    </style>
</head>
<body>
<div id="app"></div>
</body>
<!--<script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>-->
<script src="https://cdn.staticfile.net/react/18.2.0/umd/react.production.min.js"></script>
<script src="https://cdn.staticfile.net/react-dom/18.2.0/umd/react-dom.production.min.js"></script>
<!--<script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>-->
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
<script type="text/babel">
    const {useState} = React
    const {render} = ReactDOM
    const size = 100
    const background = 'https://ts1.cn.mm.bing.net/th/id/R-C.6cf09e69f9f3fdf2c15f2c1954e9bf52?rik=TZbO4UZZbuQhBw&riu=http%3a%2f%2fimg.pconline.com.cn%2fimages%2fupload%2fupc%2ftx%2fphotoblog%2f1407%2f14%2fc3%2f36307617_36307617_1405352312543.jpg&ehk=qO9b8i%2bpDgBY9RryFAckE%2bSihFxKz41FZHH%2fnvyKcjo%3d&risl=&pid=ImgRaw&r=0'
    const scale = '80%'
    const SwitchButton = () => {
        const [current, setCurrent] = useState(false)
        const change = () => setCurrent(c => !c)
        return (
            <div class="container" onClick={change} style={{
                width: size * 2,
                height: size,
                borderRadius: size,
            }}>
                <div style={{position: 'absolute'}}>
                //做背景的预点击时的效果，判断是顶部还是底部确定（是 .container:hover .top还是 .container:hover .bottom），因为关于鼠标只能使用hover来监听
                    <div class={`transition ${current ? 'top' : 'bottom'}`}>
                        <img style={{
                            width: size * 2,
                            height: size * 2,
                            transform: `translateY(${current ? -size : 0}px)`
                        }}
                             class="transition"
                             src={background}/>
                    </div>
                </div>
                
                <div class="transition" style={{
                //做点击时
                    width: size, height: size, position: 'relative', transform: `translateX(${current ? 0 : 100}%)`
                }}>
                    <div style={{height: '100%'}}
                    //做图标预点击时效果，判断是 .container:hover .right还是 .container:hover .left
                         class={`center transition ${current ? 'left' : 'right'}`}>
                        <div style={{height: scale, width: scale, position: 'relative'}}>
                            <img class="transition moon" style={{opacity: current ? 1 : 0}} src="./moon.svg"/>
                            <img class="transition sun" style={{opacity: !current ? 1 : 0}} src="./sun.svg"/>
                        </div>
                    </div>
                </div>
            </div>
        )
    }
    const App = () => {
        return <div class={'center'} style={{
            background: 'black',
            height: '100vh',
            width: '100vw',
        }}>
            <SwitchButton/>
        </div>
    }

    render(<App/>, document.querySelector('#app'))
</script>
</html>
```







## 快捷键补充

ctrl+.可以快捷键收代码（收一个层级

lorem1000+tab可以生成1000个字符的字符串（指定多少个）

lorem + tab可以生成很多无意义的字符串