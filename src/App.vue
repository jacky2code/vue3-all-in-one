<!--
 * @Author: Jakcy Chang
 * @Date: 2022-06-13 17:18:36
 * @LastEditors: Jakcy Chang
 * @LastEditTime: 2022-06-14 17:14:00
 * @Description: 
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
  <!-- v-text -->
  <div v-text="textStr">
  </div>
  <!-- v-html -->
  <div v-html="htmlStr"></div>
  <div v-if="iFlag">I am v-if hidden?</div>
  <div v-show="showFlag">I am v-show hidden?</div>
</template>

<script setup lang="ts">
// 插值语法
// 1. 在 script 声明一个变量可以直接在 templete 使用{{变量名称}} 如下：
const message: string = "我是真男人"
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
// 5.1 v-text 用来显示文本
const textStr = 'I am  textStr.'
//  5.2 v-html 用来展示富文本
const htmlStr = '<div><h2>I am htmlStr</h2></div>'
//  5.3 v-if 用来控制元素的显示隐藏（切换真假DOM）
const iFlag: boolean = false
//  5.4 v-else-if 表示 v-if 的“else if 块”。可以链式调用
//  * 
//  5.5 v-else v-if条件收尾语句
//  * 
//  5.6 v-show 用来控制元素的显示隐藏（display none block Css切换）
const showFlag: boolean = true
//  * v-on 简写@ 用来给元素添加事件
//  * 
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
