---
layout:     post
title:      "Javascript基础整理"
subtitle:   ""
date:       2017-12-28
author:     "Fiona"
header-img: "img/linux-bg.jpg"
tags:
    - mobile
    - web
    - 原创
---

> 顺带整理ES6新特性

### 变量
<img src="/blog/img/in-post/post-javascript-review/variable.png">

### JS正则
#### RegExp三大方法
##### test	
检测指定字符串中是否含有该模式匹配
```javascript
var s = "you and I, you are handsome";
var pattern = /you/;
pattern.test(s);   // true
```
##### exec	
提取指定字符串中符合要求的匹配模式，返回一个数组存放结果
输出的数组，第0个元素是与正则表达式相匹配的字符串，第1个元素是与RegExpObject匹配的第一个子表达式相匹配的文本。第2个元素是与RegExpObject匹配的第二个子表达式相匹配的文本。以此类推。
```javascript
var s = 'you love me and I love you';
    var pattern = /you/;
    var ans = pattern.exec(s);
    console.log(ans); // ["you", index: 0, input: "you love me and I love you"]
    console.log(ans.index); // 0
    console.log(ans.input); // you love me and I love you
```
第一次调用exec只会返回首次匹配成功的文本，我们可以通过反复调用 exec() 方法来遍历字符串中的所有匹配文本。（一定要在全局模式下）
```javascript
var s = 'you love me and I love you';
var pattern = /you/g;
var ans;
do {
  ans = pattern.exec(s);
  console.log(ans);
  console.log(pattern.lastIndex);
}
while (ans !== null)
// 输出
// ["you",index:0,input:"you love me and I love you"]
// 3
// ["you", index: 23, input: "you love me and I love you"]
// 26
// null
// 0
```
##### compile
改变当前匹配模式，不常用。
#### String正则相关
四大方法：str.search/match/replace/split
##### search
与String的indexOf方法功能相同，都是返回匹配项的位置，不同是不仅可以匹配字符串也可以匹配正则模式。
##### match
match是exec的轻量版，当不使用全局模式匹配时，match和exec返回结果一致；当使用全局模式匹配时，match直接返回一个字符串数组，获得的信息远没有exec多，但是使用方式简单。
var s = 'you love me and I love you';
    console.log(s.match(/you/));    // ["you", index: 0, input: "you love me and I love you"]
    console.log(s.match(/you/g));   // ["you", "you"]
##### replace
用另一个子串替换指定字符串中的某子串（或者匹配模式），返回替换后的新的字符串  str.replace(‘搜索模式’,'替换的内容’)  如果用的是pattern并且带g，则全部替换；否则替换第一处。
```javascript
var s = 'you love me and I love you';
console.log(s.replace('you', 'zichi')); // zichi love me and I love you
console.log(s.replace(/you/, 'zichi')); // zichi love me and I love you
console.log(s.replace(/you/g, 'zichi'));    // zichi love me and I love zichi
```

如果需要替代的内容不是指定的字符串，而是跟匹配模式或者原字符串有关，那么就要用到$了（记住这些和$符号有关的东东只和replace有关哦）。
```javascript
var s = 'I love you';
var pattern = /love/;
var ans = s.replace(pattern, '$`' + '$&' + "$'");
console.log(ans); // I I love you you

var ans  = 'I love you';s.replace(/(love)\s(you)/, '$2 $1')
console.log(ans); //"I you love"
```
replace的第二个参数也可以是函数：
```javascript
var s = 'I love you';
s.replace(/love/, function(matched, index, input) {
console.log('matched: ' + matched); 
console.log("index : " + index); 
console.log('input: '+ input); 
return input
});

// 输出： 
// matched: love
// index: 2
// input: I love you
// "I I love you you"
```

##### split
按照模式或者字符串分割原来的字符串
```javascript
var s = 'you love me and I love you';
var pattern = 'and';
var ans = s.split(pattern);
console.log(ans);   // ["you love me ", " I love you"]
```

#### RegExp 字符
<img src="/blog/img/in-post/post-javascript-review/reg1.png">
<img src="/blog/img/in-post/post-javascript-review/reg2.png">
<img src="/blog/img/in-post/post-javascript-review/reg3.png">
#### 高级正则
##### 后向引用
子表达式的序号问题 
简单地说：从左向右，以分组的左括号为标志，第一个出现的分组的组号为1，第二个为2，以此类推。  
复杂地说：分组0对应整个正则表达式实际上组号分配过程是要从左向右扫描两遍的：第一遍只给未命名组分配，第二遍只给命名组分配－－因此所有命名组的组号都大于未命名的组号。可以使用(?:exp)这样的语法来剥夺一个分组对组号分配的参与权． 
后向引用举例：
```javascript
var s = 'hellohellochinaworldworld';
var pattern = /(\w+)\1/g;
var a = s.match(pattern);
console.log(a); // ["hellohello", "worldworld"]
```
##### 零宽断言
别被名词吓坏了，其实解释很简单。
它们用于查找在某些内容(但并不包括这些内容)之后的东西，也就是说它们像\b,^,$那样用于指定一个位置，这个位置应该满足一定的条件(即断言)
(?=exp)
零宽度正预测先行断言，它断言自身出现的位置的后面能匹配表达式exp。
// 获取字符串中以ing结尾的单词的前半部分
```javascript
var s = 'I love dancing but he likes singing';
var pattern = /\b\w+(?=ing\b)/g;
var ans = s.match(pattern);
console.log(ans); // ["danc", "sing"]
```
(?!exp)
零宽度负预测先行断言，断言此位置的后面不能匹配表达式exp
```javascript
// 获取第五位不是i的单词的前四位
var s = 'I love dancing but he likes singing';
var pattern = /\b\w{4}(?!i)/g;
var ans = s.match(pattern);
console.log(ans); // ["love", "like"]
```
javascript正则只支持前瞻，不支持后瞻（(?<=exp)和(?<!exp)）。

##### 贪婪与懒惰匹配
默认贪婪匹配。使用？执行懒惰匹配。
```javascript
var s = "I don't like you but I love you";
var pattern = /I.*(like|love).*you/g;
var ans = s.match(pattern);
console.log(ans); // ["I don't like you but I love you"]
```
答案执行了贪婪匹配，如果需要懒惰匹配，则：
```javascript
var s = "I don't like you but I love you";
var pattern = /I.*?(like|love).*?you/g;
var ans = s.match(pattern);
console.log(ans); // ["I don't like you", "I love you"]
```
<img src="/blog/img/in-post/post-javascript-review/reg4.png" style="margin-left:0">
