---
title: JavaScript里的数组去重
categories: 前端
tags:
  - JavaScript
  - 数组
  - 前端
abbrlink: 2ad17b2f
date: 2020-04-18 15:05:20
---

## 先生成一个数组，数字里边的元素是随机生成的10以内数字
```js
let arr = Array.from({length:20},=>Math.random()*10|0);
console.log(arr);
//输出结果：
[5,4,7,0,0,0,8,0,2,9,3,0,0,1,5,9,2,8,6,0]
```

## 双层循环

双层循环，外层循环元素，内层循环时比较值，值相同时，则删去这个值

```js
// 此种方法会改变原数组的值
Array.prototype.distinct = function (){
    var arr = this,
        i,
        j,
        len = arr.length;
 
    for(i = 0; i < len; i++){
        for(j = i + 1; j < len; j++){
            if(arr[i] == arr[j]){
                arr.splice(j,1);
                len--;
                j--;
            }
        }
    }
    return arr;
};
 
var a = [1,2,3,4,5,6,5,3,2,4,56,4,1,2,1,1,1,1,1,1,];
var b = a.distinct();
console.log(b.toString()); //1,2,3,4,5,6,56

// 不改变原数组
Array.prototype.distinct = function(){
    var arr = this,
        result = [],
        i,
        j,
        len = arr.length;
  
    for(i = 0; i < len; i++){
        for(j = i + 1; j < len; j++){
            if(arr[i] === arr[j]){
                j = ++i;
            }
        }
        result.push(arr[i]);
    }
    return result;
}
var arra = [1,2,3,4,4,1,1,2,1,1,1];
arra.distinct()
```

## indexOf的使用
```js
let rs = [];
for(let i=0;i<arr.length;i++){
    if(rs.indexOf arr[i] === -1){
        rs.push(arr[i])
    }
};
console.log(rs)
```

## 数组的filter方法
```js
arr.filter(function(element,index,self){
    return self.indexOf(element) === index;
 });
```
element是数组的每个值，index是数组的索引，self是数组本身。

当使用indexOf方法时，如果数组的每项的indexOf方法得到的值与数组索引相同，则证明此值第一次出现

## 对象去重法
```js
var o={};
var new_arr=[];
for(var i=0;i<arr.length;i++){
    var k=arr[i];
    if(!o[k]){
        o[k]=true;
        new_arr.push(k);
    }
}
```
利用对象的key来做判断时，只用到了一次循环，大大增加运行的性能

## Set
```js
[...new Set(arr)]
```