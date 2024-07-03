---
title: "React常发生的坑"
description: 
date: 2024-07-03T16:36:49+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - react
---



## react的例子

关于react的核心思想：当状态（）确定了就确定了视图即声明式布局，为了好维护，传统的开发是命令式

```
[变量，函数]
//且不能放在循环和if里，只能放在顶层
const [count, setCount] = useState(0)
```

setCount允许有两种参数，一种是直接传进一个变量，就是下一次要修改的值。

一种是函数，它会将原来的变量值传入这个函数内，下次更新函数返回的值



### 例子1

关于set*，setcount会进行合并，之后再进行对count赋值

```jsx
const App = () => { 	
	const [count, setCount] = useState(0)
    const [count2, setCount2] = useState(0)

    function get(x) {
        console.log(x)
        return x + 1
    }

    // set  之后 他不是马上更新视图的， 他会合并一些状态，　一次性更新　
    const add = () => {
        // setCount((count) => count + 1)
        for (let i = 0; i < 10; i++) {
            setCount(get)
        }
        //错误演示
        for (let i = 0; i < 10; i++) {
            setCount2(count + 2)
        }
    }
    return <div>
        <button onClick={() => {
            add()
        }}>
        
            CLICK ME {count}
            CLICK ME {count2}
        </button>
    </div>
}
```



### useRef

```
const count = useRef(0) //只能保存，没有能使重新渲染
只有set*才能使重新渲染

```

关于const ref = useRef(null)的使用

本身是一个对象，传递一个指针，为了防止冲突

```
const ref = useRef(null)
 return <div style={{ aspectRatio: '4/3', outline: '2px solid black' }} ref={ref}/>
 冲突例子：如果引入的包中有id也命名为main时，就会发生冲突
  return <div style={{ aspectRatio: '4/3', outline: '2px solid black' }} id='main'/>
```

也可以作为状态使用，跟随组件的生命周期，如果组件被初始化

```
const ins = useRef<EChartsType | null>()
```

```
//监听某一个元素宽高的变化
const ob = useRef<ResizeObserver>()
//监听某一个元素是否出现在屏幕内（isIntersecting）
const ob2 = useRef<IntersectionObserver>()
```







### 关于useRef无法渲染的例子

```jsx

//成功例子
const App = () => {
    const count = useRef(0)
    const [_, setState] = useState(0)
    const add = () => {
        count.current++
    }
    
    //利用setState来渲染
    const render = () => {
        setState(c => c + 1)
    }

    return <div>
        <button onClick={() => {
            add()
        }}>
            CLICK ME
            {count.current}
        </button>
        <button onClick={render}>
            RENDER
        </button>
    </div>
}


//无法渲染例子
const App = () => {
    const count = useRef(0)
    const [_, setState] = useState(0)
    const add = () => {
        count.current++
    }
  
    return <div>
        <button onClick={() => {
            add()
        }}>
            CLICK ME
            {count.current}
        </button>
       
    </div>
}
```

所以有了useForcesUpdate，可以强制渲染

```
const useForcesUpdate = () => {
    const [_, setCount] = useState(0)
    return () => setCount(c => c + 1)
}
```



### 关于list更新的例子

```jsx
//失败例子
const App = () => {
    const [list, setList] = useState([] as number[])


    const push1 = () => {
        setList((list) => {
            list.push(1)
            return list
        })

    }


    return <div>
        <button onClick={() => {
            push1()
        }}>
            CLICK ME
        </button>
        {JSON.stringify(list)}
    </div>
}

// return list后，react判断前后的值是一样的，他就不会重新渲染，我们需要一个中间量让react知道我们改变了， return [...list]类似于复制操作，改变了值，react会重新渲染

//成功的例子
const App = () => {
    const [list, setList] = useState([] as number[])


    const push1 = () => {
        setList((list) => {
            list.push(1)
            return [...list]
        })

    }


    return <div>
        <button onClick={() => {
            push1()
        }}>
            CLICK ME
        </button>
        {JSON.stringify(list)}
    </div>
}


//另一种方法，因为react是只要有set*发生改变，就会渲染，不管是谁（这就是react的不问数据来源）
const useForcesUpdate = () => {
    const [_, setCount] = useState(0)
    return () => setCount(c => c + 1)
}

const App = () => {
    const [list, setList] = useState([] as number[])
    const forceUpdate = useForcesUpdate()


    const push1 = () => {
        list.push(1)
        forceUpdate()

    }


    return <div>
        <button onClick={() => {
            push1()
        }}>
            CLICK ME
        </button>
        {JSON.stringify(list)}
    </div>
}
```





