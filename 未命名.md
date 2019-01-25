在js中引用router     先将router下的    new Router 的文件进入, 才能使用router 的 push



判断ip和端口号

```
if (!window.location.origin) {
        window.location.origin = window.location.protocol + '//' + window.location.hostname + (window.location.port ? ':' + window.location.port : '')
      }
 console.log(window.location.origin)
```



解决chrome上密码输入框黄色

```
input:-webkit-autofill {
  -webkit-box-shadow: 0 0 0px 1000px #fff inset !important;
  -webkit-text-fill-color: rgba(0,0,0,1)!important;
}
```



先在state文件

export  default { accessToken: ''}

mutation.js  

```
changeAccessToken (state, string) {
    state.accessToken = string
  },
```

用的地方

this.$store.commit('changeAccessToken', res.data.data) 

router.push 不会走app.vue 用window.location.href



路由上this.$route.params.sourceType 取下来的是字符串



后台返回的数据一样, img不走error事件

拿到数据后, 先置空再赋值

this.picData.list = [] 

this.$nextTick(() => {

​            this.picData = res.data

​          })