# Neon Distillation
Neon 가스는 liquid air를 fractional distillation으로 생산한다.
이 project에서는 Neon language의 syntax를 실험한다.

참고
* [Neon - Wikipedia](https://en.wikipedia.org/wiki/Neon)

## 다른 언어 조사

### Go

* [The Go Programming Language Specification](https://golang.org/ref/spec)

* `type`, `func`등 처음에 무엇인지를 적도록 해서 parsing을 쉽게 함.

* Rune: \uXXXX, \UXXXXXXXX

* string: immutable

* slice: array를 storage로 사용
  - immutable한 string의 일부 data를 slice로 끊어내는 방식으로 text 기반의 HTTP protocol parsing을 효율적으로 할 수 있다.

* iota: constant generator

* class밖에 method를 선언해서, 확장이 쉬움
  - receiver는 함수의 첫번째 parameter임

```
newline        = /* the Unicode code point U+000A */ .
unicode_char   = /* an arbitrary Unicode code point except newline */ .
unicode_letter = /* a Unicode code point classified as "Letter" */ .
unicode_digit  = /* a Unicode code point classified as "Number, decimal digit" */ .

letter        = unicode_letter | "_" .
decimal_digit = "0" … "9" .
octal_digit   = "0" … "7" .
hex_digit     = "0" … "9" | "A" … "F" | "a" … "f" .

identifier = letter { letter | unicode_digit } .

int_lit     = decimal_lit | octal_lit | hex_lit .
decimal_lit = ( "1" … "9" ) { decimal_digit } .
octal_lit   = "0" { octal_digit } .
hex_lit     = "0" ( "x" | "X" ) hex_digit { hex_digit } .

float_lit = decimals "." [ decimals ] [ exponent ] |
            decimals exponent |
            "." decimals [ exponent ] .
decimals  = decimal_digit { decimal_digit } .
exponent  = ( "e" | "E" ) [ "+" | "-" ] decimals .

imaginary_lit = (decimals | float_lit) "i" .

rune_lit         = "'" ( unicode_value | byte_value ) "'" .
unicode_value    = unicode_char | little_u_value | big_u_value | escaped_char .
byte_value       = octal_byte_value | hex_byte_value .
octal_byte_value = `\` octal_digit octal_digit octal_digit .
hex_byte_value   = `\` "x" hex_digit hex_digit .
little_u_value   = `\` "u" hex_digit hex_digit hex_digit hex_digit .
big_u_value      = `\` "U" hex_digit hex_digit hex_digit hex_digit
                           hex_digit hex_digit hex_digit hex_digit .
escaped_char     = `\` ( "a" | "b" | "f" | "n" | "r" | "t" | "v" | `\` | "'" | `"` ) .

string_lit             = raw_string_lit | interpreted_string_lit .
raw_string_lit         = "`" { unicode_char | newline } "`" .
interpreted_string_lit = `"` { unicode_value | byte_value } `"` .

Type      = TypeName | TypeLit | "(" Type ")" .
TypeName  = identifier | QualifiedIdent .
TypeLit   = ArrayType | StructType | PointerType | FunctionType | InterfaceType |
	    SliceType | MapType | ChannelType .

ArrayType   = "[" ArrayLength "]" ElementType .
ArrayLength = Expression .
ElementType = Type .

SliceType = "[" "]" ElementType .

StructType    = "struct" "{" { FieldDecl ";" } "}" .
FieldDecl     = (IdentifierList Type | EmbeddedField) [ Tag ] .
EmbeddedField = [ "*" ] TypeName .
Tag           = string_lit .

PointerType = "*" BaseType .
BaseType    = Type .

FunctionType   = "func" Signature .
Signature      = Parameters [ Result ] .
Result         = Parameters | Type .
Parameters     = "(" [ ParameterList [ "," ] ] ")" .
ParameterList  = ParameterDecl { "," ParameterDecl } .
ParameterDecl  = [ IdentifierList ] [ "..." ] Type .

InterfaceType      = "interface" "{" { MethodSpec ";" } "}" .
MethodSpec         = MethodName Signature | InterfaceTypeName .
MethodName         = identifier .
InterfaceTypeName  = TypeName .

MapType     = "map" "[" KeyType "]" ElementType .
KeyType     = Type .

Block = "{" StatementList "}" .
StatementList = { Statement ";" } .

Declaration   = ConstDecl | TypeDecl | VarDecl .
TopLevelDecl  = Declaration | FunctionDecl | MethodDecl .

ConstDecl      = "const" ( ConstSpec | "(" { ConstSpec ";" } ")" ) .
ConstSpec      = IdentifierList [ [ Type ] "=" ExpressionList ] .

IdentifierList = identifier { "," identifier } .
ExpressionList = Expression { "," Expression } .

TypeDecl = "type" ( TypeSpec | "(" { TypeSpec ";" } ")" ) .
TypeSpec = AliasDecl | TypeDef .

AliasDecl = identifier "=" Type .

TypeDef = identifier Type .

VarDecl     = "var" ( VarSpec | "(" { VarSpec ";" } ")" ) .
VarSpec     = IdentifierList ( Type [ "=" ExpressionList ] | "=" ExpressionList ) .

ShortVarDecl = IdentifierList ":=" ExpressionList .

FunctionDecl = "func" FunctionName Signature [ FunctionBody ] .
FunctionName = identifier .
FunctionBody = Block .

MethodDecl = "func" Receiver MethodName Signature [ FunctionBody ] .
Receiver   = Parameters .

Operand     = Literal | OperandName | "(" Expression ")" .
Literal     = BasicLit | CompositeLit | FunctionLit .
BasicLit    = int_lit | float_lit | imaginary_lit | rune_lit | string_lit .
OperandName = identifier | QualifiedIdent.

QualifiedIdent = PackageName "." identifier .

CompositeLit  = LiteralType LiteralValue .
LiteralType   = StructType | ArrayType | "[" "..." "]" ElementType |
                SliceType | MapType | TypeName .
LiteralValue  = "{" [ ElementList [ "," ] ] "}" .
ElementList   = KeyedElement { "," KeyedElement } .
KeyedElement  = [ Key ":" ] Element .
Key           = FieldName | Expression | LiteralValue .
FieldName     = identifier .
Element       = Expression | LiteralValue .

FunctionLit = "func" Signature FunctionBody .

PrimaryExpr =
	Operand |
	Conversion |
	MethodExpr |
	PrimaryExpr Selector |
	PrimaryExpr Index |
	PrimaryExpr Slice |
	PrimaryExpr TypeAssertion |
	PrimaryExpr Arguments .

Selector       = "." identifier .
Index          = "[" Expression "]" .
Slice          = "[" [ Expression ] ":" [ Expression ] "]" |
                 "[" [ Expression ] ":" Expression ":" Expression "]" .
TypeAssertion  = "." "(" Type ")" .
Arguments      = "(" [ ( ExpressionList | Type [ "," ExpressionList ] ) [ "..." ] [ "," ] ] ")" .

MethodExpr    = ReceiverType "." MethodName .
ReceiverType  = Type .

Expression = UnaryExpr | Expression binary_op Expression .
UnaryExpr  = PrimaryExpr | unary_op UnaryExpr .

binary_op  = "||" | "&&" | rel_op | add_op | mul_op .
rel_op     = "==" | "!=" | "<" | "<=" | ">" | ">=" .
add_op     = "+" | "-" | "|" | "^" .
mul_op     = "*" | "/" | "%" | "<<" | ">>" | "&" | "&^" .

unary_op   = "+" | "-" | "!" | "^" | "*" | "&" | "<-" .

Conversion = Type "(" Expression [ "," ] ")" .

Statement =
	Declaration | LabeledStmt | SimpleStmt |
	GoStmt | ReturnStmt | BreakStmt | ContinueStmt | GotoStmt |
	FallthroughStmt | Block | IfStmt | SwitchStmt | SelectStmt | ForStmt |
	DeferStmt .

SimpleStmt = EmptyStmt | ExpressionStmt | SendStmt | IncDecStmt | Assignment | ShortVarDecl .

EmptyStmt = .

LabeledStmt = Label ":" Statement .
Label       = identifier .

ExpressionStmt = Expression .

SendStmt = Channel "<-" Expression .
Channel  = Expression .

IncDecStmt = Expression ( "++" | "--" ) .

Assignment = ExpressionList assign_op ExpressionList .

assign_op = [ add_op | mul_op ] "=" .

IfStmt = "if" [ SimpleStmt ";" ] Expression Block [ "else" ( IfStmt | Block ) ] .

SwitchStmt = ExprSwitchStmt | TypeSwitchStmt .

ExprSwitchStmt = "switch" [ SimpleStmt ";" ] [ Expression ] "{" { ExprCaseClause } "}" .
ExprCaseClause = ExprSwitchCase ":" StatementList .
ExprSwitchCase = "case" ExpressionList | "default" .

TypeSwitchStmt  = "switch" [ SimpleStmt ";" ] TypeSwitchGuard "{" { TypeCaseClause } "}" .
TypeSwitchGuard = [ identifier ":=" ] PrimaryExpr "." "(" "type" ")" .
TypeCaseClause  = TypeSwitchCase ":" StatementList .
TypeSwitchCase  = "case" TypeList | "default" .
TypeList        = Type { "," Type } .

ForStmt = "for" [ Condition | ForClause | RangeClause ] Block .
Condition = Expression .

ForClause = [ InitStmt ] ";" [ Condition ] ";" [ PostStmt ] .
InitStmt = SimpleStmt .
PostStmt = SimpleStmt .

RangeClause = [ ExpressionList "=" | IdentifierList ":=" ] "range" Expression .

GoStmt = "go" Expression .

SelectStmt = "select" "{" { CommClause } "}" .
CommClause = CommCase ":" StatementList .
CommCase   = "case" ( SendStmt | RecvStmt ) | "default" .
RecvStmt   = [ ExpressionList "=" | IdentifierList ":=" ] RecvExpr .
RecvExpr   = Expression .

ReturnStmt = "return" [ ExpressionList ] .

BreakStmt = "break" [ Label ] .

ContinueStmt = "continue" [ Label ] .

GotoStmt = "goto" Label .

FallthroughStmt = "fallthrough" .

DeferStmt = "defer" Expression .

SourceFile       = PackageClause ";" { ImportDecl ";" } { TopLevelDecl ";" } .

PackageClause  = "package" PackageName .
PackageName    = identifier .

ImportDecl       = "import" ( ImportSpec | "(" { ImportSpec ";" } ")" ) .
ImportSpec       = [ "." | PackageName ] ImportPath .
ImportPath       = string_lit .
```
