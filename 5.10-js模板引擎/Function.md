## new Function
### 介绍
- Function 是 js 提供的一个用于构造 Function 对象的构造函数
- 使用:
    ```javascript
    // 普通函数
    function log (user, msg) {
        console.log(user, msg)
    }
    log('Away0x', 'lalala')
    // Function 构造函数
    const log = new Function('user', 'msg', 'console.log(user, msg)')
    log('Away0x', 'lalala')
    ```

### 实现
- 大多数前端模板引擎都是用这种方式实现的，其原理在于运用了 js Function 对象可将字符串解析为函数的能力。
- 一个普通模板引擎的工作步骤大致如下:
```javascript
// 1. 编写模板
{@ if( data.con > 20 ) { @}
    <p>ifififififif</p>
{@ } else { @}
    <p>elseelseelseelse</p>
{@ } @}
// 2. 由模板生成函数体字符串
const functionbody = `
    var tpl = ''
    if (data.con > 20) {
        tpl += '<p>ifififififif</p>'
    } else {
        tpl += '<p>ifififififif</p>'
    }
    return tpl
`
// 3. 通过 Function 解析字符串并生成函数
new Function('data', functionbody)(data)
```
- 由此可见，只要将 {@  @} 里的字符串内容生成 js 语句，而其余内容之前加上 一个 'tpl += ' 即可。
- 实现代码如下:
```javascript
const tpl = (str, data) => {
    const tplStr = str.replace(/\n/g, '')
        .replace(/{{(.+?)}}/g, (match, p) => `'+(${p})+'`)
        .replace(/{@(.+?)@}/g, (match, p) => `'; ${p}; tpl += '`)
    // console.log(tplStr)
    return new Function('data', `var tpl='${tplStr}'; return tpl;`)(data)
}
// 测试
const str = `
    {@ if( data.con > 20 ) { @}
        <p>ifififififif</p>
    {@ } else { @}
        <p>elseelseelseelse</p>
    {@ } @}

    {@ for(var i = 0; i < data.list.length; i++) { @}
    	<p>{{i }} : {{ data.list[i] }}</p>
    {@ } @}
`
const data = {con:21, list: [1,2,3,4,5,76,87,8]}
console.log( tpl(str, data) )
// <p>ifififififif</p>
// <p>0 : 1</p><p>1 : 2</p><p>2 : 3</p><p>3 : 4</p><p>4 : 5</p><p>5 : 76</p><p>6 : 87</p><p>7 : 8</p>
```
ok, 一个最最简单的模板引擎就已经完成了，支持在模板中嵌入 js 语句，虽然只有不到10行，但还是挺强大的对不。