# Token

标签（空格分隔）： Pascal解释器

---

程序中有意义的词

## Token类

记录Token的类型和之

```javascript
export default class Token {
  constructor (type, value) {
    this.type = type
    this.value = value
  }
}
```

## Token类型

```javascript
const tokenToType = {
  ...reservedTokenToType,
  ...identifierTokenToType,
  ...literalTokenToType,
  ...singlePunctuatorTokenToType,
  ...binaryPunctuatorTokenToType,
  ...eofTokenToType
}

function buildTokenType () {
  let result = {}
  Object.values(tokenToType).forEach(type => result[type] = Symbol(type))
  return result
}

export const TOKEN_TYPE = buildTokenType()
```

### 保留字

```javascript
export const reservedTokenToType = {
  PROGRAM: 'PROGRAM',
  VAR: 'VAR',
  INTEGER: 'INTEGER',
  REAL: 'REAL',
  PROCEDURE: 'PROCEDURE',
  BEGIN: 'BEGIN',
  END: 'END',
  DIV: 'INTEGER_DIV'
}
```

### 标识符

```javascript
const identifierTokenToType = {
  ID: 'ID'
}
```

### 字面量

```javascript
const literalTokenToType = {
  INTEGER_CONST: 'INTEGER_CONST',
  FLOAT_CONST: 'FLOAT_CONST'
}
```

### 单字符符号

```javascript
export const singlePunctuatorTokenToType = {
  '.': 'DOT',
  ',': 'COMMA',
  ':': 'COLON',
  ';': 'SEMI',
  '+': 'PLUS',
  '-': 'MINUS',
  '*': 'MUL',
  '/': 'FLOAT_DIV',
  '(': 'LPAREN',
  ')': 'RPAREN'
}
```

### 双字符符号

```javascript
export const binaryPunctuatorTokenToType = {
  ':=': 'ASSIGN'
}
```

### 结束符

```javascript
const eofTokenToType = {
  EOF: 'EOF'
}
```
