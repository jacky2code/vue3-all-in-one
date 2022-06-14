<!--
 * @Author: Jakcy Chang
 * @Date: 2022-06-13 17:18:36
 * @LastEditors: Jakcy Chang
 * @LastEditTime: 2022-06-14 19:34:47
 * @Description: 模板插值语法 & vue 指令
-->

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

  <div v-text="textStr"></div>

  <div v-html="htmlStr"></div>

  <div v-if="iFlag">I am v-if hidden?</div>

  <div v-if="ifElseStr == 'A'">A</div>
  <div v-else-if="ifElseStr == 'B'">B</div>
  <div v-else-if="ifElseStr == 'C'">C</div>
  <div v-else>D</div>

  <div v-show="showFlag">I am v-show hidden?</div>
  
  <div>
    <button v-on:click="clickBtn">click</button>
  </div>
  <div>
    <button @click="clickBtn">click@</button>
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
// 7. v-on 可简写成@ 用来给元素添加事件(点击，触摸，滑动等)
const clickBtn = () => {
  alert("click 'click' button!")
}
//  * v-bind 简写:  用来绑定元素的属性Attr
//  * 
//  * v-model 双向绑定
//  * 
//  * v-for 用来遍历元素
//  */


</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
