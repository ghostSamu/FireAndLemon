# 整合SpringBoot+SpringSecurity+Jwt

## 1.用户认证的方式

* cookie&session：cookie中存有sessionID，保存在浏览器端，请求发送的时候带上它，服务端拿到sessionID以后，根据ID获取相应的数据。

* token（Jwt）：存储在本地（浏览器端），服务器端无需存储，只保有解析方法。

  参考：

  [鉴权必须了解的5个兄弟：cookie、session、token、jwt、单点登录](https://zhuanlan.zhihu.com/p/395273289)

##  2.关于SpringSecurity

* Authentication：身份认证

* Authorization：授权

  参考：

  [Spring-Security权限框架](https://maizitoday.github.io/post/spring-security%E6%9D%83%E9%99%90%E6%A1%86%E6%9E%B6/)

##  3.认证过程

####   	3-1.配置`SecurityConfig`

* 设置拦截规则：`configure(HttpSecurity httpSecurity)`
  * 登录操作以外的请求都需要进行拦截
  * 设置filter的拦截顺序：`httpSecurity.addFilterBefore(jwtAuthenticationTokenFilter(), UsernamePasswordAuthenticationFilter.class);`
  * 设置自定义异常处理：`httpSecurity.exceptionHandling()`
* 自定义`Authentication`对象：`configure(AuthenticationManagerBuilder auth)`
  * 获取自定义`UserDetails`
* 设置义密码校验规则`PasswordEncoder`

####       3-2.前端发送登录请求

* 根据`UserDetails`生成`token(Jwt)`

####       3-2.前端发送获取资源请求

* 请求`header`中带上`token(Jwt)`

####       3-2.`UsernamePasswordAuthenticationFilter`进行拦截

* 创建`Authentication`对象：`UsernamePasswordAuthenticationToken`
* 进行认证：`AuthenticationManager.authenticate()`
* 从`UserDetailsService`中获取`UserDetails`
* `PasswordEncoder.matches()`方法进行认证
* 认证成功后`Authentication`对象（封装了用户的身份信息，以及相应的权限信息）保存到`SecurityContext`中，否则抛出异常

####   	3-3.`JwtAuthenticationTokenFilter`进行拦截

* 从`HttpServletRequest`的`header`中获取`token(Jwt)`和用户信息（`username`）
* 判断`token(Jwt)`是否有效
* 使用`username`创建`UserDetails`对象
* 使用``UserDetails``创建`Authentication`对象
* `Authentication`对象（封装了用户的身份信息，以及相应的权限信息）保存到`SecurityContext`中

参考：

[Spring security （一）架构框架-Component、Service、Filter分析](https://juejin.cn/post/6844903869852418056#heading-11)

[SpringSecurity+JWT认证流程解析 | 掘金新人第一弹](https://juejin.cn/post/6846687598442708999)

