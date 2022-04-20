# 自定义方法

## 链式取值（解析字符串）

```javascript
/**
 * 链式取值
 * @param {object} obj 取值对象
 * @param {string} str 取值的链式键
 */
const _get = (obj, str) => {
    if(obj === null || obj === undefined || str === '' || typeof str !== 'string') return str
    const temp = str.split('.')
    let value = null
    for (const e of temp) {
        if(value === null) value = obj[e]
        else value = value[e]
        if(value === undefined) break
    }
    return value === undefined ? str : value
}

// 例子
const data = {
    a: {
        b: 'Hello World'
    }
}

console.log(_get(data, 'a.b')); // 'Hello World'
console.log(_get(data, 'a.c')); // 'a.c'
```

## 格式化三分位

```javascript
/**
 * @description 数字三分位，传入数字类型，非数字的值返回其本身或0
 * @param { number } 需要格式化的值
 */
const numberFormat = (num) => {
  if (!num || isNaN(num)) return num || 0
  return num.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',')
}

console.log(numberFormat('1234.56789')); // '1,234.56,789'
console.log(numberFormat(56789.3463746)); // '56,789.3,463,746'
console.log(numberFormat('hello')); // 'hello'
```

## 获取数据类型

```javascript
/**
 * @description 获取数据类型
 * @param { * } 需要查询类型的值
 */
const dateType = (value) => {
  return Object.prototype.toString.call(value).slice(8, -1)
}

console.log(dateType('1')); 		// String
console.log(dateType(1)); 			// Number
console.log(dateType(undefined)); 	// 'Undefined'
console.log(dateType(null)); 		// 'Null'
console.log(dateType(true)); 		// 'Boolean'
console.log(dateType([])); 			// 'Array'
console.log(dateType({})); 			// 'Object'
console.log(dateType(new Date())); 	// 'Date'
console.log(dateType(Math)); 		// 'Math'
console.log(dateType(() => {})); 	// 'Function'
console.log(dateType(/\./)); 		// 'RegExp'
```

## 获取图片详细信息

```javascript
/**
 * @description 获取图片信息
 * @param { string } url 图片路径
 */
export const imgInfo = (url) => {
  return new Promise((resolve, reject) => {
    if (!url) resolve()
    var img = new Image()
    // 设置跨域
    img.setAttribute('crossOrigin', 'Anonymous')
    img.src = url
    img.onload = () => {
      const { width, height } = img
      resolve({ width, height })
    }
  })
}
```

## 查看对象实例属性

```javascript
let description = ''
for (const i in obj) {
    description += `${i} = ${obj[i]} \n`
}
console.log(description)
```

## 正则提取html标签内容

```javascript
const str = "<div style='color: #f60'>等待提取的内容</div>"
const result = str.replace(/<[^>]*>|/g, '') // => 等待提取的内容
```

## 判断属性是否存在

- 老写法：in运算符

```javascript
const obj = {
    name: 'hello'
}

'name' in obj // => true
'age' in obj // => false
```

- 新写法：Reflect

```javascript
const obj = {
    name: 'hello'
}

Reflect.has(obj, 'name') // => true
Reflect.has(obj, 'age') // => false
```

- 判断自身属性

```javascript
class Person {
	constructor() {
		this.name = 'hello'
	}
}
Person.prototype.age = 18
const person = new Person()

person.hasOwnProperty('name') // => true
person.hasOwnProperty('age') // => true
```

