`[](# Variables)
# 変数

```@raw html
<!--
A variable, in Julia, is a name associated (or bound) to a value. It's useful when you want to
store a value (that you obtained after some math, for example) for later use. For example:
-->
```

Juliaでは、変数とは値に関連付け（または束縛)した名前のことです。
これは（例えば計算結果などの）値を保存してあとで使う場合に便利です。
例を挙げると


```julia-repl
# Assign the value 10 to the variable x
julia> x = 10
10

# Doing math with x's value
julia> x + 1
11

# Reassign x's value
julia> x = 1 + 1
2

# You can assign values of other types, like strings of text
julia> x = "Hello World!"
"Hello World!"
```





```@raw html
<!--
Julia provides an extremely flexible system for naming variables. Variable names are case-sensitive,
and have no semantic meaning (that is, the language will not treat variables differently based
on their names).
-->
```
Juliaの変数の命名システムは非常に柔軟です。
大文字と小文字が違うと別の変数とみなされますが、意味論的には変わりません。（つまり名前の文字種によって変数の解釈が変わることはありません。)


```jldoctest
julia> x = 1.0
1.0

julia> y = -3
-3

julia> Z = "My string"
"My string"

julia> customary_phrase = "Hello world!"
"Hello world!"

julia> UniversalDeclarationOfHumanRightsStart = "人人生而自由，在尊严和权利上一律平等。"
"人人生而自由，在尊严和权利上一律平等。"
```

```@raw html
<!--
Unicode names (in UTF-8 encoding) are allowed:

-->
```

(UTF-8エンコードを使った)ユニコードの名前も利用できます。

```jldoctest
julia> δ = 0.00001
1.0e-5

julia> 안녕하세요 = "Hello"
"Hello"
```

```@raw html
<!--
In the Julia REPL and several other Julia editing environments, you can type many Unicode math
symbols by typing the backslashed LaTeX symbol name followed by tab. For example, the variable
name `δ` can be entered by typing `\delta`-*tab*, or even `α̂₂` by `\alpha`-*tab*-`\hat`-
*tab*-`\_2`-*tab*. (If you find a symbol somewhere, e.g. in someone else's code,
that you don't know how to type, the REPL help will tell you: just type `?` and
then paste the symbol.)

Julia will even let you redefine built-in constants and functions if needed (although
this is not recommended to avoid potential confusions):
-->
```

REPLやいくつかの編集環境では、バックスラッシュの後にLaTeXのシンボル名とタブキーを入力すると、
多数のユニコードの数学記号を入力できます。
例えば、 `δ` という変数名は、 `\delta`-*tab*　と打てばいいですし、`α̂₂` でさえ `\alpha`-*tab*-`\hat`-
*tab*-`\_2`-*tab* とすれば入力できます。(例えば、他人のコードで入力の仕方がわからないような記号に出くわしたときは、
REPLが役立ちます。 `?`を入力した後に、記号を貼り付ければいいのです)

Juliaでは、組み込みの定数や関数でさえも、必要なら再定義することができます。
（しかし、混乱の元とならないよう、推奨はされません）


```jldoctest
julia> pi = 3
3

julia> pi
3

julia> sqrt = 4
4
```

```@raw html
<!--
However, if you try to redefine a built-in constant or function already in use, Julia will give
you an error:
-->
```
しかし、組み込みの定数や関数をすでに利用している場合は、再定義しようとするとエラーが起こります。

```jldoctest
julia> pi
π = 3.1415926535897...

julia> pi = 3
ERROR: cannot assign variable MathConstants.pi from module Main

julia> sqrt(100)
10.0

