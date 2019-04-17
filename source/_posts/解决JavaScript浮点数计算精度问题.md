---
title: 解决JavaScript浮点数计算精度问题
categories: 
- JavaScript
tags:
- JavaScript
---
     

##  在使用JS开发过程中发现计算某些浮点数的时候,出现了精度问题 ! 导致出现了一些错误 !


比如: 0.1 + 0.2 = 0.30000000000000004  计算结果并不是我们想要的结果 0.3

刚开始发现的时候我也很纳闷, 这是怎么出现的 ? 后面去百度了一下,才知道原来是由于**某些浮点数**不能精确的转换成**二进制**造成的 !

## **1. 那么 如何将小数转换成二进制呢?**

**1) 整数部分**：除二取余，然后倒序排列，高位补零
**2) 小数部分**：小数部分乘以2，取整数部分依次从左往右放在小数点后，直至小数点后为0

例如: 0.1 转换成二进制

0.1 x 2 = 0
0.2 x 2 = 0
0.4 x 2 = 0
0.8 x 2 = 1
0.6 x 2 = 1
0.2 x 2 = 0
0.4 x 2 = 0
0.8 x 2 = 1
0.6 x 2 = 1

由于0.1 的二进制 0.000110011001100... 无限循环的

然而最终在计算机中存储的数值是有限的. 所以只能保留多少位..



## 2.我的解决思路:

  **先把小数转成整数.在计算, 最后把结果再转换成小数就ok了.**

```javascript

function MathTool() {

	//获取小数长度
	var getDecimalsLength = function (num) {
		return num.toString().split(".")[1] ? num.toString().split(".")[1].length : 0
	}
	
	//获取最大长度
	
	var getMaxLength = function (num1, num2) {
	
		var num1Length = getDecimalsLength(num1);
		
		var num2Length = getDecimalsLength(num2);
		
		var p = Math.max(num1Length, num2Length);
		
		var times = Math.pow(10, p);
		
		return times
	
	}
	
	//加法
	
	this.add = function(num1, num2) {
	
		var times = getMaxLength(num1, num2);
		
		return (this.mul(num1 ,times) + this.mul(num2 ,times)) / times;
		
	}
	
	//减法
	
	this.sub = function(num1, num2) {
	
		var times = getMaxLength(num1, num2);
		
		return (this.mul(num1 ,times) - this.mul(num2 , times)) / times;
		
	}
	
	//乘法
	
	this.mul = function(num1, num2) {
	
		var times = getMaxLength(num1, num2);
		
		var intNum1 = num1.toString().replace(".", "")
		
		var intNum2 = num2.toString().replace(".", "")
		
		var countDecimals = getDecimalsLength(num1) + getDecimalsLength(num2)
		
		return intNum1 * intNum2 / Math.pow(10, countDecimals);
		
	}
	
	//除法
	
	this.div = function(num1, num2) {
	
		var times = getMaxLength(num1, num2);
		
		return (this.mul(num1 , times) / this.mul(num2 , times));
		
	}
	
}
	
	var MathTool = new MathTool();
	console.log(MathTool.add(0.1, 0.2))
	console.log(MathTool.sub(0.0012, 0.0002))

```

