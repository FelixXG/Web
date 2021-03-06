## 场景
```
//写法一
import {Switch,Route,Router,HashHistory,Link} from 'react-router-dom'

//写法二
import {Switch,Route,Router} from 'react-router'
import {HashHistory,Link} from 'react-router-dom'

```

## `react-router`,`react-router-dom`和`react-router-native`

### `react-router`
提供了一些router的核心api,包括`Router`,`Route`,`Switch`等，但是它没有提供dom操作进行跳转的api,**服务器端渲染**正好可以。

### `react-router-dom`
基于`react-router`，加入了在浏览器运行环境下的一些功能，我们可以通过dom的事件控制路由。例如`Link`组件，会渲染一个`a`标签；`BrowserRouter`和`HahsRouter`组件，前者使用`pushState`和`popState`事件构建路由，后者使用`window.location.hash`和`hashchange`事件构建路由。

### `react-router-native`
基于`react-router`,类似`react-router-dom`,加入了`react-native`运行环境下的一些功能。

### 结论
`react-router-dom`依赖`react-router`，所以我们使用npm安装以来的时候，只需要安装相应环境下的库即可，不用再显式安装`react-router`。基于浏览器环境下的开发，只需要安装`raect-router-dom`;基于`react-native`环境的开发，只需要安装`react-router-native`。`npm`会自动解析`react-router-dom`包中`package.json`的依赖并安装。


## V4与之前版本路由跳转的区别

### `react-router v4使用history控制路由跳转`

在使用`react-router v3`时候，跳转路由如下：
- 从`react-router`导出`browserHistory`。
- 使用`browserHistory.push()`等操作路由跳转

但是在`react-router v4`中，不提供`browserHistory`等导出

#### 推荐使用`withRouter`
`withRouter`高阶组件，提供了`history`使用
```
import React from "react";
import {withRouter} from "react-router-dom";

class MyComponent extends React.Component {
    ...
    myFunction(){
        this.props.history.push("/some/path")
    }
    ...
}
export default withRouter(MyComponent)
```

## 改版后的`react-router-dom`路由，需要理解三个概念`Router`、`Route`和`Link`。

### `<Router>`
`<Router>`是所有路由组件共用的底层接口，一般我们的应用不会使用这个接口，而是使用高级的路由：
- `<BrowserRouter>`:使用HTML5提供的historyAPI来保持UI和URL的同步；
- `<HashRouter>`:使用URL的hash(例如：`window.location.hash`)来保持UI和URL同步
- `<MemoryRouter>`：能在内存保存你“URL”的历史记录（并没有对地址栏读写）;
- `<NativeRouter>`：为使用`React Native`提供路由支持
- `<StaticRouter>`: 从不会改变地址

### `<Link>`
Link是react路由中的点击切换到哪一个组件的链接，（这里只素衣不说是页面，而是说组件，因为切换到另一个界面只是展示效果，react的本质还是一个单页面应用-single page application）
```
eg:
import { BrowserRouter as Router, Link} from "react-router-dom";
class Main extends Component{
    render(){
        return(
        <Router>
            <div>
                <ul>
                    <li><link to='/'>首页</Link></li>
                    <li><link to='/other'>其他页</Link></li>
                </ul>
            </div>
        </Router>
        )
    }
}
```
需要说明的是：</br>
`<Router>`下面只能包含一个盒子标签，类似这里的div</br>
`<Link>`代表一个链接，在html界面中会解析成a标签。作为一个链接，必须有一个to属性，代表链接地址。这个链接地址是一个相对路径。

### `<Route>`
- `Route`代表你的路由界面，`path`代表路径，`component`代表路径对应的界面。
- 在其`path`属性与某个`location`匹配时呈现一些UI
- 
```
<Route path='/roster'>
//when the pathname is '/',the path dose not match
//when the pathname is '/roster' or '/roster/2',the path matches
//If you only want to match '/roster',then you need to use
//the "exact" prop.The following will match '/roster',but not
//'/roster/2'

<Route exact path="/roster">
```

#### `<Route>`将会渲染什么
[链接](https://juejin.im/post/5a7e9ee7f265da4e7832949c#heading-12)</br>
Routes可以接受三种props来决定路径匹配时渲染的元素，只能给`<Route>`元素提供一种来定义要渲染的内容。
- `<component>`</br>
一个React组件，当一个带有`component`prop的路由匹配的时候，路由将会返回prop提供的component类型的组件
- `render`</br>
一个返回React元素的方法，与`Component`类似，也是当路径匹配的时候会被调用。写成内联形式渲染和传递参数的时候非常方便。
- `children`</br>
一个返回React元素的方法。与前两种不同的是，这种方法总是会被渲染，无论路由与当前的路径是否匹配。
```
<Route path="/page" component={Page}/>

const extraProps = {color:'red'}
<Route path="/page" render={(props)=>{
    <Page {...props} data={extraProps}/>
}}
/>

<Route path="/page" children={(props)=>{
    props.match
    ?<Page {...props}/>
    :<EmptyPage {...props}/>
}}/>
```

### 路由传参
```
/a/1 ---this.props.match.params.id
/a?id=1---this.props.location.query.id`
```

### React Router V4设计思路
[链接](https://juejin.im/post/5986d1456fb9a03c3f405bd2)</br>
在V4中，路由是动态，需要忘记之前使用静态路由的思维方式，把路由当做普通组件，动态路由的优点，可以任意时间、任意地点自由添加新的Route。

e.g.</br>
```
<BrowserRoute>
    <div>
        <Route path='/' component={App}/>
        <Route path={'/about'} component={About}/>
        <Route path={'/contact'} component={Contact}/>
    </div>
</BrowserRoute>

const About = (props) =>{
    <div>
        <Route path={`${props.match.url/a}` component={AboutA}}/>
        <Route 
            path={`${props.match.url}/b` component={AboutB}}
        />
    </div>
}
//动态在About组件内定义两个新的路由
//当访问/about/a时，组件AboutA会被作为About的子组件渲染，
//当访问/about/b时，组件AboutB会作为About的子组件渲染。

```


###
[参考链接（1）](https://github.com/mrdulin/blog/issues/42)</br>
[参考链接（2）](https://www.kancloud.cn/chandler/react_handbook/1271261)