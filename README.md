## 技术栈 
svelte、webpack

[svelte 官网](https://svelte.dev/examples/hello-world)
[webpack 文档地址](https://webpack.docschina.org/concepts/)
## 关键步骤
#### 一、创建index.js 
`index.js` 文件就是引入组件，并导出，作为`npm`包的入口文件；   
注意`index.js`不是项目启动时候的`main.js`， `main.js` 是把组件挂载到`body`上运行， `index.js` 只是把组件导出
#### 二、webpack打包入口区分
我这里配置了两个打包命令 `npm run build ` 和  `npm run build-npm`, 主要是传了`NPM_ENV` 参数，用这个参数来区分，`webpack`打包的入口文件
`webpack` 的入口配置如下：
```
entry: {
		bundle: [
			...stylesheets,
			npm_mode == '1' ? './src/index.js' : './src/main.js'
		]
	},
```
项目本地启动 和 正常build 的时候都是使用 `main.js`， 只有打包成npm的时候 使用`index.js`
#### 三、webpack产出代码配置
`npm`要想打包成`umd`格式的代码，需要在`output`里添加， `library`  `libraryTarget` `umdNamedDefine` 三个字段,代码如下
```
output: {
		path: path.resolve(__dirname, 'public/build'),
		publicPath: '/build/',
		filename: '[name].js',
		chunkFilename: '[name].[id].js',
		library: 'YylSvelteNpm', //类库名称
        libraryTarget: 'umd', //类库加载方式
        umdNamedDefine: true
},
```
#### 四、配置package.json
主要有下面几个配置
```
  "private": false, 
  "version": "0.0.1",
  "description": "基于svelte开发npm",
  "author": "yyl",
  "main": "public/build/bundle.js",
  "name": "yyl-svelte-npm",
```
- private 必须设置为false, 不然发布到npm别人也看不到这个包
- version 版本号，每次发布的时候 都需要修改版本，如果版本跟线上一样的话，会导致发布不上去
- description 描述，可以加一些自己相加的描述
- author 作者名字
- main npm 入口文件
- name npm上显示的名字

## 在Vue中使用打包后的npm
- npm i yyl-svelte-npm -S   // 安装npm包
- import YlSvelteTest from 'yl-svelte-test' // 导入包
-  new YlSvelteTest({
          target:  document.getElementById('aaaa')
        }) // 把组件挂载到一个div上，该div的id为aaaa

vue文件代码如下：
```
<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import YylSvelteTest from 'yyl-svelte-npm'

export default {
  name: 'App',
  components: {
    HelloWorld
  },
  mounted() {
      setTimeout(() => {
        new YylSvelteTest({
          target:  document.getElementById('aaaa')
        })
      }, 2000);
  }
}
</script>
```
注意：`<div id="aaaa"></div>` 不能放在一个异步组件里，否则会找不到，导致报错 我这里直接放在里 `index.html` 里了
如下就是运行的效果，红色框内 就是npm组件：

<img src="https://img2024.cnblogs.com/blog/872412/202403/872412-20240328151559948-1495444542.png" alt="" width="300" height="400">

## 在Jquery项目中使用
##### 1. 在index.html 中像引用jquery.js 那样引用webpack打包好的js, 如下：
`<script src="https://abc.com/test/yyl-svelte-npm.js"></script>`

这个`yyl-svelte-npm.js` 就是 打包后的 `bundle.js` 这里只是改了下名字

##### 2. 具体使用的代码如下：
```
    const topRow2 = $('#aaaa')[0] // 这里必须加上[0]
    new window.YylSvelteNpm.default({target: topRow2});
```
或者换成一行如下所示：
```
new window.YylSvelteNpm.default({target: document.getElementById('aaaa')});
```
注意：不要忘了 `id` 为 `aaaa`的 `div` 元素

## npm 发布相关命令
1. npm 官网地址：https://www.npmjs.com/
2. npm login 登录npm
3. npm publish 发布到npm仓库
4. npm who i am 查看当前登录的账号名字

## 注意点
1. 记得需要单独引用样式文件，打包后的样式文件跟bundle.js在一块的
2. 在package.json中添加 "files": ["./public"], 可以限制只把打包的代码上传到npm，避免源码上传和泄露

## 总结
1. 使用svelte webpack生成umd 规范的npm 可以在不同框架之间公用，可以提高开发效率
2. svelte本身相比vue、react会更轻量化，打包后的产物代码量更少而且还没有使用vdom，运行效率也会更高

