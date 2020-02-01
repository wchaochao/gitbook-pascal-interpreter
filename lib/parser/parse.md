# 语法分析

标签（空格分隔）： Pascal解释器

---

## Parser

语法解析器，用于将Token转换为AST节点

```javascript
export default class Parser {
  constructor (lexer) {
    this.lexer = lexer
    this.currentToken = lexer.getNextToken()
  }

  // ...

  parse () {
    return this.program()
  }
}
```

## eat()

消费当前Token，获取下一个Token，并在语法错误的时候报错

```javascript
  eat (type) {
    let token = this.currentToken
    if (token.type === type) {
      this.currentToken = this.lexer.getNextToken()
      return token
    } else {
      this.error()
    }
  }
```

## number()

解析number语法，生成Num节点

```javascript
  number () {
    if (this.currentToken.type === TOKEN_TYPE.INTEGER_CONST) {
      return new Num(this.eat(TOKEN_TYPE.INTEGER_CONST))
    } else {
      return new Num(this.eat(TOKEN_TYPE.FLOAT_CONST))
    }
  }
```

## variable()

解析variable语法，生成Var节点

```javascript
  variable () {
    return new Var(this.eat(TOKEN_TYPE.ID))
  }
```

## factor()

解析factor语法，生成相应的值节点

```javascript
  factor () {
    let type = this.currentToken.type
    if (include(type, ['INTEGER_CONST', 'FLOAT_CONST'])) {
      return new Num(this.eat(type))
    }

    if (type === TOKEN_TYPE.ID) {
      return this.variable()
    }

    if (type === TOKEN_TYPE.LPAREN) {
      this.eat(TOKEN_TYPE.LPAREN)
      let node = this.expr()
      this.eat(TOKEN_TYPE.RPAREN)
      return node
    }

    this.error()
  }
```

## unary()

解析unary语法，生成UnaryOp节点

```javascript
  unary () {
    let type = this.currentToken.type
    if (include(type, ['PLUS', 'MINUS'])) {
      return new UnaryOp(this.eat(type), this.factor())
    } else {
      return this.factor()
    }
  }
```

## term()

解析term语法，生成BinOp节点

```javascript
  term () {
    let node = this.unary()
    while (include(this.currentToken.type, ['MUL', 'DIV', 'FLOAT_DIV'])) {
      node = new BinOp(node, this.eat(this.currentToken.type), this.unary())
    }
    return node
  }
```

## expr()

解析expr语法，生成BinOp节点

```javascript
  expr () {
    let node = this.term()
    while (include(this.currentToken.type, ['PLUS', 'MINUS'])) {
      node = new BinOp(node, this.eat(this.currentToken.type), this.term())
    }
    return node
  }
```

## empty()

解析empty语法，生成NoOp节点

```javascript
  empty () {
    return new NoOp()
  }
```

## assignment()

解析assignment语法，生成Assign节点

```javascript
  assignment () {
    return new Assign(this.variable(), this.eat(TOKEN_TYPE.ASSIGN), this.expr())
  }
```

## procedureCall()

解析procedureCall语法，生成ProcedureCall节点

```javascript
  procedureCall () {
    const token = this.eat(TOKEN_TYPE.ID)
    this.eat(TOKEN_TYPE.LPAREN)
    let params = []
    if (this.currentToken.type !== TOKEN_TYPE.RPAREN) {
      params.push(this.expr())
      while (this.currentToken.type === TOKEN_TYPE.COMMA) {
        this.eat(TOKEN_TYPE.COMMA)
        params.push(this.expr())
      }
    }
    this.eat(TOKEN_TYPE.RPAREN)
    return new ProcedureCall(token, params)
  }
```

## statement()

解析statement语法，生成相应的语句节点

```javascript
  statement () {
    let type = this.currentToken.type
    if (type === TOKEN_TYPE.BEGIN) {
      return this.compound()
    }

    if (type === TOKEN_TYPE.ID) {
      if (this.lexer.currentChar === '(') {
        return this.procedureCall()
      } else {
        return this.assignment()
      }
    }

    return this.empty()
  }
```

## statementList()

解析statementList语法，生成语句节点数组

```javascript
  statementList () {
    let statements = [this.statement()]
    while (this.currentToken.type === TOKEN_TYPE.SEMI) {
      this.eat(this.currentToken.type)
      statements.push(this.statement())
    }
    if (this.currentToken.type === TOKEN_TYPE.ID) {
      this.error()
    }
    return statements
  }
```

