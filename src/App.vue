<!--
 * @Author: Jakcy Chang
 * @Date: 2022-06-13 17:18:36
 * @LastEditors: Jakcy Chang
 * @LastEditTime: 2022-06-15 11:51:46
 * @Description: 模板插值语法 & vue 指令
-->

<template>
  <div>{{ message }}</div>
  <div>{{ condition == 0 ? '我是A' : '我是B' }}</div>
  <div>{{ calculation + 1 }}</div>
  <div>{{ ApiOpration.split(' ') }}</div>
  <!-- 1. v-text 用来显示文本 -->
  <div v-text="textStr"></div>
  <!-- 2. v-html 用来展示富文本 -->
  <div v-html="htmlStr"></div>
  <!-- 3. v-if 用来控制元素的显示隐藏（切换真假DOM） -->
  <div v-if="iFlag">I am v-if hidden?</div>
  <!-- 4. v-else-if 表示 v-if 的“else if 块”。可以链式调用 -->
  <div v-if="ifElseStr == 'A'">A</div>
  <div v-else-if="ifElseStr == 'B'">B</div>
  <div v-else-if="ifElseStr == 'C'">C</div>
  <!-- 5. v-else v-if条件收尾语句 -->
  <div v-else>D</div>
  <!-- 6. v-show 用来控制元素的显示隐藏（display none block Css切换） -->
  <div v-show="showFlag">I am v-show hidden?</div>
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
  <!-- 8.1 v-bind 绑定style -->
  <div :style="style">v-bind style</div>
  <!-- 8.2 v-bind 绑定class -->
  <div :class="[vBindFlag ? 'a' : 'b', 'o']">v-bind class</div>
  <!-- 8.2 v-bind 绑定class -->
  <div :class="vBindClass">v-bind class2</div>
  <!-- 9 v-model 双向绑定 -->
  <input v-model="vModelMsg" type="text" />
  <div>{{vModelMsg}}</div>
  <!-- 10. v-for -->
  <div :key="item" v-for="item in arrNum">{{ item }}</div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
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

/**
 * 5. 指令
 * v- 开头都是vue 的指令
 */
// 1. v-text 用来显示文本
const textStr = 'I am  textStr.'
// 2. v-html 用来展示富文本
const htmlStr = '<div><h2>I am htmlStr</h2></div>'
// 3. v-if 用来控制元素的显示隐藏（切换真假DOM）
const iFlag: boolean = false
// 4. v-else-if 表示 v-if 的“else if 块”。可以链式调用
const ifElseStr: string = 'A'
// 5. v-else v-if条件收尾语句
// 6. v-show 用来控制元素的显示隐藏（display none block Css切换）
const showFlag: boolean = true
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

// 9 v-model 双向绑定
const vModelMsg = ref('v-model')
// 10 v-for 用来遍历元素
const arrNum: Array<number> = [1, 2, 3, 4, 5]
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
