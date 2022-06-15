# Vue 3 all in one

感谢[小满](https://blog.csdn.net/qq1195566313?type=blog) 出的学习文章，整理成此一篇，方便学习。

## 第一章 Vue3 介绍以及新特性

### 1.1 介绍Vue

Vue (读音 /vjuː/，类似于 **view**) 是一套用于构建用户界面的**渐进式框架**。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与[现代化的工具链](https://cn.vuejs.org/v2/guide/single-file-components.html)以及各种[支持类库](https://github.com/vuejs/awesome-vue#libraries--plugins)结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

MVVM（Model-View-ViewModel）架构

- View：视图层（UI 用户界面）

- ViewModel：业务逻辑层（一切 js 可视为业务逻辑）

- Model：数据层（存储数据及对数据的处理如增删改查）

![img](https://img-blog.csdnimg.cn/a5c02dc81b9547a6bebfd9dbc3502687.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_20,color_FFFFFF,t_70,g_se,x_16)

官方文档地址  [介绍 — Vue.js](https://cn.vuejs.org/v2/guide/#Vue-js-是什么)

[新版地址文档快速开始 | Vue.js](https://staging-cn.vuejs.org/guide/quick-start.html)

### 1.2 回顾Vue2 对比 Vue3

我们看如下图

发现传统的Vue2 逻辑比较分散 可读性差 可维护性差

对比Vue3 逻辑分明 可维护性 高

![img](https://img-blog.csdnimg.cn/img_convert/e8ad905d83aaec45451797517ef453aa.png)

### 1.3 Vue3 新特性介绍

- 重写双向数据绑定
- VDOM 性能瓶颈
- Fragments
- Tree-Shaking 的支持
- Composition API

#### 重写双向数据绑定

``` vue
vue2
基于Object.defineProperty()实现
 
vue3 基于Proxy
proxy与Object.defineProperty(obj, prop, desc)方式相比有以下优势：
 
//丢掉麻烦的备份数据
//省去for in 循环
//可以监听数组变化
//代码更简化
//可以监听动态新增的属性；
//可以监听删除的属性 ；
//可以监听数组的索引和 length 属性；
 
    let proxyObj = new Proxy(obj,{
        get : function (target,prop) {
            return prop in target ? target[prop] : 0
        },
        set : function (target,prop,value) {
            target[prop] = 888;
        }
    })
```

#### Vue3 优化 Vdom

**在Vue2中,每次更新diff,都是全量对比,Vue3则只对比带有标记的,这样大大减少了非动态内容的对比消耗**

[Vue Template Explorer](https://vue-next-template-explorer.netlify.app/) 我们可以通过这个网站看到静态标记

![img](https://img-blog.csdnimg.cn/0b5650888b95431c8682836a68e2ca98.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_20,color_FFFFFF,t_70,g_se,x_16)

#### patch flag 优化静态树

``` vue
<span>Hello world!</span>
<span>Hello world!</span>
<span>Hello world!</span>
<span>Hello world!</span>
<span>{{msg}}/span>
<span>Hello world!</span>
<span>Hello world! </span>
```

`Vue3` 编译后的 `Vdom` 是这个样子的

``` javascript
export function render(_ctx，_cache，$props，$setup，$data，$options){return (_openBlock(),_createBlock(_Fragment,null，[
_createvNode( "span", null,"Hello world ! "),
_createvNode( "span",null，"Hello world! "),
_createvNode( "span"，null，"Hello world! "),
_createvNode( "span", null，"Hello world! "),
_createVNode("span", null，_toDisplaystring(_ctx.msg)，1/* TEXT */)，
_createvNode( "span", null，"Hello world! "),
_createvNode( "span", null，"Hello world! ")]，64/*STABLE_FRAGMENT */))
```

新增了 patch flag 标记

``` vue
TEXT = 1 // 动态文本节点
CLASS=1<<1,1 // 2//动态class
STYLE=1<<2，// 4 //动态style
PROPS=1<<3,// 8 //动态属性，但不包含类名和样式
FULLPR0PS=1<<4,// 16 //具有动态key属性，当key改变时，需要进行完整的diff比较。
HYDRATE_ EVENTS = 1 << 5，// 32 //带有监听事件的节点
STABLE FRAGMENT = 1 << 6, // 64 //一个不会改变子节点顺序的fragment
KEYED_ FRAGMENT = 1 << 7, // 128 //带有key属性的fragment 或部分子字节有key
UNKEYED FRAGMENT = 1<< 8, // 256 //子节点没有key 的fragment
NEED PATCH = 1 << 9, // 512 //一个节点只会进行非props比较
DYNAMIC_SLOTS = 1 << 10 // 1024 // 动态slot
HOISTED = -1 // 静态节点
BALL = -2
```

我们发现创建动态 dom 元素的时候，Vdom 除了模拟出来了它的基本信息之外，还给它加了一个标记： 1 /* TEXT */

这个标记就叫做 patch flag（补丁标记）

patch flag 的强大之处在于，当你的 diff 算法走到 _createBlock 函数的时候，会忽略所有的静态节点，只对有标记的动态节点进行对比，而且在多层的嵌套下依然有效。

尽管 JavaScript 做 Vdom 的对比已经非常的快，但是 patch flag 的出现还是让 Vue3 的 Vdom 的性能得到了很大的提升，尤其是在针对大组件的时候。

#### Vue3 Fragment

vue3 允许我们支持多个根节点

``` vue
<template>
  <div>12</div>
  <div>23</div>
</template>
```

同时支持render JSX 写法

``` vue
render() {
    return (
        <>
            {this.visable ? (
                <div>{this.obj.name}</div>
            ) : (
                <div>{this.obj.price}</div>
            )}
            <input v-model={this.val}></input>
            {[1, 2, 3].map((v) => {
               return <div>{v}-----</div>;
            })}
        </>
    );
},
```

同时新增了Suspense 和 多 v-model 用法

#### Vue3 Tree shaking

简单来讲，就是在保持代码运行结果不变的前提下，去除无用的代码

在Vue2中，无论我们使用什么功能，它们最终都会出现在生产代码中。主要原因是Vue实例在项目中是单例的，捆绑程序无法检测到该对象的哪些属性在代码中被使用到

而Vue3源码引入tree shaking特性，将全局 API 进行分块。如果你不使用其某些功能，它们将不会包含在你的基础包中

就是比如你要用watch 就是import {watch} from 'vue' 其他的computed 没用到就不会给你打包减少体积

#### Vue3 Composition Api

Setup 函数式编程 也叫vue Hook

例如 ref reactive watch computed toRefs toRaws 我们会在下几个章节详解



## 第二章 配置环境，NVM及NRM使用

### 2.1 配置环境

#### 2.1.1 安装 nodejs

***装过的同学可以忽略\***

[下载 | Node.js 中文网](http://nodejs.cn/download/current/)

装完之后会有一个命令叫 npm

可以在终端输入npm -v 来检查是否安装成功

![img](https://img-blog.csdnimg.cn/7d2fc2a1c353444b8e9dd56e0f63145f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 2.1.2 构建 Vite 项目

官方文档[开始 {#getting-started} | Vite中文网](https://vitejs.cn/guide/#overview)

vite 的优势

 冷服务  默认的构建目标浏览器是能 [在 script 标签上支持原生 ESM](https://caniuse.com/es6-module) 和 [原生 ESM 动态导入](https://caniuse.com/es6-module-dynamic-import)

 HMR 速度快到惊人的 [模块热更新（HMR）](https://vitejs.cn/guide/features.html#hot-module-replacement)

Rollup打包 它使用 [Rollup](https://rollupjs.org/) 打包你的代码，并且它是预配置的 并且支持大部分rollup插件

使用vite初始化一个项目

- npm

  ``` bash
  npm init vite@latest
  ```

- Yarn

  ``` bash
  yarn create vite
  ```

项目名称
![img](https://img-blog.csdnimg.cn/79a9f7a215224aafb904217075b3816b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_20,color_FFFFFF,t_70,g_se,x_16)

构建的项目模板

![img](https://img-blog.csdnimg.cn/229e41aa7c124fb8a0ed11d89a37e342.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_20,color_FFFFFF,t_70,g_se,x_16)

切换目录

npm install 安装依赖包

npm run dev 启动

![img](https://img-blog.csdnimg.cn/06271d7048fb4dbda0447badfd4c0e1d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_20,color_FFFFFF,t_70,g_se,x_16)

package json 命令解析

``` json
{
  "scripts": {
    "dev": "vite", // 启动开发服务器，别名：`vite dev`，`vite serve`
    "build": "vite build", // 为生产环境构建产物
    "preview": "vite preview" // 本地预览生产构建产物
  }
}
```

#### 2.1.3 安装Vue cli 脚手架

``` bash
npm install @vue/cli -g
```

检查是否安装成功

![img](https://img-blog.csdnimg.cn/89487aa89f184aa9bd5f90df990a0380.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_20,color_FFFFFF,t_70,g_se,x_16)

创建项目

``` bash
vue create <project name>
```

### 2.2 NVM, NRM 使用

#### 2.2.1 NVM 介绍

NVM全称[node](https://so.csdn.net/so/search?q=node&spm=1001.2101.3001.7020).js version management ，专门针对node版本进行管理的工具，通过它可以安装和切换不同版本的node.js

使用场景

我目前的公司有很多项目，其中有一些老项目用的是vue2.5左右了[webpack](https://so.csdn.net/so/search?q=webpack&spm=1001.2101.3001.7020)版本也比较低，只能使用10.16.0左右的node版本，但是也有一些新项目需要使用高版本的node例如14.17.3左右的这时候就可以使用nvm切换node 版本

##### 1.安装nvm

- windows 

[github](https://so.csdn.net/so/search?q=github&spm=1001.2101.3001.7020)（程序员交友平台）地址

[Releases · coreybutler/nvm-windows · GitHub](https://github.com/coreybutler/nvm-windows/releases)

![img](https://img-blog.csdnimg.cn/7a957dd3e7c7499a88d4de2bcc8db702.png)

- Mac

1.执行以下脚本

``` bash
sudo curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```

2.重启电脑 或者是刷新bash文件即可生效

``` bash
source ~/.bash_profile
```

##### 2. Nvm 常用命令操作

###### nvm list 查看现在所有安装的node版本

![img](https://img-blog.csdnimg.cn/52b2f2a10a604e4f860e30797d795f23.png)

###### nvm list available 查看nodejs 官方的所有版本

![img](https://img-blog.csdnimg.cn/5e5273181bdf4780ad3ec37b6bc8180e.png)

###### nvm install （版本号）下载对应的node版本号

![img](https://img-blog.csdnimg.cn/474060de2ed7452f99e17525c760796a.png)

###### nvm use 切换node版本

![img](https://img-blog.csdnimg.cn/103768aa0c634639befdfa3277f21862.png)

#### 2.2.2 NRM 介绍

nrm 是一个 npm 源管理器，允许你快速地在 npm源间切换。

##### 1.安装

``` bash
npm install -g nrm
```

##### 2.npm常用命令介绍

 ###### nrm ls **查看可选源 星号代表当前使用源**

![img](https://img-blog.csdnimg.cn/fefabc7a1309407a9069339f3f40a184.png)

###### nrm use （源）切换源

![img](https://img-blog.csdnimg.cn/c2085f24803844adac59822266683a48.png)

###### nrm add （名称）（源）

![img](https://img-blog.csdnimg.cn/6bb66c6b99284e79811d1b7ffcf5e455.png)

###### 测试速度 nrm test npm

![img](https://img-blog.csdnimg.cn/bf45982512ef41c2b8f9af22e6ca4694.png)



## 第三章 Vite 目录 & Vue 单文件组件

### Vite 目录

public 下面的不会被编译 可以存放静态资源

assets 下面可以存放可编译的静态资源

components 下面用来存放我们的组件

App.vue 是全局组件

main ts 全局的ts文件

index.html 非常重要的入口文件 （webpack，rollup 他们的入口文件都是enrty input 是一个js文件 而Vite 的入口文件是一个html文件，他刚开始不会编译这些js文件 只有当你用到的时候 如script src="xxxxx.js" 会发起一个请求被vite拦截这时候才会解析js文件）

vite config ts 这是vite的配置文件具体配置项 后面会详解

VsCode Vue3 插件推荐 Vue Language Features (Volar)

### SFC 语法规范

`*.vue` 文件都由三种类型的顶层语法块所组成：`<template>`、`<script>`、`<style>`

#### `<template>`

- 每个 `*.vue` 文件最多可同时包含一个顶层 `<template>` 块。
- 其中的内容会被提取出来并传递给 `@vue/compiler-dom`，预编译为 JavaScript 的渲染函数，并附属到导出的组件上作为其 `render` 选项。

#### `<script>`

- 每一个 `*.vue` 文件最多可同时包含一个 `<script>` 块 (不包括[`<script setup>`](https://v3.cn.vuejs.org/api/sfc-script-setup.html))。
- 该脚本将作为 ES Module 来执行。
- 其**默认导出**的内容应该是 Vue 组件选项对象，它要么是一个普通的对象，要么是 [defineComponent](https://v3.cn.vuejs.org/api/global-api.html#definecomponent) 的返回值。

#### `<script setup>`

- 每个 `*.vue` 文件最多可同时包含一个 `<script setup>` 块 (不包括常规的 `<script>`)
- 该脚本会被预处理并作为组件的 `setup()` 函数使用，也就是说它会在**每个组件实例**中执行。`<script setup>` 的顶层绑定会自动暴露给模板。更多详情请查看 [<script setup> 文档](https://v3.cn.vuejs.org/api/sfc-script-setup)。

#### `<style>`

- 一个 `*.vue` 文件可以包含多个 `<style>` 标签。
- `<style>` 标签可以通过 `scoped` 或 `module` attribute (更多详情请查看 [SFC 样式特性](https://v3.cn.vuejs.org/api/sfc-style)) 将样式封装在当前组件内。多个不同封装模式的 `<style>` 标签可以在同一个组件中混



## 第四章 模板语法 & Vue 指令

### 4.1 模板[插值](https://so.csdn.net/so/search?q=插值&spm=1001.2101.3001.7020)语法

- 在 `<script>` 声明一个变量可以直接在`<template>` 使用，用法为 `{{变量名称}}`
- 编写条件运算
- 编写运算
- 操作 API

``` vue
<template>
  <div>
    {{ message }}
  </div>
  <div>
    {{ condition == 0 ? '我是A' : '我是B' }}
  </div>
  <div>
    {{ calculation + 1 }}
  </div>
  <div>
    {{ ApiOpration.split(' ') }}
  </div>
</template>

<script setup lang="ts">
/**
 * 4.1 插值语法
 * v- 开头都是vue 的指令
 */
// 1. 在 script 声明一个变量可以直接在 templete 使用{{变量名称}} 如下：
const message: string = "我是A"
// 2. 模板语法可以编写条件运算
const condition: number = 1
// 3. 支持运算
const calculation: number = 1
// 4. 支持操作API
const ApiOpration: string = 'I am Jack .'
</script>

<style>
</style>
```

### 4.2 指令

v- 开头都是vue 的指令

- v-text 用来显示文本

  ```vue
  <template>
    <!-- 1. v-text 用来显示文本 -->
    <div v-text="textStr"></div>
  </template>
  <script setup lang="ts">
    // 1. v-text 用来显示文本
  	const textStr = 'I am  textStr.'
  </script>
  <style>
  </style>
  ```

- v-html 用来展示富文本

  ```vue
  <template>
    <!-- 2. v-html 用来展示富文本 -->
    <div v-html="htmlStr"></div>
  </template>
  <script setup lang="ts">
    // 2. v-html 用来展示富文本
  	const htmlStr = '<div><h2>I am htmlStr</h2></div>'
  </script>
  <style>
  </style>
  ```

- v-if 用来控制元素的显示隐藏（切换真假DOM）

  ```vue
  <template>
    <!-- 3. v-if 用来控制元素的显示隐藏（切换真假DOM） -->
    <div v-if="iFlag">I am v-if hidden?</div>
  </template>
  <script setup lang="ts">
    // 3. v-if 用来控制元素的显示隐藏（切换真假DOM）
  	const iFlag: boolean = false
  </script>
  <style>
  </style>
  ```

- v-else-if 表示 v-if 的“else if 块”。可以链式调用 v-else v-if条件收尾语句

  ```vue
  <template>
    <!-- 4. v-else-if 表示 v-if 的“else if 块”。可以链式调用 -->
    <div v-if="ifElseStr == 'A'">A</div>
    <div v-else-if="ifElseStr == 'B'">B</div>
    <div v-else-if="ifElseStr == 'C'">C</div>
  </template>
  <script setup lang="ts">
    // 4. v-else-if 表示 v-if 的“else if 块”。可以链式调用
  	const ifElseStr: string = 'A'
  </script>
  <style>
  </style>
  ```

- v-show 用来控制元素的显示隐藏（display none block Css切换）

  ```vue
  <template>
    <!-- 6. v-show 用来控制元素的显示隐藏（display none block Css切换） -->
    <div v-show="showFlag">I am v-show hidden?</div>
  </template>
  <script setup lang="ts">
    // 6. v-show 用来控制元素的显示隐藏（display none block Css切换）
  	const showFlag: boolean = true
  </script>
  <style>
  </style>
  ```

- v-on 简写@ 用来给元素添加事件

  ```vue
  <template>
    <!-- 7.1 v-on 可简写成@ 用来给元素添加事件(点击，触摸，滑动等) -->
    <div>
      <button v-on:click="clickBtn">click</button>
    </div>
    <div>
      <!-- v-on 简写 @ -->
      <button @click="clickBtn">click@</button>
    </div>
    <div @click="parent">
      <!-- 7.2 防止父级被单击 -->
      <div @click.stop="child">child</div>
    </div>
    <!-- 7.3 阻止表单提交刷新案例 -->
    <form action="/">
      <button @click.prevent="submit" type="submit">submit</button>
    </form>
  </template>
  <script setup lang="ts">
    // 7.1 v-on 可简写成@ 用来给元素添加事件(点击，触摸，滑动等)
  	const clickBtn = () => {
  	  alert("click 'click' button!")
  	}
  	// 7.2 v-on 修饰符 冒泡案例
  	const child = () => {
  	  console.log('child')
  	}
  	const parent = () => {
  	  console.log('parent')
  	}
  	// 7.3 阻止表单提交案例
  	const submit = () => {
  	  console.log('child')
  	}
  </script>
  <style>
  </style>
  ```

- v-bind 简写:  用来绑定元素的属性Attr, 绑定class, style

  ```vue
  <template>
    <!-- 8.1 v-bind 绑定style -->
    <div :style="style">v-bind style</div>
    <!-- 8.2 v-bind 绑定class -->
    <div :class="[vBindFlag ? 'a' : 'b', 'o']">v-bind class</div>
    <!-- 8.2 v-bind 绑定class -->
    <div :class="vBindClass">v-bind class2</div>
  </template>
  <script setup lang="ts">
    // 8 v-bind 简写:  用来绑定元素的属性Attr
  	// 8.1 v-bind 绑定style
  	type Style = {
  	  height: string,
  	  color: string
  	}
  	const style: Style = {
  	  height: "30px",
  	  color: "blue"
  	}
  	// 8.2 v-bind 绑定class
  	const vBindFlag: boolean = false
  	// 8.3 v-bind 绑定class
  	type Cls = {
  	  b: boolean,
  	  o: boolean
  	}
  	const vBindClass: Cls = {
  	  b: false,
  	  o: true
  	}
  </script>
  <style>
    .a {
  	  color: red;
  	}
  	
  	.b {
  	  color: blue;
  	}
  	
  	.o {
  	  height: 30px;
  	  border: 1px solid #ccc;
  	}
  </style>
  ```

- v-model 双向绑定

  ```vue
  <template>
    <!-- 9 v-model 双向绑定 -->
    <input v-model="vModelMsg" type="text" />
    <div>{{vModelMsg}}</div>
  </template>
  <script setup lang="ts">
    import { ref } from 'vue';
    // 9 v-model 双向绑定
  	const vModelMsg = ref('v-model')
  </script>
  <style>
  </style>
  ```

- v-for 用来遍历元素

  ```vue
  <template>
    <!-- 10. v-for -->
    <div :key="item" v-for="item in arrNum">{{ item }}</div>
  </template>
  <script setup lang="ts">
    // 10 v-for 用来遍历元素
  	const arrNum: Array<number> = [1, 2, 3, 4, 5]
  </script>
  <style>
  </style>
  ```

  

