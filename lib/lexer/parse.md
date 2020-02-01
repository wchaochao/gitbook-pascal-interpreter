# 词法分析

标签（空格分隔）： Pascal解释器

---

## Lexer类

* 将源代码文本转换为源代码字符流
* 将源代码字符流转换为Token流

```javascript
export default class Lexer {
  // ...
}
```

## constructor()

记录当前解析的字符位置、字符值

```javascript
  constructor (text) {
    this.text = text
    this.pos = 0
    this.currentChar = this.text[this.pos]
  }
```

## advanceBy()

消费n个字符

```javascript
  advanceBy (n = 1) {
    this.pos += n
    if (this.pos > this.text.length - 1) {
      this.currentChar = null
    } else {
      this.currentChar = this.text[this.pos]
    }
  }
```

## advanceWhen()

消费符合条件的字符

```javascript
  advanceWhen (callback) {
    let result = ''
    while (this.currentChar && callback(this.currentChar)) {
      result += this.currentChar
      this.advanceBy()
    }
    return result
  }
```

## peekBy()

窥探第n个字符

```javascript
  peekBy (n = 1) {
    const pos = this.pos + n
    if (pos > this.text.length - 1) {
      return null
    } else {
      return this.text[pos]
    }
  }
```

## getNextToken()

获取下一个Token

```javascript
  getNextToken () {
    while (this.currentChar) {
      let char = this.currentChar
      // ...

      this.error()
    }
    return new Token(TOKEN_TYPE.EOF, null)
  }
```

### 空白符

跳过空白符

```javascript
      if (isWhitespace(char)) {
        this.whitespace()
        continue
      }
```

```javascript
  whitespace () {
    this.advanceWhen(isWhitespace)
  }
```

### 注释

跳过注释

```javascript
      if (char === '{') {
        this.comment()
        continue
      }
```

```javascript
  comment () {
    this.advanceWhen(isComment)
    if (this.currentChar === '}') {
      this.advanceBy()
    } else {
      throw new Error('单行注释不能换行')
    }
  }
```

### 标识符

生成关键字或标识符Token

```javascript
      if (isAlpha(char)) {
        return this.id()
      }
```

```javascript
  id () {
    const id = this.advanceWhen(isAlnum).toUpperCase()
    const keyword = TOKEN_TYPE[reservedTokenToType[id]]
    const type = keyword || TOKEN_TYPE.ID
    return new Token(type, id)
  }
```

### 字面量

生成整数或小数Token

```javascript
      if (isInteger(char)) {
        return this.number()
      }
```

```javascript
  number () {
    let result = this.advanceWhen(isInteger)
    if (this.currentChar === '.') {
      this.advanceBy()
      result += '.' + this.advanceWhen(isInteger)
      const number = parseFloat(result)
      return new Token(TOKEN_TYPE.FLOAT_CONST, number)
    } else {
      const number = parseInt(result)
      return new Token(TOKEN_TYPE.INTEGER_CONST, number)
    }
  }
```

### 双字符符号

生成双字符符号Token

```javascript
      let binaryPunctuator = char + this.peekBy()
      let binaryType = TOKEN_TYPE[binaryPunctuatorTokenToType[binaryPunctuator]]
      if (binaryType) {
        this.advanceBy(2)
        return new Token(binaryType, binaryPunctuator)
      }
```

### 单字符符号

生成单字符符号Token

```javascript
      let type = TOKEN_TYPE[singlePunctuatorTokenToType[char]]
      if (type) {
        this.advanceBy()
        return new Token(type, char, this.positionInfo)
      }
```