```jsx
const useForcesUpdate = () => {
    const [_, setCount] = useState(0)
    return () => setCount(c => c + 1)
}


const list = []
const Counter = () => {
    const forceUpdate = useForcesUpdate()

    return <div>
        <button onClick={() => list.push(1)}>
            PUSH
        </button>
        
        //这一个forceUpdate的
        <button onClick={forceUpdate}>
            Render
        </button>
        {JSON.stringify(list)}
    </div>
}


const App = () => {

    const forceUpdate = useForcesUpdate()


    return <div>
        <button onClick={forceUpdate}>Render</button>
        <Counter></Counter>
        <Counter></Counter>
    </div>
}
```





## 路由



```jsx
const Nav = () => {
    return <div>
        //主动去跳转路由
        <Link to={'/'}>root</Link>
        <Link to={'/demo2'}>demo2</Link>
        <Link to={'/demo3'}>demo3</Link>
    </div>
}

对应下面的element={<Layout/>}
const Layout = () => {
    const location = useLocation();
    const navigate = useNavigate();
    const [count, setCount] = useState([])

    return <div>
        {/*这里是页头*/}
        <div>
            HEADER
        </div>
          {/*此时的Outlet对应了下面的RootLayout或CommonLayout*/}
        <Outlet/>
        <div>
            FOOTER
        </div>
        {/*这里是页脚*/}
    </div>
}

const permissions = [
    'user:add'
]
//用来判断用户是否有某些权限
const hasPermission = (name) => {
    return userStore.role == 'root' || permissions.includes(name)
}
const RootLayout = () => {
    return <div style={{ display: "flex" }}>
        <div style={{ width: 300 }}>
            这里是root侧边栏
        </div>
        <div style={{ flex: 1 }}>
            
            {/* 此对应的是<Route path={'/'} element={<div/>}></Route>
                        <Route path={'/demo2'} element={<App2/>}></Route>
                        <Route path={'/demo3'} element={<App3/>}></Route>*/}
            <Outlet/>
        </div>
    </div>
}
const CommonLayout = () => {
    return <div style={{ display: "flex" }}>
        <div style={{ width: 300 }}>
            这里是common 侧边栏
        </div>
        <div style={{ flex: 1 }}>
            {/* 此对应的是<Route path={'/'} element={<div/>}></Route>
                        <Route path={'/demo2'} element={<App2/>}></Route>
                        <Route path={'/demo3'} element={<App3/>}></Route>*/}
            <Outlet/>
        </div>
    </div>
}
// page:user_add
// page:user_delete
// menu:system
// menu:user
// menu:product

// operation:get
// operation:update 这个不给
//


// http://xxxxx.com/path
const App = () => {
    // autorun + set...\
    const forceUpdate = useForcesUpdate()

    useEffect(() => {
        return autorun(() => {
            userStore.permissions
            forceUpdate()
        })
    }, [])
    return <div>
        {/*这是是用来给下面的Routes提供上下文的， 也就是说 Routes 放在这个里面才能正常工作 */}
        <HashRouter>
            <Nav></Nav>
            <Routes>
                //嵌套路由
                <Route path={'/'} element={<Layout/>}>
                    <Route path={'/'} element={hasPermission('root') ? <RootLayout/> : <CommonLayout/>}>
                        <Route path={'/'} element={<div/>}></Route>
                        <Route path={'/demo2'} element={<App2/>}></Route>
                        <Route path={'/demo3'} element={<App3/>}></Route>
                    </Route>
                </Route>
                <Route path={'/api2'} element={<Layout/>}>
                    <Route path={'/'} element={<div/>}></Route>
                    {
                        // 条件判断是否渲染路由
                        hasPermission("user:add") ? <Route path={'/demo2'} element={<App2/>}></Route> : null
                    }
                    <Route path={'/demo3'} element={<App3/>}></Route>

                </Route>
            </Routes>
        </HashRouter>
    </div>
}

```



### 关于页面跳转

~~~jsx
const goto = () => {
	navigate('/path/demo1')
}
~~~

