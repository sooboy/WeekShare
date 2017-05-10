### 介绍
replace 是字符串提供的一个超级强大的方法，这里只介绍简单的使用，更多详细的语法参见下面提供的链接
- 一参可为 `字符串` 或 `正则`:
    - 为正则时有两种情况: `普通匹配模式` 和 `全局匹配模式`:
        - 全局匹配模式下，若二参为函数，则该函数在每次匹配时都会被调用
- 二参可为 `字符串` 或 `一个用于生成字符串的函数`:
    - 当为字符串时:
        - 可在字符串中使用 特殊替换字符 ($n ...)
    - 当为函数时:
        - 函数中不能用 特殊替换字符
        - 一参为正则匹配的文本
        - 倒数第二参为匹配到的子字符串在原字符串中的偏移量
        - 最后一参为被匹配的原始字符串
        - 其余参数为正则中每个分组匹配到的文本
        ```javascript
            function replacer(match, p1, p2, p3, offset, string) {
                // match 为 'abc12345#$*%'        : 正则匹配的文本
                // p1,p2,p3 分别为 abc 12345 #$*% : 即每个小组匹配到的文本, pn 表示有 n 个小组
                // offset 为 0                    : 匹配到的子字符串在原字符串中的偏移量。
                //      （比如，如果原字符串是'abcd'，匹配到的子字符串时'bc'，那么这个参数将是1）
                // string 为 'abc12345#$*%'       : 被匹配的原始字符串
                return [p1, p2, p3].join(' - ');
            }
            // 注意正则中的 括号 ，这里分有 3个组
            var newString = 'abc12345#$*%'.replace(/([^\d]*)(\d*)([^\w]*)/, replacer);
            // newString => 'abc - 12345 - #$*%'
        ```

以下是各种情况下的具体案例:
```javascript
// 最基础的使用
'123'.replace('1', 'A') // 'A23'
'lalala 2Away0x2'.replace(/2(.*)2/, '$1') // 'lalala Away0x'

// trim
const trim = str => str.replace(/(^\s*)|(\s*$)/g, '')

trim('  abc    ') // 'abc'

// format
const format = str =>
    (...args) =>
        str.replace(/{(\d+)}/g, (match, p) => args[p] || '')

format('lalal{0}wowowo{1}hahah{2}')('-A-', '-B-', '-C') // lala-A-wowo-B-haha-C
```

### 原理
先在模板中预留占位( {{填充数据}} )，再将对应的数据填入

### 实现
要求1: 可填充简单数据
```javascript
const tpl = (str, data) => str.replace(/{{(.*)}}/g, (match, p) => data[p])
tpl('<div>{{data}}</div>', {data: 'tpl'}) // '<div>tpl</div>'
```

要求2: 可填充嵌套数据
```javascript
// 可根据占位符 {{data.a}} 中的 "." 来获得数据的依赖路径，从而得到对应的数据
// 由于 使用 "." 连接，所以其前后应为合法的变量名，因此需重新构造正则
/* 合法变量名
*    - 开头可为字符和少量特殊字符: [a-zA-Z$_]
*    - 余部还可是数字:            [a-zA-Z$_0-9]
*/
// 除开头外还需匹配 连接符 "." ,因此最终正则为: /{{([a-zA-Z$_][a-zA-Z$_0-9\.]*)}}/g
function tpl (str, data) {
    const reg = /{{([a-zA-Z$_][a-zA-Z$_0-9\.]*)}}/g

    // 全局匹配模式下，replace 的回调在每次匹配时都会执行,
    // p 为占位符中的变量,该例为 data.a
    return str.replace(reg, (match, p) => {
        const paths = p.split('.') // ['data', 'a']
        let result  = data

        while (paths.length > 0)
            result = result[ paths.shift() ] // 得到路径最末端的数据
        return String(result) || match // 需转成字符串，因为可能遇到 0, null 等数据
    })
}
tpl('<div>{{data.a}}</div>', {data: {a: 'tpl'}}) // '<div>tpl</div>'
```

最终代码:
```javascript
function tpl (str, data) {
    const reg = /{{([a-zA-Z$_][a-zA-Z$_0-9\.]*)}}/g

    return str.replace(reg, (match, p) => {
        const paths = p.split('.')
        let result  = data

        while (paths.length > 0)
            result = result[ paths.shift() ]
        return String(result) || match
    })
}
```

### 优缺点
- 优点: 简单
- 缺点: 无法在模板中使用表达式，所有数据都得事先计算好再填入，且填充的数据应为基础类型，灵活性差，难以满足复杂的需求

### 资料
[详细语法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace)
