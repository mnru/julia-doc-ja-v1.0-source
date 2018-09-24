`[](# Complex and Rational Numbers)
# 複素数と有理数

```@raw html
<!--
Julia ships with predefined types representing both complex and rational numbers, and supports
all standard [Mathematical Operations and Elementary Functions](@ref) on them. [Conversion and Promotion](@ref conversion-and-promotion) are defined
so that operations on any combination of predefined numeric types, whether primitive or composite,
behave as expected.
-->
```
Juliaには複素数と有理数が定義済みの型として備わっていて、すべての標準的な[算術演算子と初等関数](@ref)に対応しています。
プリミティブ型も複合型も、定義済みの型の組み合わせに対しては期待通りに動くように、[変換と昇格](@ref conversion-and-promotion)
が定義されています。


`[](## Complex Numbers)
## 複素数

```@raw html
<!--
The global constant [`im`](@ref) is bound to the complex number *i*, representing the principal
square root of -1. It was deemed harmful to co-opt the name `i` for a global constant, since it
is such a popular index variable name. Since Julia allows numeric literals to be [juxtaposed with identifiers as coefficients](@ref man-numeric-literal-coefficients),
this binding suffices to provide convenient syntax for complex numbers, similar to the traditional
mathematical notation:
-->
```
グローバル定数の [`im`](@ref)には複素数の *i* が束縛されていて、-1の平方根を表しています。
`i`はよくインデックスに使われる変数のために、グローバル変数にするのは害があると思われます。
Juliaでは、数値リテラルを[識別子の前に置くと係数になる](@ref man-numeric-literal-coefficients)ので、
この定数は、従来の数学表記に似た、便利な複素数の構文として、利用できます。


```jldoctest
julia> 1 + 2im
1 + 2im
```

```@raw html
<!--
You can perform all the standard arithmetic operations with complex numbers:
-->
```
複素数の標準的な算術演算はすべて実行可能です。

```jldoctest
julia> (1 + 2im)*(2 - 3im)
8 + 1im

julia> (1 + 2im)/(1 - 2im)
-0.6 + 0.8im

julia> (1 + 2im) + (1 - 2im)
2 + 0im

julia> (-3 + 2im) - (5 - 1im)
-8 + 3im

julia> (-1 + 2im)^2
-3 - 4im

julia> (-1 + 2im)^2.5
2.729624464784009 - 6.9606644595719im

julia> (-1 + 2im)^(1 + 1im)
-0.27910381075826657 + 0.08708053414102428im

julia> 3(2 - 5im)
6 - 15im

julia> 3(2 - 5im)^2
-63 - 60im

julia> 3(2 - 5im)^-1.0
0.20689655172413796 + 0.5172413793103449im
```

```@raw html
<!--
The promotion mechanism ensures that combinations of operands of different types just work:
-->
```
昇格のしくみによって、異なる型の組み合わせでもちゃんと動くことが保証されています。


```jldoctest
julia> 2(1 - 1im)
2 - 2im

julia> (2 + 3im) - 1
1 + 3im

julia> (1 + 2im) + 0.5
1.5 + 2.0im

julia> (2 + 3im) - 0.5im
2.0 + 2.5im

julia> 0.75(1 + 2im)
0.75 + 1.5im

julia> (2 + 3im) / 2
1.0 + 1.5im

julia> (1 - 3im) / (2 + 2im)
-0.5 - 1.0im

julia> 2im^2
-2 + 0im

julia> 1 + 3/4im
1.0 - 0.75im
```

```@raw html
<!--
Note that `3/4im == 3/(4*im) == -(3/4*im)`, since a literal coefficient binds more tightly than
division.

Standard functions to manipulate complex values are provided:
-->
```

`3/4im == 3/(4*im) == -(3/4*im)`となることに注意してください。リテラル係数の方が除算より結合がつよいのです。


```jldoctest
julia> z = 1 + 2im
1 + 2im

julia> real(1 + 2im) # real part of z
1

julia> imag(1 + 2im) # imaginary part of z
2

julia> conj(1 + 2im) # complex conjugate of z
1 - 2im

julia> abs(1 + 2im) # absolute value of z
2.23606797749979

julia> abs2(1 + 2im) # squared absolute value
5

julia> angle(1 + 2im) # phase angle in radians
1.1071487177940904
```

```@raw html
<!--
As usual, the absolute value ([`abs`](@ref)) of a complex number is its distance from zero.
[`abs2`](@ref) gives the square of the absolute value, and is of particular use for complex
numbers where it avoids taking a square root. [`angle`](@ref) returns the phase angle in radians
(also known as the *argument* or *arg* function). The full gamut of other [Elementary Functions](@ref)
is also defined for complex numbers:
-->
```

通常通り、複素数の絶対値 ([`abs`](@ref))は０からの距離です。
[`abs2`](@ref)は、複素数の絶対値の二乗で、特に複素数に対して、平方根の計算をさけるために使われます。
[`angle`](@ref)はラジアンによる位相角（**偏角**としても知られています）を返します。
その他すべての[初等関数](@ref)も複素数に対して定義されています。


```jldoctest
julia> sqrt(1im)
0.7071067811865476 + 0.7071067811865475im

julia> sqrt(1 + 2im)
1.272019649514069 + 0.7861513777574233im

julia> cos(1 + 2im)
2.0327230070196656 - 3.0518977991518im

julia> exp(1 + 2im)
-1.1312043837568135 + 2.4717266720048188im

julia> sinh(1 + 2im)
-0.4890562590412937 + 1.4031192506220405im
```

```@raw html
<!--
Note that mathematical functions typically return real values when applied to real numbers and
complex values when applied to complex numbers. For example, [`sqrt`](@ref) behaves differently
when applied to `-1` versus `-1 + 0im` even though `-1 == -1 + 0im`:
-->
```
通常、数学的な関数は、実数に対しては実数を、複素数に対しては複素数を返す点に注意してください。
例えば、 `-1 == -1 + 0im`が成り立ちますが、[`sqrt`](@ref)は`-1`と`-1 + 0im`で挙動が違います。 


```jldoctest
julia> sqrt(-1)
ERROR: DomainError with -1.0:
sqrt will only return a complex result if called with a complex argument. Try sqrt(Complex(x)).
Stacktrace:
[...]

julia> sqrt(-1 + 0im)
0.0 + 1.0im
```

```@raw html
<!--
The [literal numeric coefficient notation](@ref man-numeric-literal-coefficients) does not work when constructing a complex number
from variables. Instead, the multiplication must be explicitly written out:
-->
```

[数値リテラル係数表記](@ref man-numeric-literal-coefficients)は変数から複素数をつくる際には機能しません。
かわりに、掛け算を明示的に書く必要があります。


```jldoctest
julia> a = 1; b = 2; a + b*im
1 + 2im
```

```@raw html
<!--
However, this is *not* recommended; Use the [`complex`](@ref) function instead to construct
a complex value directly from its real and imaginary parts:
-->
```
しかし、これは推奨**されません**。
実部と虚部から直接、複素数をつくるには、[`complex`](@ref)を使ってください。


```jldoctest
julia> a = 1; b = 2; complex(a, b)
1 + 2im
```

```@raw html
<!--
This construction avoids the multiplication and addition operations.

[`Inf`](@ref) and [`NaN`](@ref) propagate through complex numbers in the real and imaginary parts
of a complex number as described in the [Special floating-point values](@ref) section:
-->
```

この作り方によって、乗算・加算が避けられます。
[特殊な浮動小数点数の値](@ref)のセクションで述べた、[`Inf`](@ref)や[`NaN`](@ref)は、複素数の実部や虚部に
使うことができます。


```jldoctest
julia> 1 + Inf*im
1.0 + Inf*im

julia> 1 + NaN*im
1.0 + NaN*im
```

`[](## Rational Numbers)
## 有理数

```@raw html

<!--
Julia has a rational number type to represent exact ratios of integers. Rationals are constructed
using the [`//`](@ref) operator:
-->
```

Juliaには、整数の比を正確に表現するために、有理数型があります。
有理数は、[`//`](@ref)を使って、構成します。



```jldoctest
julia> 2//3
2//3
```

```@raw html
<!--
If the numerator and denominator of a rational have common factors, they are reduced to lowest
terms such that the denominator is non-negative:
-->
```

有理数の分子と分母に公約数がある場合、分母が非負である最小の数に約分されます。



```jldoctest
julia> 6//9
2//3

julia> -4//8
-1//2

julia> 5//-15
-1//3

julia> -4//-12
1//3
```

```@raw html
<!--
This normalized form for a ratio of integers is unique, so equality of rational values can be
tested by checking for equality of the numerator and denominator. The standardized numerator and
denominator of a rational value can be extracted using the [`numerator`](@ref) and [`denominator`](@ref)
functions:
-->
```

この正規化された形式は、整数の比に対して一意的なので、有理数の等価性は分子と分母の等価性を検査すればわかります。
有理数の標準化された分子と分母は、関数の[`numerator`](@ref) と [`denominator`](@ref)を使って得ることができます。


```jldoctest
julia> numerator(2//3)
2

julia> denominator(2//3)
3
```

```@raw html
<!--
Direct comparison of the numerator and denominator is generally not necessary, since the standard
arithmetic and comparison operations are defined for rational values:
-->
```
直接、分子と分母を比較する必要は通常ありません。
標準的な算術・比較演算子有理数値に対して定義されているからです。


```jldoctest
julia> 2//3 == 6//9
true

julia> 2//3 == 9//27
false

julia> 3//7 < 1//2
true

julia> 3//4 > 2//3
true

julia> 2//4 + 1//6
2//3

julia> 5//12 - 1//4
1//6

julia> 5//8 * 3//12
5//32

julia> 6//5 / 10//7
21//25
```

```@raw html
<!--
Rationals can be easily converted to floating-point numbers:
-->
```

有理数は簡単に浮動小数点数に変換することができます。

```jldoctest
julia> float(3//4)
0.75
```

```@raw html
<!--
Conversion from rational to floating-point respects the following identity for any integral values
of `a` and `b`, with the exception of the case `a == 0` and `b == 0`:
-->
```
有理数から浮動小数点数への変換は、`a == 0`かつ`b == 0`の場合を除く任意の整数値`a`と`b`に対して、
下記の式に記した等価性が成り立つことを尊重しています。



```jldoctest
julia> a = 1; b = 2;

julia> isequal(float(a//b), a/b)
true
```

```@raw html
<!--
Constructing infinite rational values is acceptable:
-->
```
無限の有理数も構成可能です。

```jldoctest
julia> 5//0
1//0

julia> -3//0
-1//0

julia> typeof(ans)
Rational{Int64}
```

```@raw html
<!--
Trying to construct a [`NaN`](@ref) rational value, however, is not:
-->
```
しかし、[`NaN`](@ref)の有理数値は構成できません。

```jldoctest
julia> 0//0
ERROR: ArgumentError: invalid rational: zero(Int64)//zero(Int64)
Stacktrace:
[...]
```

```@raw html
<!--
As usual, the promotion system makes interactions with other numeric types effortless:
-->
```
たいていの場合、昇格のしくみのおかげで、苦労することなく他の数値型といっしょに使うことができます。

```jldoctest
julia> 3//5 + 1
8//5

julia> 3//5 - 0.5
0.09999999999999998

julia> 2//7 * (1 + 2im)
2//7 + 4//7*im

julia> 2//7 * (1.5 + 2im)
0.42857142857142855 + 0.5714285714285714im

julia> 3//2 / (1 + 2im)
3//10 - 3//5*im

julia> 1//2 + 2im
1//2 + 2//1*im

julia> 1 + 2//3im
1//1 - 2//3*im

julia> 0.5 == 1//2
true

julia> 0.33 == 1//3
false

julia> 0.33 < 1//3
true

julia> 1//3 - 0.33
0.0033333333333332993
```
