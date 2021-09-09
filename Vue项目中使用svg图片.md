# 在Vue项目中加载<svg>标签

### 1.批量加载svg文件

```livescript
const requireAll = requireContext => requireContext.keys().map(requireContext)
const req = require.context('./svg', false, /\.svg$/)
requireAll(req)
```

* **向`require.context`中传入的三个参数**
  
  * 读取文件的路径
  * 是否加载子文件夹
  * 匹配加载文件的正则表达式
  
* **`require.context`会返回一个`function`对象，拥有下面几个方法**
  
  * id
  * keys（）：返回由文件的键组成的数组（例如  key：./user.svg）
  * resolve（）：传入文件的键，返回文件相对于项目启动目录的路径
  
* **数组调用map()函数,遍历数组中元素(key)**

  * 传入函数req

    `ƒ webpackContext(req) {  //传入的参数req即为key
    	var id = webpackContextResolve(req);
    	return __webpack_require__(id);  //根据id返回模块
    }`

    `function webpackContextResolve(req) {
    	if(!__webpack_require__.o(map, req)) {
    		var e = new Error("Cannot find module '" + req + "'");
    		e.code = 'MODULE_NOT_FOUND';
    		throw e;
    	}
    	return map[req];   //map:   {"./user.svg": "./src/icons/svg/user.svg"
    }`}

  * 返回模块

### 2.配置`webpack.base.conf.js`和`vue.config.js`还有`package.json`

```
rules: [
    {
        test: /\.svg$/,
        loader: 'svg-sprite-loader',
        include: [resolve('src/icons')], //需要被loader 处理的文件
        options: {
            symbolId: 'icon-[name]'
        }
    },
    {
        test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
        loader: 'url-loader',
        exclude: [resolve('src/icons')],//不需要被loader 处理的文件
        options: {
            limit: 10000,
            name: utils.assetsPath('img/[name].[hash:7].[ext]')
        }
    },
```

* **虽然在上面指定了2种加载器的加载范围，但是在我的项目中并没有起作用，vue.cli还是默认执行`url-loader`，所以还要配置`vue.config.js`**

```
module.exports = {
    chainWebpack: config => {
        const svgRule = config.module.rule('svg');
        // 清空默认svg规则
        svgRule.uses.clear();
        //针对svg文件添加svg-sprite-loader规则
        svgRule
            .test( /\.svg$/)
            .use('svg-sprite-loader')
            .loader('svg-sprite-loader')
            .options({
                symbolId: 'icon-[name]'
            });
    }
}
```

* **添加`chainWebpack`以后，项目启动又报错了`'this.top' is assigned to itself  no-self-assign`,Google了一下，发现要修改ESLint的配置**

```
{
  "eslintConfig": {
    "root": true,
    "env": {
      "node": true
    },
    "extends": [
      "plugin:vue/essential",
      "eslint:recommended"
    ],
    "parserOptions": {
      "parser": "babel-eslint"
    },
    "rules": {
      "no-self-assign": ["error", {"props": true}]  //注意要放到rules里面，不然会报错
    }
  },
  "name": "mall-learning-web",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
  "version": "0.1.0"
}
```

### 3.注册全局组件

* **`src/components/SvgIcon/index.vue`**

```
<template>
    <svg :class="svgClass" aria-hidden="true">
        <use :xlink:href="iconName"></use>
    </svg>
</template>
```

* **`src/icons/index.js`**

```
import Vue from 'vue'
import SvgIcon from '@/components/SvgIcon/idnex'
Vue.component('svg-icon',SvgIcon) //全局注册组件
```

* **`src/main.js`**

```
import '@/icons' //导入加载文件
```

### 4.关联知识点

* `svg` ,`user`标签, `xlink:href`属性
* webpage的加载器（loader），批量导入模块（modulel），文件
* ESlint的配置，校验规则

### 5.参考

* [使用require.context实现前端工程自动化](https://www.jianshu.com/p/c894ea00dfec) 
* [关于webpack require.context() 的那点事](https://juejin.cn/post/6844903896171675656) 