## compound()

解析compound语法，生成Compound节点

```javascript
  compound () {
    this.eat(TOKEN_TYPE.BEGIN)
    let node = new Compound(this.statementList())
    this.eat(TOKEN_TYPE.END)
    return node
  }
```

## type()

解析type语法，生成Type节点

```javascript
  type () {
    if (include(this.currentToken.type, ['INTEGER', 'REAL'])) {
      return new Type(this.eat(this.currentToken.type))
    }
    this.error()
  }
```

## variableDeclaration()

解析variableDeclaration语法，生成VarDecl节点数组

```javascript
  variableDeclaration () {
    let varNodes = [this.variable()]
    while (this.currentToken.type === TOKEN_TYPE.COMMA) {
      this.eat(this.currentToken.type)
      varNodes.push(this.variable())
    }
    this.eat(TOKEN_TYPE.COLON)
    let typeNode = this.type()
    return varNodes.map(node => new VarDecl(node, typeNode))
  }
```

## variableDeclarationList()

解析variableDeclarationList语法，生成VarDecl节点数组

```javascript
  variableDeclarationList () {
    let result = []
    while (this.currentToken.type === TOKEN_TYPE.ID) {
      result = result.concat(this.variableDeclaration())
      this.eat(TOKEN_TYPE.SEMI)
    }
    return result
  }
```

## formalParam()

解析formalParam语法，生成Param节点数组

```javascript
  formalParam () {
    let varNodes = [this.variable()]
    while (this.currentToken.type === TOKEN_TYPE.COMMA) {
      this.eat(this.currentToken.type)
      varNodes.push(this.variable())
    }
    this.eat(TOKEN_TYPE.COLON)
    let typeNode = this.type()
    return varNodes.map(node => new Param(node, typeNode))
  }
```

## formalParamList()

解析formalParamList语法，生成Param节点数组

```javascript
  formalParamList () {
    let result = this.formalParam()
    while (this.currentToken.type === TOKEN_TYPE.SEMI) {
      this.eat(this.currentToken.type)
      result = result.concat(this.formalParam())
    }
    return result
  }
```

## procedureDeclaration()

解析procedureDeclaration, 生成ProcedureDecl节点

```javascript
  procedureDeclaration () {
    this.eat(TOKEN_TYPE.PROCEDURE)
    const id = this.eat(TOKEN_TYPE.ID)

    let params = []
    if (this.currentToken.type === TOKEN_TYPE.LPAREN) {
      this.eat(this.currentToken.type)
      params = this.formalParamList()
      this.eat(TOKEN_TYPE.RPAREN)
    }

    this.eat(TOKEN_TYPE.SEMI)
    const block = this.block()
    let result = new ProcedureDecl(id, params, block)
    this.eat(TOKEN_TYPE.SEMI)
    return result
  }
```

## procedureDeclarationList()

解析procedureDeclaration, 生成ProcedureDecl节点数组

```javascript
  procedureDeclaration () {
    this.eat(TOKEN_TYPE.PROCEDURE)
    const id = this.eat(TOKEN_TYPE.ID)

    let params = []
    if (this.currentToken.type === TOKEN_TYPE.LPAREN) {
      this.eat(this.currentToken.type)
      params = this.formalParamList()
      this.eat(TOKEN_TYPE.RPAREN)
    }

    this.eat(TOKEN_TYPE.SEMI)
    const block = this.block()
    let result = new ProcedureDecl(id, params, block)
    this.eat(TOKEN_TYPE.SEMI)
    return result
  }
```

## declarations

解析declarations语法，生成声明节点数组

```javascript
  declarations () {
    let result = []

    while (this.currentToken.type === TOKEN_TYPE.VAR) {
      this.eat(this.currentToken.type)
      result = result.concat(this.variableDeclarationList())
    }

    while (this.currentToken.type === TOKEN_TYPE.PROCEDURE) {
      result.push(this.procedureDeclaration())
    }

    return result
  }
```

## block

解析block语法，生成Block节点

```javascript
  block () {
    return new Block(this.declarations(), this.compound())
  }
```

## program

解析program语法，生成Program节点

```javascript
  program () {
    this.eat(TOKEN_TYPE.PROGRAM)
    let name = this.eat(TOKEN_TYPE.ID).value
    this.eat(TOKEN_TYPE.SEMI)
    let block = this.block()
    this.eat(TOKEN_TYPE.DOT)
    return new Program(name, block)
  }
```
