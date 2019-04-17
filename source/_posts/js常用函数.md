---
title: JavaScript 常用函数
categories: 
- JavaScript
tags:
- JavaScript
- 常用函数
top: 2
---
###  获取当前时间

```javascript
  function getNowFormatDate(cutLine) {
        var date = new Date();
        var year = date.getFullYear();
        var month = date.getMonth() + 1;
        var day = date.getDate();
        var h = date.getHours();
        var m = date.getMinutes();
        var s = date.getSeconds();

        function getNow(s) {
            return s < 10 ? '0' + s : s;
        }

        var currentdate = year + cutLine + getNow(month) + cutLine + getNow(day) + " " + getNow(h) + ":" + getNow(m) + ":" + getNow(s);
        return currentdate;
    }
        
 console.log(getNowFormatDate("/")) // 2018/12/13 16:48:16
    
```

### 类型判断

```javascript
function dataType(obj) {
   if (obj === null) return "Null";
   if (obj === undefined) return "Undefined";
   console.log(Object.prototype.toString.call(obj));
   return Object.prototype.toString.call(obj).slice(8, -1);
};
          
```


###  字符串格式化

```javascript
String.prototype.format = function (args) {
    var result = this;
    if (arguments.length > 0) {
        if (arguments.length == 1 && typeof (args) == "object") {
            for (var key in args) {
                if (args[key] != undefined) {
                    var reg = new RegExp("({" + key + "})", "g");
                    result = result.replace(reg, args[key]);
                }
            }
        }
        else {
            for (var i = 0; i < arguments.length; i++) {
                if (arguments[i] != undefined) {
                    var reg = new RegExp("({)" + i + "(})", "g");
                    result = result.replace(reg, arguments[i]);
                }
            }
        }
    }
    return result;
};

console.log("name:{0},content:{1}".format("L","test!!")) //name:L,content:test!!      
```
###  数组取最大值和最小值

```javascript
 
Array.prototype.max = function () {
        return Math.max.apply({}, this)
    }

  
Array.prototype.min = function () {
        return Math.min.apply({}, this) //apply()  :  接收两个参数，第一个是函数运行的作用域（this），第二个是参数数组。
    }
          
```

###  数组排序

```javascript
//从小到大	
function sortby(a,b) {
      return a-b
}
arr.sort(sortby) //console.log(arr);

//按照数组对象中某个属性值进行排序
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
###  验证非法字符

```javascript
 function checkStr(strTest) {
     var pat = new RegExp("[^a-zA-Z0-9\_\u4e00-\u9fa5]", "i");
     var strArr = strTest.split("");
     for (var i = 0; i < strArr.length; i++) {
         var str = strTest.charAt(i);
         if (pat.test(strArr[i]) == true) {
             strArr[i] = "()";
             console.log("含有非法字符");
             return false
         }
     }
     return true
 }    
```

### 深拷贝对象(递归思想)

```javascript
function cloneObj(obj) {
   var newObj;
   if (Object.prototype.toString.call(obj) === '[object Array]') {
       newObj = [];
   } else if (Object.prototype.toString.call(obj) === '[object Object]') {
       newObj = {};
   } else {
       return obj;
   }

   for (const key in obj) {
       newObj[key] = cloneObj(obj[key]);
   }
   return newObj
}    
```


### 判断JSON字符串

```javascript
function isJSON(str) {
    if (typeof str == 'string') {
        try {
            var obj = JSON.parse(str);
            if (typeof obj == 'object' && obj) {
                return true;
            } else {
                return false;
            }

        } catch (e) {
            console.log('error：' + str + '!!!' + e);
            return false;
        }
    }
    console.log('It is not a string!')
    return false;
}    
```

### xml 与 json互转(node.js)
###### 需要引入xml2js
```javascript
exports.Object2XML = function (data) {
	var b = new xml2js.Builder();
	var content = b.buildObject(data);
	return content;
}


exports.XML2Objdect = function (result,callback) {
	var xmlParser = new xml2js.Parser({ explicitArray: false, ignoreAttrs: true }); // xml -> json
	xmlParser.parseString(result, function (err, data) {
		// console.log('xml解析成json:',data);
		callback(data)
	});
}  
```

### 获取所有的排列组合(不论顺序)

```javascript
   function getGroup(data, index = 0, group = []) {
      var need_apply = new Array();
      need_apply.push(data[index]);
      for (var i = 0; i < group.length; i++) {
        need_apply.push(group[i] +"_"+ data[index]);
      }
      group.push.apply(group, need_apply);

      if (index + 1 >= data.length) return group;
      else return getGroup(data, index + 1, group);
    }
    
    //console.log(getGroup([6, 5, 4]));    输出: [6, 5, "6_5", 4, "6_4", "5_4", "6_5_4"]
```

### 获取url参数

```javascript
   function GetRequest() {
            var url = location.search; 
            var theRequest = new Object();
            if (url.indexOf("?") != -1) {
                var str = url.substr(1);
                strs = str.split("&");
                for (var i = 0; i < strs.length; i++) {
                    theRequest[strs[i].split("=")[0]] = (strs[i].split("=")[1]);
                }
            }
            return theRequest;
        }
```
### 拼接url数据
```javascript
  var formData = function (data) {
        var str = ""
        for (var k in data) {
            if (str != "") {
                str += "&";
            }
            str += k + "=" + data[k];
        }
        return str
    }
```
