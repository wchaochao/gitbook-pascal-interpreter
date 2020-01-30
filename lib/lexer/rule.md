# 词法规则

标签（空格分隔）： Pascal解释器

---

规定源代码字符如何组成有意义的单词

**非Token类**

## 空白符

用于改善源代码的可读性、分隔Token

* 空白字符：空格符、制表符、换页符等
* 行终结符：回车符、换行符、行分隔符、段分隔符等

```javascript
export function isWhitespace (char) {
  return /^\s$/.test(char)
}
```

## 注释

解释源代码

* 单行注释：`{ 注释 }`

```javascript
export function isComment (char) {
  return char !== '}' && !isTerminator(char)
}

export function isTerminator (char) {
  return ['\u000A', '\u000D', '\u2028', '\u2028'].indexOf(char) !== -1
}
```

**Token类**

## 标识符名

特定名称

### 标识符

变量名、过程名、形参名等

* 字母、数字、下划线组成
* 数字不开头
* 不区分大小写
* 不为关键字

```javascript
export function isAlpha (char) {
  return /^[a-zA-z_]$/.test(char)
}

export function isAlnum (char) {
  return /^\w$/.test(char)
}
```

### 关键字

有特定意义的词

* `PROGRAM`: 程序开头
* `VAR`: 变量声明
* `INTEGER`: 整数类型
* `REAL`: 浮点类型
* `PROCEDURE`: 过程声明
* `BEGIN`: 复合语句开头
* `END`: 复合语句结尾
* `DIV`: 整数除法

## 字面量

* 整数：如1
* 小数：如1.2

```javascript
export function isInteger (char) {
  return /^\d$/.test(char)
}
```

## 符号

### 运算符号

* `+`: 加法
* `-`: 减法
* `*`: 乘法
* `/`: 除法
* `(`: 括号（左）
* `)`: 括号（右）

### 语句符号

* `;`: 语句结尾
* `,`: 多个变量声明分隔
* `:`: 类型声明
* `.`: 主程序结尾
* `:=`: 赋值
