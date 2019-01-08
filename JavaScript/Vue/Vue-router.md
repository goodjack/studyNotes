# Vue-router

[Vue-router API文档地址](https://router.vuejs.org/zh/api/#router-link)

`router-link`：导航标签，相当于`<a href=''></a>`

例：`<router-link to='/'>首页</router-link>`

`router-view`：可以给子模板提供插入位置

`vue-router`传参

1. name传参：`<router-link :to="{name:'test',params:{key:value}}"></router-link>`
   - name：路由配置文件中的name值
   - params：以对象形式传递要传的参数
   - {{$route.params.key}}：这种形式接受传递的参数
2. 

