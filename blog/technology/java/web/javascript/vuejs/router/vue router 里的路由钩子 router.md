[TOC]



# vue里的路由钩子

## 简介

在某些情况下，当路由跳转前或跳转后、进入、离开某一个路由前、后，需要做某些操作，就可以使用路由钩子来监听路由的变化

## 全局路由钩子：

```js
router.beforeEach((to, from, next) => {
    //会在任意路由跳转前执行，next一定要记着执行，不然路由不能跳转了
    console.log('beforeEach')
    console.log(to,from)
    //
    next()
})
//
router.afterEach((to, from) => {
    //会在任意路由跳转后执行
    console.log('afterEach')
    console.log(to,from)
})
```

## 单个路由钩子：

只有beforeEnter，在进入前执行，to参数就是当前路由

```js
 routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
```

## 路由组件钩子：

```js
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
```





https://segmentfault.com/a/1190000012552286