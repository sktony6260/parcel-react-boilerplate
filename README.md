# parcel-react-boilerplate

这个模板是用parcel和webpack打包的react应用脚手架，并且都支持组件的异步加载

### react和相关库的版本

* react 16.2.0
* react-dom 16.2.0
* react-router 4.2.0

### 打包工具的版本

* parcel 1.2.0
* webpack 3.10.0

## 安装和运行程序

克隆项目

```
git clone https://github.com/sktony6260/parcel-react-boilerplate
```
进入有package.json的根目录

使用parcel来打包

```
启动项目
> npm start
```
```
编译项目
> npm build
```
使用webpack来打包

```
启动项目
> npm run webpack-start
```

```
编译项目
> npm run webpack-build
```

## 说明

parcel是零配置的打包工具，所以配置方面只需要简单配置下babelrc即可，webpack方面使用了DllPlugin来打包一些vender库， 并且该脚手架中使用到了code split，让所有组件都执行异步加载，但是parcel和webpack的组件异步加载方式不一样，具体运行请参考下方的提示


**react-router4.0**后改动比较大，之前支持导出一个routes对象来配置路由，现在需要依赖他的react-router-dom来配置，但官方文档也给出了一个例子可以参考
```
const RouteWithSubRoutes = (route) => (
  <Route path={route.path} render={props => (
    // pass the sub-routes down to keep nesting
    <route.component {...props} routes={route.routes}/>
  )}/>
)
const routerMaker = routes.map((route, i) => (
    <RouteWithSubRoutes key={i} {...route}/>
))

```
### parcel打包异步加载组件方式

parcel官方推荐的方法是使用import方法来异步加载，但是针对react要多写一个方法`bundle`方法来接受一个import引用的组件才能支持异步加载，请在路由文件中使用下面的方式来配置路由
```
const TablePage = Load({
  template: import('../Table.js'),
  name: 'table'
})
const ListPage = Load({
  template: import('../List.js'),
  name: 'list'
})
const routes = [
  {
    path:'/welcome',
    component:Welcome
  },
  {
    path:'/table',
    component:TablePage
  },
  {
    path:'/list',
    component:ListPage
  }
];
```

### webpack打包异步加载组件方式

react-router4.0之前在导出routes的时候，组件如果是配置成异步的webpack会自动codesplit，但之后不支持导出routes对象形式后，可以利用一个官方提供的lazy的方式来实现，代码见lazyLoad.js

请在路由文件中使用下面的方式来配置路由

```
const Loading = () => {
  return <div>loading component....</div>
}
const createComponent = (component) =>() => {
    let AsyncComponent = (
        <Lazyload load={component}>
            {
                (Async) => Async?<Async />:<Loading/>
            }
        </Lazyload>
    )
    return AsyncComponent
}
const routes = [
  {
    path:'/welcome',
    component:Welcome
  },
  {
    path:'/table',
    component:createComponent(TablePage)
  },
  {
    path:'/list',
    component:createComponent(ListPage)
  }
];
```

## 结束语

建的第一个自己的github项目，mark下


