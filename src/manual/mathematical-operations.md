`[](# Mathematical Operations and Elementary Functions)
# 算術演算と初等関数

```@raw html
<!--
Julia provides a complete collection of basic arithmetic and bitwise operators across all of its
numeric primitive types, as well as providing portable, efficient implementations of a comprehensive
collection of standard mathematical functions.
-->
```

Juliaには、すべての数値プリミティブ型に対して、基本的な算術演算子とビット演算子が一通りそろっています。
また、移植性が高く効率的な実装の、標準的な数学関数も一通りそろっています。


`[](## Arithmetic Operators)
## 算術演算子

```@raw html
<!--
The following [arithmetic operators](https://en.wikipedia.org/wiki/Arithmetic#Arithmetic_operations)
are supported on all primitive numeric types:

| Expression | Name           | Description                            |
|:---------- |:-------------- |:-------------------------------------- |
| `+x`       | unary plus     | the identity operation                 |
| `-x`       | unary minus    | maps values to their additive inverses |
| `x + y`    | binary plus    | performs addition                      |
| `x - y`    | binary minus   | performs subtraction                   |
| `x * y`    | times          | performs multiplication                |
| `x / y`    | divide         | performs division                      |
| `x ÷ y`    | integer divide | x / y, truncated to an integer         |
| `x \ y`    | inverse divide | equivalent to `y / x`                  |
| `x ^ y`    | power          | raises `x` to the `y`th power          |
| `x % y`    | remainder      | equivalent to `rem(x,y)`               |

-->
```

下記の[算術演算子](https://en.wikipedia.org/wiki/Arithmetic#Arithmetic_operations)
はすべてのプリミティブ数値型で利用可能です。

| 式         | 名前        | 説明                            |
|:---------- |:---------- |:-------------------------------------- |
| `+x`       | 単項加算    | 恒等演算                 |
| `-x`       | 単項減算    | 加算の逆元への関数 |
| `x + y`    | 二項加算    | 加算を実行                      |
| `x - y`    | 二項減算    | 減算を実行                   |
| `x * y`    | 乗算        | 乗算を実行                |
| `x / y`    | 除算        | 除算を実行                      |
| `x ÷ y`    | 整数除算    | x / y, 整数に切り捨て         |
| `x \ y`    | 逆除算      | `y / x`と同等                  |
| `x ^ y`    | 巾          | `x` の `y`乗          |
| `x % y`    | 剰余算      | `rem(x,y)`と同等               |


```@raw html
<!--
as well as the negation on [`Bool`](@ref) types:

| Expression | Name     | Description                              |
|:---------- |:-------- |:---------------------------------------- |
| `!x`       | negation | changes `true` to `false` and vice versa |

-->
```

[`Bool`](@ref) 型の否定も同様です

| 式         | 名前      | 説明                            |
|:---------- |:-------- |:---------------------------------------- |
| `!x`       | 否定     | `true`と`false`を逆にする|


```@raw html
<!--
Julia's promotion system makes arithmetic operations on mixtures of argument types "just work"
naturally and automatically. See [Conversion and Promotion](@ref conversion-and-promotion) for details of the promotion
system.

Here are some simple examples using arithmetic operators:

-->
```
Juliaは昇格の仕組みのおかげで、引数に型の混合した算術演算をおこなっても、自然かつ自動的に"うまく動作"します。
昇格の仕組みの詳細は[変換と昇格](@ref conversion-and-promotion)を参照してください。



```jldoctest
julia> 1 + 2 + 3
6

julia> 1 - 2
-1

julia> 3*2/12
0.5
```

```@raw html
<!--
(By convention, we tend to space operators more tightly if they get applied before other nearby
operators. For instance, we would generally write `-x + 2` to reflect that first `x` gets negated,
and then `2` is added to that result.)
-->
```
(慣習的に、周りの演算子より先に演算を行うときは、空白を詰めがちです。
例えば、よく`-x + 2`と書きますが、これは先に`X`に-1を掛けてから`2`を足すことを反映しています。)


`[](## Bitwise Operators)
## ビット演算子

```@raw html
<!--
The following [bitwise operators](https://en.wikipedia.org/wiki/Bitwise_operation#Bitwise_operators)
are supported on all primitive integer types:
-->
```
下記の[ビット演算子](https://en.wikipedia.org/wiki/Bitwise_operation#Bitwise_operators)
はすべてのプリミティブ整数型で利用可能です。

```@raw html
<!--
| Expression | Name                                                                     |
|:---------- |:------------------------------------------------------------------------ |
| `~x`       | bitwise not                                                              |
| `x & y`    | bitwise and                                                              |
| `x \| y`   | bitwise or                                                               |
| `x ⊻ y`    | bitwise xor (exclusive or)                                               |
| `x >>> y`  | [logical shift](https://en.wikipedia.org/wiki/Logical_shift) right       |
| `x >> y`   | [arithmetic shift](https://en.wikipedia.org/wiki/Arithmetic_shift) right |
| `x << y`   | logical/arithmetic shift left                                            |

-->
```

| 式         | 名前                                                                     |
|:---------- |:------------------------------------------------------------------------ |
| `~x`       | 否定(not)                                                              |
| `x & y`    | 論理積(and)                                                              |
| `x \| y`   | 論理和(or)                                                               |
| `x ⊻ y`    | 排他的論理和(xor)                                               |
| `x >>> y`  | [論理シフト](https://en.wikipedia.org/wiki/Logical_shift) 右       |
| `x >> y`   | [算術シフト](https://en.wikipedia.org/wiki/Arithmetic_shift) 右 |
| `x << y`   | 論理/算術　シフト　左                                            |


```@raw html
<!--
Here are some examples with bitwise operators:

-->
```

ビット演算の例をいくつかあげます。

```jldoctest
julia> ~123
-124

julia> 123 & 234
106

julia> 123 | 234
251

julia> 123 ⊻ 234
145

julia> xor(123, 234)
145

julia> ~UInt32(123)
0xffffff84

julia> ~UInt8(123)
0x84
```

`[](## Updating operators)
## 代入演算子

```@raw html
<!--
Every binary arithmetic and bitwise operator also has an updating version that assigns the result
of the operation back into its left operand. The updating version of the binary operator is formed
by placing a `=` immediately after the operator. For example, writing `x += 3` is equivalent to
writing `x = x + 3`:
-->
```

算術・ビット演算子で二項演算子のものはすべて演算結果を左の演算子に代入するバージョンの演算子が存在します。
代入バージョンの演算子は元の演算子のすぐ後ろに`=`をつけます。
例えば、`x += 3`は`x = x + 3`と同等です。


```jldoctest
julia> x = 1
1

julia> x += 3
4

julia> x
4
```

```@raw html
<!--
The updating versions of all the binary arithmetic and bitwise operators are:
-->
```

代入バージョンの二項演算子である算術・ビット演算子をすべて挙げると

```
+=  -=  *=  /=  \=  ÷=  %=  ^=  &=  |=  ⊻=  >>>=  >>=  <<=
```

```@raw html
<!--
!!! note
    An updating operator rebinds the variable on the left-hand side. As a result, the type of the
    variable may change.
-->
```

!!! 注意
    代入演算子は左辺の変数に対して再束縛を行います。この結果、変数の型が変わることも起こりえます。



```jldoctest
julia> x = 0x01; typeof(x)
UInt8

julia> x *= 2 # Same as x = x * 2
2

julia> typeof(x)
Int64
```

`[](## [Vectorized "dot" operators](@id man-dot-operators))
## [ベクトル化した "dot" 演算子](@id man-dot-operators)


```@raw html
<!--
For *every* binary operation like `^`, there is a corresponding
"dot" operation `.^` that is *automatically* defined
to perform `^` element-by-element on arrays. For example,
`[1,2,3] ^ 3` is not defined, since there is no standard
mathematical meaning to "cubing" a (non-square) array, but
`[1,2,3] .^ 3` is defined as computing the elementwise
(or "vectorized") result `[1^3, 2^3, 3^3]`.  Similarly for unary
operators like `!` or `√`, there is a corresponding `.√` that
applies the operator elementwise.
-->
```

 **すべての** `^`のような二項演算子に対して 、`.^`のように対応する"dot"演算子が存在します。
 これは **自動的に** 定義されて、配列の要素ごとに演算を行います。
例えば、`[1,2,3] ^ 3`は定義されていません。というのも、（二次元ではない）配列に数学的に標準的な"三乗"の
意味がないからです。
しかし、`[1,2,3] .^ 3`は要素ごとの(またはベクトル化した)計算結果`[1^3, 2^3, 3^3]`が定義できます。
同様に`!` や`√`などの単項演算子にも、`.√`のような、要素ごとに演算するものが存在します。



```jldoctest
julia> [1,2,3] .^ 3
3-element Array{Int64,1}:
  1
  8
 27
```

```@raw html
<!--
More specifically, `a .^ b` is parsed as the ["dot" call](@ref man-vectorized)
`(^).(a,b)`, which performs a [broadcast](@ref Broadcasting) operation:
it can combine arrays and scalars, arrays of the same size (performing
the operation elementwise), and even arrays of different shapes (e.g.
combining row and column vectors to produce a matrix). Moreover, like
all vectorized "dot calls," these "dot operators" are
*fusing*. For example, if you compute `2 .* A.^2 .+ sin.(A)` (or
equivalently `@. 2A^2 + sin(A)`, using the [`@.`](@ref @__dot__) macro) for
an array `A`, it performs a *single* loop over `A`, computing `2a^2 + sin(a)`
for each element of `A`. In particular, nested dot calls like `f.(g.(x))`
are fused, and "adjacent" binary operators like `x .+ 3 .* x.^2` are
equivalent to nested dot calls `(+).(x, (*).(3, (^).(x, 2)))`.
-->
```

More specifically, `a .^ b` is parsed as the ["dot" call](@ref man-vectorized)
`(^).(a,b)`, which performs a [broadcast](@ref Broadcasting) operation:
it can combine arrays and scalars, arrays of the same size (performing
the operation elementwise), and even arrays of different shapes (e.g.
combining row and column vectors to produce a matrix). Moreover, like
all vectorized "dot calls," these "dot operators" are
*fusing*. For example, if you compute `2 .* A.^2 .+ sin.(A)` (or
equivalently `@. 2A^2 + sin(A)`, using the [`@.`](@ref @__dot__) macro) for
an array `A`, it performs a *single* loop over `A`, computing `2a^2 + sin(a)`
for each element of `A`. In particular, nested dot calls like `f.(g.(x))`
are fused, and "adjacent" binary operators like `x .+ 3 .* x.^2` are
equivalent to nested dot calls `(+).(x, (*).(3, (^).(x, 2)))`.



```@raw html
<!--
Furthermore, "dotted" updating operators like `a .+= b` (or `@. a += b`) are parsed
as `a .= a .+ b`, where `.=` is a fused *in-place* assignment operation
(see the [dot syntax documentation](@ref man-vectorized)).

Note the dot syntax is also applicable to user-defined operators.
For example, if you define `⊗(A,B) = kron(A,B)` to give a convenient
infix syntax `A ⊗ B` for Kronecker products ([`kron`](@ref)), then
`[A,B] .⊗ [C,D]` will compute `[A⊗C, B⊗D]` with no additional coding.

Combining dot operators with numeric literals can be ambiguous.
For example, it is not clear whether `1.+x` means `1. + x` or `1 .+ x`.
Therefore this syntax is disallowed, and spaces must be used around
the operator in such cases.

-->
```
Furthermore, "dotted" updating operators like `a .+= b` (or `@. a += b`) are parsed
as `a .= a .+ b`, where `.=` is a fused *in-place* assignment operation
(see the [dot syntax documentation](@ref man-vectorized)).

Note the dot syntax is also applicable to user-defined operators.
For example, if you define `⊗(A,B) = kron(A,B)` to give a convenient
infix syntax `A ⊗ B` for Kronecker products ([`kron`](@ref)), then
`[A,B] .⊗ [C,D]` will compute `[A⊗C, B⊗D]` with no additional coding.

Combining dot operators with numeric literals can be ambiguous.
For example, it is not clear whether `1.+x` means `1. + x` or `1 .+ x`.
Therefore this syntax is disallowed, and spaces must be used around
the operator in such cases.


`[](## Numeric Comparisons)
## 数値の比較

```@raw html
<!--
Standard comparison operations are defined for all the primitive numeric types:
-->
```
すべてのプリミティブ数値型に対して標準的な比較演算が定義されています。


```@raw html
<!--
| Operator                     | Name                     |
|:---------------------------- |:------------------------ |
| [`==`](@ref)                 | equality                 |
| [`!=`](@ref), [`≠`](@ref !=) | inequality               |
| [`<`](@ref)                  | less than                |
| [`<=`](@ref), [`≤`](@ref <=) | less than or equal to    |
| [`>`](@ref)                  | greater than             |
| [`>=`](@ref), [`≥`](@ref >=) | greater than or equal to |
-->
```

| 演算             　　　        | 名前                     |
|:---------------------------- |:------------------------ |
| [`==`](@ref)                 |  等号               |
| [`!=`](@ref), [`≠`](@ref !=) | 不等号               |
| [`<`](@ref)                  | 未満                |
| [`<=`](@ref), [`≤`](@ref <=) | 以下    |
| [`>`](@ref)                  | より大きい             |
| [`>=`](@ref), [`≥`](@ref >=) | 以上 |


```@raw html
<!--
Here are some simple examples:

-->
```

簡単な例を挙げます。


```jldoctest
julia> 1 == 1
true

julia> 1 == 2
false

julia> 1 != 2
true

julia> 1 == 1.0
true

julia> 1 < 2
true

julia> 1.0 > 3
false

julia> 1 >= 1.0
true

julia> -1 <= 1
true

julia> -1 <= -1
true

julia> -1 <= -2
false

julia> 3 < -0.5
false
```

```@raw html
<!--
Integers are compared in the standard manner -- by comparison of bits. Floating-point numbers
are compared according to the [IEEE 754 standard](https://en.wikipedia.org/wiki/IEEE_754-2008):

  * Finite numbers are ordered in the usual manner.
  * Positive zero is equal but not greater than negative zero.
  * `Inf` is equal to itself and greater than everything else except `NaN`.
  * `-Inf` is equal to itself and less then everything else except `NaN`.
  * `NaN` is not equal to, not less than, and not greater than anything, including itself.

The last point is potentially surprising and thus worth noting:




-->
```
整数の比較は標準的にビットの比較によって行います。
浮動小数点の比較は[IEEE 754 規格](https://en.wikipedia.org/wiki/IEEE_754-2008)によって行います。


  * 有限の数の順序は通常通り。
  * 正の0は負の0と等しく、より大きくはない。
  * `Inf`は自身と等しく、`NaN`以外のその他すべてより大きい。
  * `-Inf`は自身と等しく、`NaN`以外のその他すべてより小さい。
  * `NaN`は自身も含めて、どれとも等しくなく、より小さくもなく、より大きくもない。



```jldoctest
julia> NaN == NaN
false

julia> NaN != NaN
true

julia> NaN < NaN
false

julia> NaN > NaN
false
```

```@raw html
<!--
and can cause especial headaches with [arrays](@ref man-multi-dim-arrays):
-->
```

[配列](@ref man-multi-dim-arrays)を扱いは、頭痛の種になりえます。

```jldoctest
julia> [1 NaN] == [1 NaN]
false
```

```@raw html
<!--
Julia provides additional functions to test numbers for special values, which can be useful in
situations like hash key comparisons:
-->
```

Juliaには特殊な値の数を検査する補助的な関数があって、ハッシュキーの比較などには役立ちます。


```@raw html
<!--
| Function                | Tests if                  |
|:----------------------- |:------------------------- |
| [`isequal(x, y)`](@ref) | `x` and `y` are identical |
| [`isfinite(x)`](@ref)   | `x` is a finite number    |
| [`isinf(x)`](@ref)      | `x` is infinite           |
| [`isnan(x)`](@ref)      | `x` is not a number       |
-->
```

| 関数                     | 検査内容                  |
|:----------------------- |:------------------------- |
| [`isequal(x, y)`](@ref) | `x` と`y`が等しいかどうか |
| [`isfinite(x)`](@ref)   | `x` が有限の数かどうか   |
| [`isinf(x)`](@ref)      | `x` が無限かどうか           |
| [`isnan(x)`](@ref)      | `x` が非数かどうか       |


```@raw html
<!--
[`isequal`](@ref) considers `NaN`s equal to each other:
-->
```
[`isequal`](@ref)では`NaN`は互いに等しいとみなします。


```jldoctest
julia> isequal(NaN, NaN)
true

julia> isequal([1 NaN], [1 NaN])
true

julia> isequal(NaN, NaN32)
true
```

```@raw html
<!--
`isequal` can also be used to distinguish signed zeros:
-->
```

`isequal`は符号つきの０を見分ける用途にも利用できます。

```jldoctest
julia> -0.0 == 0.0
true

julia> isequal(-0.0, 0.0)
false
```

```@raw html
<!--
Mixed-type comparisons between signed integers, unsigned integers, and floats can be tricky. A
great deal of care has been taken to ensure that Julia does them correctly.

For other types, `isequal` defaults to calling [`==`](@ref), so if you want to define
equality for your own types then you only need to add a [`==`](@ref) method.  If you define
your own equality function, you should probably define a corresponding [`hash`](@ref) method
to ensure that `isequal(x,y)` implies `hash(x) == hash(y)`.
-->
```

符号付整数、符号なし整数、浮動小数点数と型の混ざった比較には技巧が必要です。
Juliaでは比較が正しくできることを保証するために、多大な注意を払ってきました。
他の型に対しては、`isequal`はデフォルトで [`==`](@ref)を呼び出します。
なので、自分の作った型に対して等号を定義するには、 [`==`](@ref)メソッドを加えるだけいいです。
自分で等号を定義する場合には、`isequal(x,y)` が成り立つときは`hash(x) == hash(y)`が成り立つのを保証するために
 対応する[`hash`](@ref)メソッドも定義すべきでしょう。

`[](### Chaining comparisons)
### 比較の連鎖

```@raw html
<!--
Unlike most languages, with the [notable exception of Python](https://en.wikipedia.org/wiki/Python_syntax_and_semantics#Comparison_operators),
comparisons can be arbitrarily chained:
-->
```

 [有名な例外であるPython](https://en.wikipedia.org/wiki/Python_syntax_and_semantics#Comparison_operators)
は別にして、ほとんどの言語とは異なり、Juliaでは比較を好きなだけつなぐことができます。

```jldoctest
julia> 1 < 2 <= 2 < 3 == 3 > 2 >= 1 == 1 < 3 != 5
true
```

```@raw html
<!--
Chaining comparisons is often quite convenient in numerical code. Chained comparisons use the
`&&` operator for scalar comparisons, and the [`&`](@ref) operator for elementwise comparisons,
which allows them to work on arrays. For example, `0 .< A .< 1` gives a boolean array whose entries
are true where the corresponding elements of `A` are between 0 and 1.

Note the evaluation behavior of chained comparisons:
-->
```

比較の連鎖は数値を使うコードでは大変便利です。
比較の連鎖では、`&&`演算子をスカラ値の比較に使い、[`&`](@ref)演算子を要素ごとの比較に使って配列での比較を可能にします。
例えば、`0 .< A .< 1`は 、`A`の対応する要素が0と1の間にある場合にtrueをとなるブール値の配列を返します。



```jldoctest
julia> v(x) = (println(x); x)
v (generic function with 1 method)

julia> v(1) < v(2) <= v(3)
2
1
3
true

julia> v(1) > v(2) <= v(3)
2
1
false
```

```@raw html
<!--
The middle expression is only evaluated once, rather than twice as it would be if the expression
were written as `v(1) < v(2) && v(2) <= v(3)`. However, the order of evaluations in a chained
comparison is undefined. It is strongly recommended not to use expressions with side effects (such
as printing) in chained comparisons. If side effects are required, the short-circuit `&&` operator
should be used explicitly (see [Short-Circuit Evaluation](@ref)).
-->
```
真ん中の式は1度しか評価されませんが、`v(1) < v(2) && v(2) <= v(3)`と書いたなら評価は2度行われます。
しかし、連鎖した比較の評価の順番は決まっていません。
比較の連鎖では、（表示などの）副作用を伴う式は使わないように強くお勧めします。
副作用が必要な場合は短絡評価の`&&`演算子を明示的に使うべきでしょう。
([短絡評価](@ref)を参照のこと。)


`[](### Elementary Functions)
### 初等関数

```@raw html
<!--
Julia provides a comprehensive collection of mathematical functions and operators. These mathematical
operations are defined over as broad a class of numerical values as permit sensible definitions,
including integers, floating-point numbers, rationals, and complex numbers,
wherever such definitions make sense.

Moreover, these functions (like any Julia function) can be applied in "vectorized" fashion to
arrays and other collections with the [dot syntax](@ref man-vectorized) `f.(A)`,
e.g. `sin.(A)` will compute the sine of each element of an array `A`.
-->
```

Juliaには、数学的な関数や演算子が一通りそろっています。
この数学的な演算子は、広範な種類の数値に対して、意味をなす限り定義され、
対象は整数、浮動小数点数、有理数、複素数に及びます。

さらに、これらの関数は(Juliaの他の関数と同じように)、"ベクトル化"した形で、配列やその他のコレクションに対して
適用できます。
その時には、[dot 構文](@ref man-vectorized) `f.(A)`を使い、
例えば、`sin.(A)`だと配列`A`の各要素に対するsinの値を計算します。


`[](## Operator Precedence and Associativity)
## 演算子の優先順位と結合則

```@raw html
<!--
Julia applies the following order and associativity of operations, from highest precedence to lowest:
-->
```

Juliaでは、下記の優先順位と結合則で、高いほうから低いほうへと、演算子が適用されます。


```@raw html
<!--
| Category       | Operators                                                                                         | Associativity              |
|:-------------- |:------------------------------------------------------------------------------------------------- |:-------------------------- |
| Syntax         | `.` followed by `::`                                                                              | Left                       |
| Exponentiation | `^`                                                                                               | Right                      |
| Unary          | `+ - √`                                                                                           | Right[^1]                  |
| Bitshifts      | `<< >> >>>`                                                                                       | Left                       |
| Fractions      | `//`                                                                                              | Left                       |
| Multiplication | `* / % & \ ÷`                                                                                     | Left[^2]                   |
| Addition       | `+ - \| ⊻`                                                                                        | Left[^2]                   |
| Syntax         | `: ..`                                                                                            | Left                       |
| Syntax         | `\|>`                                                                                             | Left                       |
| Syntax         | `<\|`                                                                                             | Right                      |
| Comparisons    | `> < >= <= == === != !== <:`                                                                      | Non-associative            |
| Control flow   | `&&` followed by `\|\|` followed by `?`                                                           | Right                      |
| Pair           | `=>`                                                                                              | Right                      |
| Assignments    | `= += -= *= /= //= \= ^= ÷= %= \|= &= ⊻= <<= >>= >>>=`                                            | Right                      |

-->
```

| 種別    | 演算子                                                                                         | 結合則             |
|:--------|:------------------------------------------------------------------------------------------------- |:-------------------------- |
| 構文    | `.` 次に `::`                                                　　　　                              | 左                       |
| 巾      | `^`                                                                                               | 右                      |
| 単項    | `+ - √`                                                                                           | 右[^1]                  |
| シフト  | `<< >> >>>`                                                                                       | 左                       |
| 分数    | `//`                                                                                              | 左                       |
| 乗算    | `* / % & \ ÷`                                                                                     | 左[^2]                   |
| 加算    | `+ - \| ⊻`                                                                                        | 左[^2]                   |
| 構文    | `: ..`                                                                                            | 左                       |
| 構文    | `\|>`                                                                                             | 左                       |
| 構文    | `<\|`                                                                                             | 右                      |
| 比較    | `> < >= <= == === != !== <:`                                                                      | 非結合           |
| 制御    | `&&` 次に `\|\|` 次に `?`                                            　　　　　　　　               | 右                      |
| 対      | `=>`                                                                                              | 右                      |
| 代入    | `= += -= *= /= //= \= ^= ÷= %= \|= &= ⊻= <<= >>= >>>=`                                            | 右                      |


```@raw html
<!--
[^1]:
    The unary operators `+` and `-` require explicit parentheses around their argument to disambiguate them from the operator `++`, etc. Other compositions of unary operators are parsed with right-associativity, e. g., `√√-a` as `√(√(-a))`.
[^2]:
    The operators `+`, `++` and `*` are non-associative. `a + b + c` is parsed as `+(a, b, c)` not `+(+(a, b),
    c)`. However, the fallback methods for `+(a, b, c, d...)` and `*(a, b, c, d...)` both default to left-associative evaluation.
-->
```

[^1]:
    単項演算子の `+` と `-`には、`++`などの演算子との曖昧さをなくすために、明示的な括弧が必要です。
    他の単項演算子の結合は右結合として解析されます。例えば、 `√√-a`は`√(√(-a))`です。
[^2]:
    演算子の `+`, `++`,`*` は非結合的です。
    `a + b + c` は `+(a, b, c)` として解析され `+(+(a, b),c)`ではありません。
    しかし、フォールバックメソッドの `+(a, b, c, d...)` と `*(a, b, c, d...)` は両方ともデフォルトでは左結合で評価されます。


```@raw html
<!--
For a complete list of *every* Julia operator's precedence, see the top of this file:
[`src/julia-parser.scm`](https://github.com/JuliaLang/julia/blob/master/src/julia-parser.scm)

You can also find the numerical precedence for any given operator via the built-in function `Base.operator_precedence`, where higher numbers take precedence:

-->
```

**すべての** Juliaの演算子の優先順位の完全なリストはこのファイルの最初をみてください。
[`src/julia-parser.scm`](https://github.com/JuliaLang/julia/blob/master/src/julia-parser.scm)

また演算子の優先順位を表す数値は、組み込み関数の`Base.operator_precedence`を使ってみることができます。
大きい数字のほうが優先順位が高くなります。


```jldoctest
julia> Base.operator_precedence(:+), Base.operator_precedence(:*), Base.operator_precedence(:.)
(11, 13, 17)

julia> Base.operator_precedence(:sin), Base.operator_precedence(:+=), Base.operator_precedence(:(=))  # (Note the necessary parens on `:(=)`)
(0, 1, 1)
```

```@raw html
<!--
A symbol representing the operator associativity can also be found by calling the built-in function `Base.operator_associativity`:
-->
```
演算子を表すシンボルの結合則も、組み込み関数の`Base.operator_associativity`を使ってみることができます。


```jldoctest
julia> Base.operator_associativity(:-), Base.operator_associativity(:+), Base.operator_associativity(:^)
(:left, :none, :right)

julia> Base.operator_associativity(:⊗), Base.operator_associativity(:sin), Base.operator_associativity(:→)
(:left, :none, :right)
```

```@raw html
<!--
Note that symbols such as `:sin` return precedence `0`. This value represents invalid operators and not
operators of lowest precedence. Similarly, such operators are assigned associativity `:none`.
-->
```

`:sin`のようなシンボルには優先順位が `0`として返される点に注意してください。
この値は演算子として無効であることを表し、優先順位が最も低いわけではありません。
同様にこういった演算子には結合性は`:none`が割り当てられています。


`[](## Numerical Conversions)
## 数値変換

```@raw html
<!--
Julia supports three forms of numerical conversion, which differ in their handling of inexact
conversions.
-->
```

Juliaでは3種の数値変換が利用できますが、それぞれ厳密な変換ができないときの扱いが異なります。



```@raw html
<!--
  * The notation `T(x)` or `convert(T,x)` converts `x` to a value of type `T`.

      * If `T` is a floating-point type, the result is the nearest representable value, which could be
        positive or negative infinity.
      * If `T` is an integer type, an `InexactError` is raised if `x` is not representable by `T`.
  * `x % T` converts an integer `x` to a value of integer type `T` congruent to `x` modulo `2^n`,
    where `n` is the number of bits in `T`. In other words, the binary representation is truncated
    to fit.
  * The [Rounding functions](@ref) take a type `T` as an optional argument. For example, `round(Int,x)`
    is a shorthand for `Int(round(x))`.
-->
```

  * `T(x)`や`convert(T,x)`といった表記では`x`を型`T`の値に変換する。

      * `T`が浮動小数点数型の場合、最近接の表現可能な値を返し、正負の無限大になることもありえます。
      * `T`が整数型の場合、`x`が型`T`で表現不可能な場合、`InexactError`が生じる。
  * `x % T`は、`x`を型が`T`で、`2^n`を法として`x`と合同な数に変換します。
    ここで`n`は`T`のビット数です。
    換言すると、2進表現を`T`に収まるように切り捨てているのです。
  * [端数処理関数](@ref)は、必要に応じて、型`T`を引数にとることができます。
   例えば、`round(Int,x)`は`Int(round(x))`を手短にしたものです。


```@raw html
<!--
The following examples show the different forms.
-->
```
それぞれの例を下記に挙げます。


```jldoctest
julia> Int8(127)
127

julia> Int8(128)
ERROR: InexactError: trunc(Int8, 128)
Stacktrace:
[...]

julia> Int8(127.0)
127

julia> Int8(3.14)
ERROR: InexactError: Int8(Int8, 3.14)
Stacktrace:
[...]

julia> Int8(128.0)
ERROR: InexactError: Int8(Int8, 128.0)
Stacktrace:
[...]

julia> 127 % Int8
127

julia> 128 % Int8
-128

julia> round(Int8,127.4)
127

julia> round(Int8,127.6)
ERROR: InexactError: trunc(Int8, 128.0)
Stacktrace:
[...]
```

```@raw html
<!--
See [Conversion and Promotion](@ref conversion-and-promotion) for how to define your own conversions and promotions.
-->
```

自分で変換や昇格を定義するには、[変換と昇格](@ref conversion-and-promotion)を参照してください。

`[](### Rounding functions)
### 端数処理関数


```@raw html
<!--
| Function              | Description                      | Return type |
|:--------------------- |:-------------------------------- |:----------- |
| [`round(x)`](@ref)    | round `x` to the nearest integer | `typeof(x)` |
| [`round(T, x)`](@ref) | round `x` to the nearest integer | `T`         |
| [`floor(x)`](@ref)    | round `x` towards `-Inf`         | `typeof(x)` |
| [`floor(T, x)`](@ref) | round `x` towards `-Inf`         | `T`         |
| [`ceil(x)`](@ref)     | round `x` towards `+Inf`         | `typeof(x)` |
| [`ceil(T, x)`](@ref)  | round `x` towards `+Inf`         | `T`         |
| [`trunc(x)`](@ref)    | round `x` towards zero           | `typeof(x)` |
| [`trunc(T, x)`](@ref) | round `x` towards zero           | `T`         |
-->
```
| 関数                  | 説明                        | 戻り値の型  |
|:--------------------- |:-------------------------- |:----------- |
| [`round(x)`](@ref)    | `x`を最近接の整数に丸める 　　| `typeof(x)` |
| [`round(T, x)`](@ref) | `x`を最近接の整数に丸める 　　| `T`         |
| [`floor(x)`](@ref)    | `x`を`-Inf`方向に丸める      | `typeof(x)` |
| [`floor(T, x)`](@ref) | `x`を`-Inf`方向に丸める      | `T`         |
| [`ceil(x)`](@ref)     | `x`を`+Inf`方向に丸める      | `typeof(x)` |
| [`ceil(T, x)`](@ref)  | `x`を`+Inf`方向に丸める      | `T`         |
| [`trunc(x)`](@ref)    | `x`を0方向に丸める           | `typeof(x)` |
| [`trunc(T, x)`](@ref) | `x`を0方向に丸める           | `T`         |


`[](### Division functions)
### 除算関数

```@raw html
<!--
| Function                  | Description                                                                                               |
|:------------------------- |:--------------------------------------------------------------------------------------------------------- |
| [`div(x,y)`](@ref), `x÷y` | truncated division; quotient rounded towards zero                                                         |
| [`fld(x,y)`](@ref)        | floored division; quotient rounded towards `-Inf`                                                         |
| [`cld(x,y)`](@ref)        | ceiling division; quotient rounded towards `+Inf`                                                         |
| [`rem(x,y)`](@ref)        | remainder; satisfies `x == div(x,y)*y + rem(x,y)`; sign matches `x`                                       |
| [`mod(x,y)`](@ref)        | modulus; satisfies `x == fld(x,y)*y + mod(x,y)`; sign matches `y`                                         |
| [`mod1(x,y)`](@ref)       | `mod` with offset 1; returns `r∈(0,y]` for `y>0` or `r∈[y,0)` for `y<0`, where `mod(r, y) == mod(x, y)`   |
| [`mod2pi(x)`](@ref)       | modulus with respect to 2pi;  `0 <= mod2pi(x)    < 2pi`                                                   |
| [`divrem(x,y)`](@ref)     | returns `(div(x,y),rem(x,y))`                                                                             |
| [`fldmod(x,y)`](@ref)     | returns `(fld(x,y),mod(x,y))`                                                                             |
| [`gcd(x,y...)`](@ref)     | greatest positive common divisor of `x`, `y`,...                                                          |
| [`lcm(x,y...)`](@ref)     | least positive common multiple of `x`, `y`,...                                                            |
-->
```

| 関数                      | 説明                                                                                               |
|:------------------------- |:--------------------------------------------------------------------------------------------------------- |
| [`div(x,y)`](@ref), `x÷y` | 切り落とし除算 0方向に丸めた商                                                        |
| [`fld(x,y)`](@ref)        | 床除算　　　`-Inf`方向に丸めた商                                                          |
| [`cld(x,y)`](@ref)        | 天井除算　　`+Inf`方向に丸めた商                                                               |
| [`rem(x,y)`](@ref)        | 剰余(remainder)　 `x == div(x,y)*y + rem(x,y)`　 符号は `x` と一致                                       |
| [`mod(x,y)`](@ref)        | 剰余(modulus)　　 `x == fld(x,y)*y + mod(x,y)`　　符号は `y` と一致                                        |
| [`mod1(x,y)`](@ref)       | 1 オフセットした`mod` `mod(r, y) == mod(x, y)を満たす`r`の中で`y>0`の時`r∈(0,y]``y<0`の時 `r∈[y,0)`のものを返す  |
| [`mod2pi(x)`](@ref)       | 2piを法とする剰余  `0 <= mod2pi(x)    < 2pi`                                                   |
| [`divrem(x,y)`](@ref)     | `(div(x,y),rem(x,y))`を返す                                                                             |
| [`fldmod(x,y)`](@ref)     | `(fld(x,y),mod(x,y))`を返す                                                                             |
| [`gcd(x,y...)`](@ref)     |  `x`, `y`,... の正の最大公約数                                                          |
| [`lcm(x,y...)`](@ref)     | `x`, `y`,... の正の最小公倍数                                                            |


`[](### Sign and absolute value functions)
### 符号・絶対値関数

```@raw html
<!--
| Function                | Description                                                |
|:----------------------- |:---------------------------------------------------------- |
| [`abs(x)`](@ref)        | a positive value with the magnitude of `x`                 |
| [`abs2(x)`](@ref)       | the squared magnitude of `x`                               |
| [`sign(x)`](@ref)       | indicates the sign of `x`, returning -1, 0, or +1          |
| [`signbit(x)`](@ref)    | indicates whether the sign bit is on (true) or off (false) |
| [`copysign(x,y)`](@ref) | a value with the magnitude of `x` and the sign of `y`      |
| [`flipsign(x,y)`](@ref) | a value with the magnitude of `x` and the sign of `x*y`    |
-->
```

| 関数                     | 説明                                                |
|:----------------------- |:---------------------------------------------------------- |
| [`abs(x)`](@ref)        | `x`の絶対値である正の値                 |
| [`abs2(x)`](@ref)       | `x`の絶対値の2乗                               |
| [`sign(x)`](@ref)       | `x`の符号。 -1, 0, +1 のいずれかを返す         |
| [`signbit(x)`](@ref)    | 符号ビットが オン (真) か オフ (偽)かを示す |
| [`copysign(x,y)`](@ref) | 絶対値が`x`で符号が`y`の値      |
| [`flipsign(x,y)`](@ref) | 絶対値が`x`で符号が`x*y`の値   |

`[](### Powers, logs and roots)
### 巾、対数、平方根

```@raw html
<!--
| Function                 | Description                                                                |
|:------------------------ |:-------------------------------------------------------------------------- |
| [`sqrt(x)`](@ref), `√x`  | square root of `x`                                                         |
| [`cbrt(x)`](@ref), `∛x`  | cube root of `x`                                                           |
| [`hypot(x,y)`](@ref)     | hypotenuse of right-angled triangle with other sides of length `x` and `y` |
| [`exp(x)`](@ref)         | natural exponential function at `x`                                        |
| [`expm1(x)`](@ref)       | accurate `exp(x)-1` for `x` near zero                                      |
| [`ldexp(x,n)`](@ref)     | `x*2^n` computed efficiently for integer values of `n`                     |
| [`log(x)`](@ref)         | natural logarithm of `x`                                                   |
| [`log(b,x)`](@ref)       | base `b` logarithm of `x`                                                  |
| [`log2(x)`](@ref)        | base 2 logarithm of `x`                                                    |
| [`log10(x)`](@ref)       | base 10 logarithm of `x`                                                   |
| [`log1p(x)`](@ref)       | accurate `log(1+x)` for `x` near zero                                      |
| [`exponent(x)`](@ref)    | binary exponent of `x`                                                     |
| [`significand(x)`](@ref) | binary significand (a.k.a. mantissa) of a floating-point number `x`        |

-->
```

| 関数                     | 説明                                                                |
|:------------------------ |:-------------------------------------------------------------------------- |
| [`sqrt(x)`](@ref), `√x`  | `x`の平方根                                                         |
| [`cbrt(x)`](@ref), `∛x`  | `x`の三乗根                                                           |
| [`hypot(x,y)`](@ref)     | 直角をはさむ2辺が `x`,`y`である三角形の斜辺 |
| [`exp(x)`](@ref)         | 自然指数関数の `x`での値                                        |
| [`expm1(x)`](@ref)       | 0付近の`x`に対する `exp(x)-1`の正確な値                                      |
| [`ldexp(x,n)`](@ref)     | 整数`n`にたいして効率的に計算できる`x*2^n`の値                     |
| [`log(x)`](@ref)         | 自然対数の`x`での値                                                   |
| [`log(b,x)`](@ref)       | `b`を底とする対数の`x`での値                                                  |
| [`log2(x)`](@ref)        | 2を底とする対数の`x`での値                                               |
| [`log10(x)`](@ref)       | 10を底とする対数の`x`での値                                                   |
| [`log1p(x)`](@ref)       | 0付近の`x`に対する `log(1+x)`の正確な値                                       |
| [`exponent(x)`](@ref)    | 浮動小数点数の`x`の２を基数とする指数部分                                                     |
| [`significand(x)`](@ref) | 浮動小数点数の`x`の２を基数とする仮数部分        |

```@raw html
<!--
For an overview of why functions like [`hypot`](@ref), [`expm1`](@ref), and [`log1p`](@ref)
are necessary and useful, see John D. Cook's excellent pair of blog posts on the subject: [expm1, log1p, erfc](https://www.johndcook.com/blog/2010/06/07/math-library-functions-that-seem-unnecessary/),
and [hypot](https://www.johndcook.com/blog/2010/06/02/whats-so-hard-about-finding-a-hypotenuse/).

-->
```
[`hypot`](@ref), [`expm1`](@ref), [`log1p`](@ref)といった関数がなぜ必要で役に立つのを概観するには
John D. Cook の 優れた2編のブログ投稿を参照のこと。
 [expm1, log1p, erfc](https://www.johndcook.com/blog/2010/06/07/math-library-functions-that-seem-unnecessary/),
[hypot](https://www.johndcook.com/blog/2010/06/02/whats-so-hard-about-finding-a-hypotenuse/).


`[](### Trigonometric and hyperbolic functions)
### 三角関数と双曲線関数

```@raw html
<!--
All the standard trigonometric and hyperbolic functions are also defined:
-->
```

標準的な三角関数と双曲線関数はすべて定義されています。

```
sin    cos    tan    cot    sec    csc
sinh   cosh   tanh   coth   sech   csch
asin   acos   atan   acot   asec   acsc
asinh  acosh  atanh  acoth  asech  acsch
sinc   cosc
```

```@raw html
<!--
These are all single-argument functions, with [`atan`](@ref) also accepting two arguments
corresponding to a traditional [`atan2`](https://en.wikipedia.org/wiki/Atan2) function.

Additionally, [`sinpi(x)`](@ref) and [`cospi(x)`](@ref) are provided for more accurate computations
of [`sin(pi*x)`](@ref) and [`cos(pi*x)`](@ref) respectively.

In order to compute trigonometric functions with degrees instead of radians, suffix the function
with `d`. For example, [`sind(x)`](@ref) computes the sine of `x` where `x` is specified in degrees.
The complete list of trigonometric functions with degree variants is:

-->
```
これらの関数の引数はすべて１つですが、[`atan`](@ref)の場合は、引数を２つとると昔からある[`atan2`](https://en.wikipedia.org/wiki/Atan2)
として使うことができます。

さらに、 [`sinpi(x)`](@ref)や[`cospi(x)`](@ref) を、それぞれ[`sin(pi*x)`](@ref)や[`cos(pi*x)`](@ref)よりも
正確な計算をする関数として使うことができます。

ラジアンのかわりに度を使うには、三角関数の後ろに`d`をつけます。
例えば、[`sind(x)`](@ref)は`x`に度が指定されたものとして、計算します。
変数を度として扱う三角関数の完全なリストは、

```
sind   cosd   tand   cotd   secd   cscd
asind  acosd  atand  acotd  asecd  acscd
```

`[](### Special functions)
### 特殊関数


```@raw html
<!--
Many other special mathematical functions are provided by the package
[SpecialFunctions.jl](https://github.com/JuliaMath/SpecialFunctions.jl).
-->
```

その他多くの数学の特殊関数がパッケージ
[SpecialFunctions.jl](https://github.com/JuliaMath/SpecialFunctions.jl).
によって利用可能です。