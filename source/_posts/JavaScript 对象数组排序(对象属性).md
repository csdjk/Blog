---
title: JavaScript 对象数组排序(对象属性)
categories: 
- JavaScript
tags:
- JavaScript
- 数组
- 排序
- 对象
- 算法
---

```js

//1.数组排序(从小到大)
function sortby(a,b) {
      return a-b
}
arr.sort(sortby) //console.log(arr);

//2.对象数组排序(按照数组对象中某个属性值进行排序)
var arrObj = [
	{
	    name: "b",
	    age: 18
	},
	{
	    name: "bb",
	    age: 30
	},
	{
	    name: "aa",
	    age: 30
	},
	{
	    name: "c",
	    age: 20
	}
]
function compare(key) {
	return function (a,b) { 
		return a[key]-b[key]
	}
}
arrObj.sort(compare("age"))	//根据age排序

//如果还需要在 age 的排序基础上再通过 name的字母排序	例如: age=30的值有两个:"bb","aa",再在这个基础上根据字母排序: aa 排在 bb 前面
arrObj.sort((a, b) => {
  if (b.age !== a.age) {
        return b.age - a.age
    } else {
        return a.name < b.name ? -1 : 1
    }
})
```





