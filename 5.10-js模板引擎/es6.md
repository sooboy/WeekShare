## es6 模板字符串
### 介绍
- 模板字符串是 es6 中我最爱的特性啦！比起传统模板引擎，我更喜欢用模板字符串来编写组件
- 模板字符串包裹在 反引号(Esc按钮下面那个) 中，其中可通过 ${} 的语法进行插值
```javascript
// 特性一:多行
`123123
 23213`

// 特性二: 字符串中可插值（强大的不要不要的）
/* 作为一门伪函数式编程语言，js 的很多语法都可以返回数据:
*    - 表达式: 各种运算符表达式，三目(可用来替代简单的判断语句)
*    - 函数:  封装各种复杂的逻辑，最后返回一个值即可
*    - 方法:  如一些有返回值的数据方法
*       - 最强大的如数组的 map, filter ...
*/
// 以下字符串都等于 '123tpl456'
const str1 = `123${'tpl'}456`
const str2 = `123${false || 'tpl'}456`
const str3 = `123${true ? 'tpl' : ''}456`
const str4 = `123${ (function () {return 'tpl'}()) }456`
const fn = () => 'tpl'
const str5 = `123${ fn() }456`
const str6 = `123${
    ['T', 'P', 'L'].map(s => s.toLowerCase()).join('')
}456`
console.log([str1, str2, str3, str4, str5, str6].every(s => s === '123tpl456'))

// 特性三: 模板函数 (个人很少用到)
var a = 5, b = 10
function tag (strArr, ...vals) {
    console.log(strArr, vals)
}
tag`Hello ${ a + b } world ${a * b}`
// strArr => ['Hello ', ' world ', '']
// vals   => [15, 30]  (${}里的值)
```

### 案例
- 由于直接用模板字符串当模板引擎了，所以就直接写个组件吧
    - [演示](http://codepen.io/Away0x/pen/dvEGpL)
    - [代码](https://github.com/Away0x/learning-template/blob/master/src/tplstr.pagination/page.js)
- 用这种方法写模板需注意的是一定要细分组件(很函数式，有种写 jsx 的既视感)

### 资料
- [详细语法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/template_strings)
- [相关文章](http://www.pdosgk.com/index.php/home/news/show/id/80442.html)

***