julia> sqrt = 4
ERROR: cannot assign variable Base.sqrt from module Main
```

`[](## Allowed Variable Names)
## 利用可能な変数名

```@raw html
<!--
Variable names must begin with a letter (A-Z or a-z), underscore, or a subset of Unicode code
points greater than 00A0; in particular, [Unicode character categories](http://www.fileformat.info/info/unicode/category/index.htm)
Lu/Ll/Lt/Lm/Lo/Nl (letters), Sc/So (currency and other symbols), and a few other letter-like characters
(e.g. a subset of the Sm math symbols) are allowed. Subsequent characters may also include ! and
digits (0-9 and other characters in categories Nd/No), as well as other Unicode code points: diacritics
and other modifying marks (categories Mn/Mc/Me/Sk), some punctuation connectors (category Pc),
primes, and a few other characters.
-->
```

変数名は、アルファベット(A-Z または a-z)、アンダースコア、ユニコードの一部で、符号位置が00A0よりも大きいもの、
のいずれかで始めなければなりません。
特に[ユニコードの文字カテゴリー](http://www.fileformat.info/info/unicode/category/index.htm)
では、Lu/Ll/Lt/Lm/Lo/Nl (文字), Sc/So (通貨その他の記号),その他、アルファベットのような文字のいくつか
(例:数学記号Smの一部) が利用可能です。
二文字目以降は、`!`や数字(0-9やカテゴリーNd/Noの文字)が混在可能で、
他の符号位置のユニコードでは、発音区別記号などの修飾記号（カテゴリーMn/Mc/Me/Sk）、接続用の約物のいくつか(カテゴリーPc)、
プライムやその他いくつかの文字が利用可能です。


```@raw html
<!--
Operators like `+` are also valid identifiers, but are parsed specially. In some contexts, operators
can be used just like variables; for example `(+)` refers to the addition function, and `(+) = f`
will reassign it. Most of the Unicode infix operators (in category Sm), such as `⊕`, are parsed
as infix operators and are available for user-defined methods (e.g. you can use `const ⊗ = kron`
to define `⊗` as an infix Kronecker product).  Operators can also be suffixed with modifying marks,
primes, and sub/superscripts, e.g. `+̂ₐ″` is parsed as an infix operator with the same precedence as `+`.

The only explicitly disallowed names for variables are the names of built-in statements:

-->
```

`+`のような演算子は有効な識別子ですが、構文解析のされ方が特殊です。
 演算子が単なる変数のように扱われることもあります。例えば、`(+)`は加法の関数を表しますが、
`(+) = f`のように再代入することができます。
`⊕`のような(カテゴリーSmに属する)ユニコードの中置演算子のほとんどは、中置演算子として解析されて、ユーザー定義のメソッドを
利用できます。（例えば、`const ⊗ = kron`とすると`⊗`に中置記法のクロネッカ積を定義できます）。
また演算子の後に修飾記号、プライム、下付・上付文字をつけることもできます。
例えば、`+̂ₐ″`は前述の`+`と同じく、中置演算子として解析されます。

明示的に禁止されている変数名は唯一、組み込みの予約語のみです。


```julia-repl
julia> else = false
ERROR: syntax: unexpected "else"

julia> try = "No"
ERROR: syntax: unexpected "="
```

```@raw html
<!--
Some Unicode characters are considered to be equivalent in identifiers.
Different ways of entering Unicode combining characters (e.g., accents)
are treated as equivalent (specifically, Julia identifiers are NFC-normalized).
The Unicode characters `ɛ` (U+025B: Latin small letter open e)
and `µ` (U+00B5: micro sign) are treated as equivalent to the corresponding
Greek letters, because the former are easily accessible via some input methods.

-->
```

ユニコード文字のなかには識別子として同等に考えられるものがあります。
ユニコードの結合文字（アクセントなど）に対する異なる入力方法は同一視されます。
（具体的にはJuliaの識別子はNFC正規化されます）
ユニコード文字の `ɛ` (U+025B: Latin small letter open e)
and `µ` (U+00B5: micro sign) は対応するギリシャ文字と同一視されます。
というのも、入力法の中には前者のほうが簡単なものもあるからです。


`[](## Stylistic Conventions)
## 文体上の慣習

```@raw html
<!--
While Julia imposes few restrictions on valid names, it has become useful to adopt the following
conventions:

  * Names of variables are in lower case.
  * Word separation can be indicated by underscores (`'_'`), but use of underscores is discouraged
    unless the name would be hard to read otherwise.
  * Names of `Type`s and `Module`s begin with a capital letter and word separation is shown with upper
    camel case instead of underscores.
  * Names of `function`s and `macro`s are in lower case, without underscores.
  * Functions that write to their arguments have names that end in `!`. These are sometimes called
    "mutating" or "in-place" functions because they are intended to produce changes in their arguments
    after the function is called, not just return a value.

For more information about stylistic conventions, see the [Style Guide](@ref).

-->
```

Juliaの妥当な名前にはほとんど制限がありませんが、以下のような慣習に従うと役立つでしょう。


  * 変数名を小文字にする
  * 語句の区切るときに、アンダースコア(`'_'`)を利用できる。
  しかし他の方法では読みづらい時以外は、使わないほうがいい。
  * `型`や`モジュール`の名前は大文字ではじめ、語句の区切りにはアンダースコアを使わずに、アッパーキャメルケースを使う。
  * `関数`や`マクロ`の名前は小文字にして、アンダースコアを使わない
  * 引数に対して書き込みを行う関数は名前の最後に`!`をつける。
  こういった関数は時折、「変異(mutating)」「上書き(in-place)」関数とよばれます。
  これは，関数が呼び出された後、単に値を返すだけでなく、引数に対して変化を起こそうとするからです。

文体上の慣習に関するさらなる情報は [Style Guide](@ref)を参照してください。