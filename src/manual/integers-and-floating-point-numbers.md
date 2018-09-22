`[](# Integers and Floating-Point Numbers)
# 整数と浮動小数点数

```@raw html
<!--
Integers and floating-point values are the basic building blocks of arithmetic and computation.
Built-in representations of such values are called numeric primitives, while representations of
integers and floating-point numbers as immediate values in code are known as numeric literals.
For example, `1` is an integer literal, while `1.0` is a floating-point literal; their binary
in-memory representations as objects are numeric primitives.
-->
```

整数と浮動小数点数は計算処理の基本要素です。
これらの組み込みの表現は、数値プリミティブと呼ばれますが、
コード中に書かれる具体的なデータの表現は数値リテラルと知られています。
例えば、`1`は整数リテラルで`1.0`は浮動小数点数リテラルです。
これらのオブジェクトのメモリ上のバイナリ表現が数値プリミティブです。


```@raw html
<!--
Julia provides a broad range of primitive numeric types, and a full complement of arithmetic and
bitwise operators as well as standard mathematical functions are defined over them. These map
directly onto numeric types and operations that are natively supported on modern computers, thus
allowing Julia to take full advantage of computational resources. Additionally, Julia provides
software support for [Arbitrary Precision Arithmetic](@ref), which can handle operations on numeric
values that cannot be represented effectively in native hardware representations, but at the cost
of relatively slower performance.
-->
```

Juliaにはさまざまな種類・範囲の数値プリミティブ型がありますが、
それぞれの型に対して算術演算やビット演算がすべて通常の数学関数と同様に定義されています。
こうした型や演算子は今時のコンピュータならネイティブに利用できる型や演算子と直接対応しているので、
Juliaでは計算資源を最大限に活用することができます。
さらに、Juliaではソフトウェアによる[任意精度演算](@ref)が利用可能なので、
ネイティブなハードウェア表現の事実上不可能な値も扱えますが、
パフォーマンスは比較的遅くなってしまいます。


```@raw html
<!--
The following are Julia's primitive numeric types:
-->
```
以下にJuliaのプリミティブ数値型を挙げていきます。

`[](  * **Integer types:**)
  * **整数型:**

```@raw html
<!--
| Type              | Signed? | Number of bits | Smallest value | Largest value |
|:----------------- |:------- |:-------------- |:-------------- |:------------- |
| [`Int8`](@ref)    | ✓       | 8              | -2^7           | 2^7 - 1       |
| [`UInt8`](@ref)   |         | 8              | 0              | 2^8 - 1       |
| [`Int16`](@ref)   | ✓       | 16             | -2^15          | 2^15 - 1      |
| [`UInt16`](@ref)  |         | 16             | 0              | 2^16 - 1      |
| [`Int32`](@ref)   | ✓       | 32             | -2^31          | 2^31 - 1      |
| [`UInt32`](@ref)  |         | 32             | 0              | 2^32 - 1      |
| [`Int64`](@ref)   | ✓       | 64             | -2^63          | 2^63 - 1      |
| [`UInt64`](@ref)  |         | 64             | 0              | 2^64 - 1      |
| [`Int128`](@ref)  | ✓       | 128            | -2^127         | 2^127 - 1     |
| [`UInt128`](@ref) |         | 128            | 0              | 2^128 - 1     |
| [`Bool`](@ref)    | N/A     | 8              | `false` (0)    | `true` (1)    |

-->
```

| 型　              | 符号付? | ビット数         | 最小値 　　　　| 最大値　　　　　|
|:----------------- |:------- |:-------------- |:-------------- |:------------- |
| [`Int8`](@ref)    | ✓       | 8              | -2^7           | 2^7 - 1       |
| [`UInt8`](@ref)   |         | 8              | 0              | 2^8 - 1       |
| [`Int16`](@ref)   | ✓       | 16             | -2^15          | 2^15 - 1      |
| [`UInt16`](@ref)  |         | 16             | 0              | 2^16 - 1      |
| [`Int32`](@ref)   | ✓       | 32             | -2^31          | 2^31 - 1      |
| [`UInt32`](@ref)  |         | 32             | 0              | 2^32 - 1      |
| [`Int64`](@ref)   | ✓       | 64             | -2^63          | 2^63 - 1      |
| [`UInt64`](@ref)  |         | 64             | 0              | 2^64 - 1      |
| [`Int128`](@ref)  | ✓       | 128            | -2^127         | 2^127 - 1     |
| [`UInt128`](@ref) |         | 128            | 0              | 2^128 - 1     |
| [`Bool`](@ref)    | N/A     | 8              | `false` (0)    | `true` (1)    |


`[](  * **Floating-point types:**)
  * **浮動小数点数型:**

```@raw html
<!--
| Type              | Precision                                                                      | Number of bits |
|:----------------- |:------------------------------------------------------------------------------ |:-------------- |
| [`Float16`](@ref) | [half](https://en.wikipedia.org/wiki/Half-precision_floating-point_format)     | 16             |
| [`Float32`](@ref) | [single](https://en.wikipedia.org/wiki/Single_precision_floating-point_format) | 32             |
| [`Float64`](@ref) | [double](https://en.wikipedia.org/wiki/Double_precision_floating-point_format) | 64             |

-->
```

| 型                | 精度                                                                           | ビット数       |
|:----------------- |:------------------------------------------------------------------------------ |:-------------- |
| [`Float16`](@ref) | [半精度](https://en.wikipedia.org/wiki/Half-precision_floating-point_format)   | 16             |
| [`Float32`](@ref) | [単精度](https://en.wikipedia.org/wiki/Single_precision_floating-point_format) | 32             |
| [`Float64`](@ref) | [倍精度](https://en.wikipedia.org/wiki/Double_precision_floating-point_format) | 64             |


```@raw html
<!--
Additionally, full support for [Complex and Rational Numbers](@ref) is built on top of these primitive
numeric types. All numeric types interoperate naturally without explicit casting, thanks to a
flexible, user-extensible [type promotion system](@ref conversion-and-promotion).
-->
```

さらに、Juliaは[複素数と有理数](@ref)に完全対応していますが、こういったプリミティブ数値型のもとに構築されています。
すべての数値型はわざわざキャストしなくても自然に変換されます。
これは柔軟でユーザーも拡張可能な[型昇格システム](@ref conversion-and-promotion)のおかげです。

`[](## Integers)
## 整数

```@raw html
<!--
Literal integers are represented in the standard manner:
-->
```

整数リテラルは標準的な方法で表現できます。

```jldoctest
julia> 1
1

julia> 1234
1234
```


```@raw html
<!--
The default type for an integer literal depends on whether the target system has a 32-bit architecture
or a 64-bit architecture:
-->
```

整数リテラルのデフォルトの型はターゲットシステムのアーキテクチャが32bitか64bitかで変わります。



```julia-repl
# 32-bit system:
julia> typeof(1)
Int32

# 64-bit system:
julia> typeof(1)
Int64
```

```@raw html
<!--
The Julia internal variable [`Sys.WORD_SIZE`](@ref) indicates whether the target system is 32-bit
or 64-bit:
-->
```

Juliaの内部変数[`Sys.WORD_SIZE`](@ref)からターゲットシステムが32bitと64bitのどちらなのかが分かります。

```julia-repl
# 32-bit system:
julia> Sys.WORD_SIZE
32

# 64-bit system:
julia> Sys.WORD_SIZE
64
```

```@raw html
<!--
Julia also defines the types `Int` and `UInt`, which are aliases for the system's signed and unsigned
native integer types respectively:
-->
```
Juliaでは`Int` や `UInt` といった型も定義されていますが、それぞれシステムネイティブの符号付き整数・符号なし整数の型のエイリアスです。



```julia-repl
# 32-bit system:
julia> Int
Int32
julia> UInt
UInt32

# 64-bit system:
julia> Int
Int64
julia> UInt
UInt64
```

```@raw html
<!--
Larger integer literals that cannot be represented using only 32 bits but can be represented in
64 bits always create 64-bit integers, regardless of the system type:
-->
```
大きな整数リテラルで、32bitでは表現できないけれども、64bitなら可能なものは、システムの整数型にかかわらず、常に64bitの整数が
生成されます。



```jldoctest
# 32-bit or 64-bit system:
julia> typeof(3000000000)
Int64
```

```@raw html
<!--
Unsigned integers are input and output using the `0x` prefix and hexadecimal (base 16) digits
`0-9a-f` (the capitalized digits `A-F` also work for input). The size of the unsigned value is
determined by the number of hex digits used:
-->
```

符号なし整数の入出力には、`0x`を頭につけた16進数の数字`0-9a-f`(入力には大文字の`A-F`も利用可能)を使います。
符号なしの値のサイズは、使っている16進数の数字の数で決まります。



```jldoctest
julia> 0x1
0x01

julia> typeof(ans)
UInt8

julia> 0x123
0x0123

julia> typeof(ans)
UInt16

julia> 0x1234567
0x01234567

julia> typeof(ans)
UInt32

julia> 0x123456789abcdef
0x0123456789abcdef

julia> typeof(ans)
UInt64

julia> 0x11112222333344445555666677778888
0x11112222333344445555666677778888

julia> typeof(ans)
UInt128
```

```@raw html
<!--
This behavior is based on the observation that when one uses unsigned hex literals for integer
values, one typically is using them to represent a fixed numeric byte sequence, rather than just
an integer value.

Recall that the variable [`ans`](@ref) is set to the value of the last expression evaluated in
an interactive session. This does not occur when Julia code is run in other ways.

Binary and octal literals are also supported:
-->
```

こうした挙動は、符号なしの16進リテラルを使うときは、通常、単なる整数値としてよりも長さの決まったバイト列として使うだろう
という考察に基づいています。

 変数[`ans`](@ref)には、対話セッションで最後に評価された式の値が代入されることを、思い出してください。
 Juliaのコードを別の方法で実行しても、何もだいにゅうされません。

 2進・8進のリテラルにも対応しています。
 

```jldoctest
julia> 0b10
0x02

julia> typeof(ans)
UInt8

julia> 0o010
0x08

julia> typeof(ans)
UInt8

julia> 0x00000000000000001111222233334444
0x00000000000000001111222233334444

julia> typeof(ans)
UInt128
```

```@raw html
<!--
As for hexadecimal literals, binary and octal literals produce unsigned integer types. The size
of the binary data item is the minimal needed size, if the leading digit of the literal is not
`0`. In the case of leading zeros, the size is determined by the minimal needed size for a
literal, which has the same length but leading digit `1`. That allows the user to control
the size.
Values, which cannot be stored in `UInt128` cannot be written as such literals.

Binary, octal, and hexadecimal literals may be signed by a `-` immediately preceding the
unsigned literal. They produce an unsigned integer of the same size as the unsigned literal
would do, with the two's complement of the value:
-->
```

2進・8進・16進リテラルに対しては、符号なし整数型が生成されます。
リテラルの先頭の桁が`0`ではない場合、バイナリデータのサイズは必要最低限のものになります。
先頭の桁が`0`の場合のサイズは、その数と同じ長さで先頭が`1`のリテラルに必要最低限のサイズになります。
値が`UInt128`に収まらないものは、リテラルを使って書くことができません。

2進・8進・16進リテラルの直前に`-`をつけて、符号をつけることができます。
この場合生成されるのは、元の数とサイズの等しい符号なしの整数で、元の数の2の補数表現となるものです。




```jldoctest
julia> -0x2
0xfe

julia> -0x0002
0xfffe
```

```@raw html
<!--
The minimum and maximum representable values of primitive numeric types such as integers are given
by the [`typemin`](@ref) and [`typemax`](@ref) functions:
-->
```

 [`typemin`](@ref) や [`typemax`](@ref)　といった関数を使うと、
 整数などのプリミティブ数値型が表現できる値の下限と上限がわかります。 


```jldoctest
julia> (typemin(Int32), typemax(Int32))
(-2147483648, 2147483647)

julia> for T in [Int8,Int16,Int32,Int64,Int128,UInt8,UInt16,UInt32,UInt64,UInt128]
           println("$(lpad(T,7)): [$(typemin(T)),$(typemax(T))]")
       end
   Int8: [-128,127]
  Int16: [-32768,32767]
  Int32: [-2147483648,2147483647]
  Int64: [-9223372036854775808,9223372036854775807]
 Int128: [-170141183460469231731687303715884105728,170141183460469231731687303715884105727]
  UInt8: [0,255]
 UInt16: [0,65535]
 UInt32: [0,4294967295]
 UInt64: [0,18446744073709551615]
UInt128: [0,340282366920938463463374607431768211455]
```

```@raw html
<!--
The values returned by [`typemin`](@ref) and [`typemax`](@ref) are always of the given argument
type. (The above expression uses several features we have yet to introduce, including [for loops](@ref man-loops),
[Strings](@ref man-strings), and [Interpolation](@ref), but should be easy enough to understand for users
with some existing programming experience.)
-->
```
 [`typemin`](@ref) や [`typemax`](@ref)といった関数の返す値は、常に引数の示す型になります。
(上記の式ではまだ紹介していない特徴である [for ループ](@ref man-loops),
[文字列](@ref man-strings),[式展開](@ref))などを使っていますが、他のプログラミング経験のある人なら簡単に理解できるでしょう。)


`[](### Overflow behavior)
### オーバーフロー時の挙動

```@raw html
<!--
In Julia, exceeding the maximum representable value of a given type results in a wraparound behavior:
-->
```
Juliaでは、値がその型の表現可能な上限を超えるとラップアラウンド(循環)がおこります。

```jldoctest
julia> x = typemax(Int64)
9223372036854775807

julia> x + 1
-9223372036854775808

julia> x + 1 == typemin(Int64)
true
```

```@raw html
<!--
Thus, arithmetic with Julia integers is actually a form of [modular arithmetic](https://en.wikipedia.org/wiki/Modular_arithmetic).
This reflects the characteristics of the underlying arithmetic of integers as implemented on modern
computers. In applications where overflow is possible, explicit checking for wraparound produced
by overflow is essential; otherwise, the [`BigInt`](@ref) type in [Arbitrary Precision Arithmetic](@ref)
is recommended instead.
-->
```

このように、Juliaの整数演算は実質的に[合同算術](https://en.wikipedia.org/wiki/Modular_arithmetic)を行っています。
これは現代のコンピュータでの整数算術の実装方法の特徴を反映しています。
オーバーフローの起こりうるアプリケーションでは、オーバーフローによってラップアラウンドが起こっていないかの、明示的な検査が不可欠です。
別の方法としては[`BigInt`](@ref)を使った[任意精度演算](@ref)がお薦めです。



`[](### Division errors)
### 除算エラー

```@raw html
<!--
Integer division (the `div` function) has two exceptional cases: dividing by zero, and dividing
the lowest negative number ([`typemin`](@ref)) by -1. Both of these cases throw a [`DivideError`](@ref).
The remainder and modulus functions (`rem` and `mod`) throw a [`DivideError`](@ref) when their
second argument is zero.
-->
```

整数の除算(`div`関数)では、2つ例外が起こる場合があります。0で割る場合と、負の下限の数([`typemin`](@ref))を-1で割る場合です。
どちらの場合も[`DivideError`](@ref)例外が投げられます。
剰余演算(`rem`と`mod`)も第2引数が0の時も
、[`DivideError`](@ref)例外が投げられます。


`[](## Floating-Point Numbers)
## 浮動小数点数

```@raw html
<!--
Literal floating-point numbers are represented in the standard formats, using
[E-notation](https://en.wikipedia.org/wiki/Scientific_notation#E-notation) when necessary:
-->
```

浮動小数点数のリテラルは標準的なものです。必要に応じて[E記法](https://en.wikipedia.org/wiki/Scientific_notation#E-notation)
も使えます。

```jldoctest
julia> 1.0
1.0

julia> 1.
1.0

julia> 0.5
0.5

julia> .5
0.5

julia> -1.23
-1.23

julia> 1e10
1.0e10

julia> 2.5e-4
0.00025
```

```@raw html
<!--
The above results are all [`Float64`](@ref) values. Literal [`Float32`](@ref) values can be
entered by writing an `f` in place of `e`:
-->
```
上記の計算結果はすべて[`Float64`](@ref)の値です。
`e`の代わりに`f`をつけると、[`Float32`](@ref)リテラルの値になります。

```jldoctest
julia> 0.5f0
0.5f0
ＳＷ
julia> typeof(ans)
Float32

julia> 2.5f-4
0.00025f0
```

```@raw html
<!--
Values can be converted to [`Float32`](@ref) easily:
-->
```

値を[`Float32`](@ref)に変換するのも簡単です。


```jldoctest
julia> Float32(-1.5)
-1.5f0

julia> typeof(ans)
Float32
```

```@raw html
<!--
Hexadecimal floating-point literals are also valid, but only as [`Float64`](@ref) values,
with `p` preceding the base-2 exponent:
-->
```

16進の浮動小数点数リテラルは、指数(基数は2)の前に`p`をつけて利用し、値は[`Float64`](@ref) になります。


```jldoctest
julia> 0x1p0
1.0

julia> 0x1.8p3
12.0

julia> 0x.4p-1
0.125

julia> typeof(ans)
Float64
```

```@raw html
<!--
Half-precision floating-point numbers are also supported ([`Float16`](@ref)), but they are
implemented in software and use [`Float32`](@ref) for calculations.
-->
```

半精度の浮動小数点数にも対応していますが ([`Float16`](@ref))、ソフトウェア上の実装で、演算には [`Float32`](@ref)を使います。


```jldoctest
julia> sizeof(Float16(4.))
2

julia> 2*Float16(4.)
Float16(8.0)
```

```@raw html
<!--
The underscore `_` can be used as digit separator:
-->
```

桁の区切りとしてアンダースコア`_`を使うことができます。

```jldoctest
julia> 10_000, 0.000_000_005, 0xdead_beef, 0b1011_0010
(10000, 5.0e-9, 0xdeadbeef, 0xb2)
```

[](### Floating-point zero)
### 浮動小数点数の0

```@raw html
<!--
Floating-point numbers have [two zeros](https://en.wikipedia.org/wiki/Signed_zero), positive zero
and negative zero. They are equal to each other but have different binary representations, as
can be seen using the [`bitstring`](@ref) function:
-->
```

浮動小数点数には正と負の[2つの0](https://en.wikipedia.org/wiki/Signed_zero)があります。
この2つは値は同じですが2進数の表現が異なり、[`bitstring`](@ref)関数で見ることができます。


```jldoctest
julia> 0.0 == -0.0
true

julia> bitstring(0.0)
"0000000000000000000000000000000000000000000000000000000000000000"

julia> bitstring(-0.0)
"1000000000000000000000000000000000000000000000000000000000000000"
```
[](### Special floating-point values)
### 特殊な浮動小数点数の値

```@raw html
<!--
There are three specified standard floating-point values that do not correspond to any point on
the real number line:
-->
```

浮動小数点数の値で実数の数直線上に対応する点のないものが3つあります。

```@raw html
<!--
| `Float16` | `Float32` | `Float64` | Name              | Description                                                     |
|:--------- |:--------- |:--------- |:----------------- |:--------------------------------------------------------------- |
| `Inf16`   | `Inf32`   | `Inf`     | positive infinity | a value greater than all finite floating-point values           |
| `-Inf16`  | `-Inf32`  | `-Inf`    | negative infinity | a value less than all finite floating-point values              |
| `NaN16`   | `NaN32`   | `NaN`     | not a number      | a value not `==` to any floating-point value (including itself) |
-->
```

| `Float16` | `Float32` | `Float64` |名前              | 説明                                                     |
|:--------- |:--------- |:--------- |:---------------- |:--------------------------------------------------------------- |
| `Inf16`   | `Inf32`   | `Inf`     | 正の無限大        | すべての有限の浮動小数点数より大きい値           |
| `-Inf16`  | `-Inf32`  | `-Inf`    | 負の無限大        | すべての有限の浮動小数点数より小さい値               |
| `NaN16`   | `NaN32`   | `NaN`     | 非数             | どんな浮動小数点数とも `==` の成り立たない値(自身とも) |



```@raw html
<!--
For further discussion of how these non-finite floating-point values are ordered with respect
to each other and other floats, see [Numeric Comparisons](@ref). By the [IEEE 754 standard](https://en.wikipedia.org/wiki/IEEE_754-2008),
these floating-point values are the results of certain arithmetic operations:
-->
```

こうした非有限の浮動小数点数を、非有限・有限のものに対してどう順序づけるかという更なる議論は、[数値の比較](@ref)を参照してください。
 [IEEE 754 規格](https://en.wikipedia.org/wiki/IEEE_754-2008)では、
 これらの浮動小数点数の値はある種の算術演算の結果として得られます。


```jldoctest
julia> 1/Inf
0.0

julia> 1/0
Inf

julia> -5/0
-Inf

julia> 0.000001/0
Inf

julia> 0/0
NaN

julia> 500 + Inf
Inf

julia> 500 - Inf
-Inf

julia> Inf + Inf
Inf

julia> Inf - Inf
NaN

julia> Inf * Inf
Inf

julia> Inf / Inf
NaN

julia> 0 * Inf
NaN
```

```@raw html
<!--
The [`typemin`](@ref) and [`typemax`](@ref) functions also apply to floating-point types:
-->
```

 関数の[`typemin`](@ref) と [`typemax`](@ref)は浮動小数点数型に対しても適用できます。

```jldoctest
julia> (typemin(Float16),typemax(Float16))
(-Inf16, Inf16)

julia> (typemin(Float32),typemax(Float32))
(-Inf32, Inf32)

julia> (typemin(Float64),typemax(Float64))
(-Inf, Inf)
```

`[](### Machine epsilon)
### 計算機イプシロン

```@raw html
<!--
Most real numbers cannot be represented exactly with floating-point numbers, and so for many purposes
it is important to know the distance between two adjacent representable floating-point numbers,
which is often known as [machine epsilon](https://en.wikipedia.org/wiki/Machine_epsilon).

Julia provides [`eps`](@ref), which gives the distance between `1.0` and the next larger representable
floating-point value:
-->
```

ほとんどの実数は浮動小数点数では、正確に表現できません。
そのため、多くの目的で、隣接する浮動小数点数の距離を知ることが重要です。
これは、[計算機イプシロン](https://en.wikipedia.org/wiki/Machine_epsilon)として、知られています。

Juliaでは[`eps`](@ref)で、`1.0`と表現可能な次に大きな浮動小数点値との距離が、分かります。

```jldoctest
julia> eps(Float32)
1.1920929f-7

julia> eps(Float64)
2.220446049250313e-16

julia> eps() # same as eps(Float64)
2.220446049250313e-16
```

```@raw html
<!--
These values are `2.0^-23` and `2.0^-52` as [`Float32`](@ref) and [`Float64`](@ref) values,
respectively. The [`eps`](@ref) function can also take a floating-point value as an
argument, and gives the absolute difference between that value and the next representable
floating point value. That is, `eps(x)` yields a value of the same type as `x` such that
`x + eps(x)` is the next representable floating-point value larger than `x`:
-->
```

これら`2.0^-23`と`2.0^-52`は、それぞれ[`Float32`](@ref)と[`Float64`](@ref)の値です。
[`eps`](@ref)関数は、浮動小数点数値を引数にとることもできて、その値と次の表現可能な浮動小数点数値との差の絶対値
が得られます。
つまり、`eps(x)`は`x`と同じ型の値を返すので、`x + eps(x)`は`x`の次に大きな表現可能な浮動小数点数値です。


```jldoctest
julia> eps(1.0)
2.220446049250313e-16

julia> eps(1000.)
1.1368683772161603e-13

julia> eps(1e-27)
1.793662034335766e-43

julia> eps(0.0)
5.0e-324
```

```@raw html
<!--
The distance between two adjacent representable floating-point numbers is not constant, but is
smaller for smaller values and larger for larger values. In other words, the representable floating-point
numbers are densest in the real number line near zero, and grow sparser exponentially as one moves
farther away from zero. By definition, `eps(1.0)` is the same as `eps(Float64)` since `1.0` is
a 64-bit floating-point value.

Julia also provides the [`nextfloat`](@ref) and [`prevfloat`](@ref) functions which return
the next largest or smallest representable floating-point number to the argument respectively:
-->
```

隣接する表現可能な浮動小数点数の距離は一定ではなく、小さいほど小さく、大きいほど大きくなります。
言い換えると、表現可能な浮動小数点数の数直線は、0の近くで一番密で、0から遠ざかるほど指数的に疎になります。
定義により、`eps(1.0)`は`eps(Float64)`と同じです。
というのも、 `1.0`は64bitの浮動小数点数値だからです。

Juliaでは[`nextfloat`](@ref)や[`prevfloat`](@ref)といった関数も利用可能で、それぞれ表現可能な浮動小数点数で
次に大きなものと小さなものを返します。



```jldoctest
julia> x = 1.25f0
1.25f0

julia> nextfloat(x)
1.2500001f0

julia> prevfloat(x)
1.2499999f0

julia> bitstring(prevfloat(x))
"00111111100111111111111111111111"

julia> bitstring(x)
"00111111101000000000000000000000"

julia> bitstring(nextfloat(x))
"00111111101000000000000000000001"
```

```@raw html
<!--
This example highlights the general principle that the adjacent representable floating-point numbers
also have adjacent binary integer representations.
-->
```

この例では隣接する表現可能な浮動小数点数は、２進整数表現も隣接するという一般原則が強調されています。


`[](### Rounding modes)
### 端数処理


```@raw html
<!--
If a number doesn't have an exact floating-point representation, it must be rounded to an
appropriate representable value. However, the manner in which this rounding is done can be
changed if required according to the rounding modes presented in the [IEEE 754
standard](https://en.wikipedia.org/wiki/IEEE_754-2008).

The default mode used is always [`RoundNearest`](@ref), which rounds to the nearest representable
value, with ties rounded towards the nearest value with an even least significant bit.

-->
```
浮動小数点数表現で正確に表現できない数は、適当な表現可能な数に丸める必要があります。
しかしその処理方法は、必要に応じて、[IEEE 754 規格](https://en.wikipedia.org/wiki/IEEE_754-2008)に記載されている丸めモードにそって変えることができます。

デフォルトの丸めモードは常に[`最近接丸め`](@ref)で、これは一番近い表現可能な数に丸める方法で、最近接の表現可能な数が２つある場合は
最下位ビットが偶数になるように丸めます。

`[](### Background and References)
### 背景と参考資料

```@raw html
<!--
Floating-point arithmetic entails many subtleties which can be surprising to users who are unfamiliar
with the low-level implementation details. However, these subtleties are described in detail in
most books on scientific computation, and also in the following references:
-->
```

浮動小数点数の算術には低レベルの実装の詳細なじみのない人が驚くような微妙な点がたくさんあります。
しかしその微妙な点の詳細は、大抵の科学計算に関する本や下記の資料に記載されています。




```@raw html
<!--

  * The definitive guide to floating point arithmetic is the [IEEE 754-2008 Standard](http://standards.ieee.org/findstds/standard/754-2008.html);
    however, it is not available for free online.
  * For a brief but lucid presentation of how floating-point numbers are represented, see John D.
    Cook's [article](https://www.johndcook.com/blog/2009/04/06/anatomy-of-a-floating-point-number/)
    on the subject as well as his [introduction](https://www.johndcook.com/blog/2009/04/06/numbers-are-a-leaky-abstraction/)
    to some of the issues arising from how this representation differs in behavior from the idealized
    abstraction of real numbers.
  * Also recommended is Bruce Dawson's [series of blog posts on floating-point numbers](https://randomascii.wordpress.com/2012/05/20/thats-not-normalthe-performance-of-odd-floats/).
  * For an excellent, in-depth discussion of floating-point numbers and issues of numerical accuracy
    encountered when computing with them, see David Goldberg's paper [What Every Computer Scientist Should Know About Floating-Point Arithmetic](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.22.6768&rep=rep1&type=pdf).
  * For even more extensive documentation of the history of, rationale for, and issues with floating-point
    numbers, as well as discussion of many other topics in numerical computing, see the [collected writings](https://people.eecs.berkeley.edu/~wkahan/)
    of [William Kahan](https://en.wikipedia.org/wiki/William_Kahan), commonly known as the "Father
    of Floating-Point". Of particular interest may be [An Interview with the Old Man of Floating-Point](https://people.eecs.berkeley.edu/~wkahan/ieee754status/754story.html).


-->
```

  * 浮動小数点数の演算 に関する最も信頼のおけるガイドは [IEEE 754-2008 規格](http://standards.ieee.org/findstds/standard/754-2008.html)です。
    しかし、無料・オンラインでは利用不可。
  * 浮動小数点数の表現方法に関する簡潔だが明快な解説として、John D.Cook'の [記事](https://www.johndcook.com/blog/2009/04/06/anatomy-of-a-floating-point-number/)
    を参照のこと。
     [入門](https://www.johndcook.com/blog/2009/04/06/numbers-are-a-leaky-abstraction/)
    では理想化された抽象的な実数と表現とのの違いからおこる問題が書かれている。
  * 同様にお薦めなのが、Bruce Dawsonの[浮動小数点数に関する一連のブログ投稿](https://randomascii.wordpress.com/2012/05/20/thats-not-normalthe-performance-of-odd-floats/).
  * 浮動小数点数に関する秀逸で深遠な議論と、その計算時に遭遇する数的精度の問題は
    David Goldbergの論文 [すべとの計算科学者が浮動小数点数演算について知るべきこと](http://citeseerx.ist.psu.edu/viewdoc/donload?doi=10.1.1.22.6768&rep=rep1&type=pdf)を参照のこと。
  * 浮動小数点数に関する歴史・理論・問題さらにその他の多くの数値計算に関する話題を集めた広範囲にわたる文書として[著作集](https://people.eecs.berkeley.edu/~wkahan/)を参照のこと。
    著者 [William Kahan](https://en.wikipedia.org/wiki/William_Kahan)は「浮動小数点の父」として広く知られている。
    特に興味深いのは [浮動小数点の老人へのインタビュー](https://people.eecs.berkeley.edu/~wkahan/ieee754status/754story.html)。


`[](## Arbitrary Precision Arithmetic)
## 任意精度演算

```@raw html
<!--
To allow computations with arbitrary-precision integers and floating point numbers, Julia wraps
the [GNU Multiple Precision Arithmetic Library (GMP)](https://gmplib.org) and the [GNU MPFR Library](http://www.mpfr.org),
respectively. The [`BigInt`](@ref) and [`BigFloat`](@ref) types are available in Julia for arbitrary
precision integer and floating point numbers respectively.

Constructors exist to create these types from primitive numerical types, and [`parse`](@ref)
can be used to construct them from `AbstractString`s.  Once created, they participate in arithmetic
with all other numeric types thanks to Julia's [type promotion and conversion mechanism](@ref conversion-and-promotion):
-->
```
整数と浮動小数点数に対して任意精度で計算できるように、Juliaは[GNU Multiple Precision Arithmetic Library (GMP)](https://gmplib.org) と [GNU MPFR Library](http://www.mpfr.org)に対するラップをそれぞれ行っています。
 [`BigInt`](@ref) と [`BigFloat`](@ref)の型がそれぞれ任意精度の整数と浮動小数点数として利用可能です。



コンストラクタは、プリミティブ数値型から、これらの型を生成し、[`parse`](@ref)は`AbstractString`からこれらの型を生成します。
生成した値は他の数値型と演算することができ、これはJuliaの [型昇格と型変換のしくみ](@ref conversion-and-promotion)のおかげです。

```jldoctest
julia> BigInt(typemax(Int64)) + 1
9223372036854775808

julia> parse(BigInt, "123456789012345678901234567890") + 1
123456789012345678901234567891

julia> parse(BigFloat, "1.23456789012345678901")
1.234567890123456789010000000000000000000000000000000000000000000000000000000004

julia> BigFloat(2.0^66) / 3
2.459565876494606882133333333333333333333333333333333333333333333333333333333344e+19

julia> factorial(BigInt(40))
815915283247897734345611269596115894272000000000
```

```@raw html
<!--
However, type promotion between the primitive types above and [`BigInt`](@ref)/[`BigFloat`](@ref)
is not automatic and must be explicitly stated.
-->
```
しかしプリミティブ型と[`BigInt`](@ref)/[`BigFloat`](@ref)との型昇格は自動では行われず、明示的に記述する必要があります。



```jldoctest
julia> x = typemin(Int64)
-9223372036854775808

julia> x = x - 1
9223372036854775807

julia> typeof(x)
Int64

julia> y = BigInt(typemin(Int64))
-9223372036854775808

julia> y = y - 1
-9223372036854775809

julia> typeof(y)
BigInt
```

```@raw html
<!--
The default precision (in number of bits of the significand) and rounding mode of [`BigFloat`](@ref)
operations can be changed globally by calling [`setprecision`](@ref) and [`setrounding`](@ref),
and all further calculations will take these changes in account.  Alternatively, the precision
or the rounding can be changed only within the execution of a particular block of code by using
the same functions with a `do` block:
-->
```

 [`BigFloat`](@ref)演算のデフォルトの精度(仮数のビット数)や丸めモードは、
[`setprecision`](@ref)や[`setrounding`](@ref)をグローバル環境で呼び出すことで変更可能で、
以降のすべての計算がこの変更の影響を受けます。
一方、精度や丸めモードの変更を特定のブロック内の実行だけにとどめたい場合は、同じ関数を
`do`ブロック内で呼び出せば可能です。

```jldoctest
julia> setrounding(BigFloat, RoundUp) do
           BigFloat(1) + parse(BigFloat, "0.1")
       end
1.100000000000000000000000000000000000000000000000000000000000000000000000000003

julia> setrounding(BigFloat, RoundDown) do
           BigFloat(1) + parse(BigFloat, "0.1")
       end
1.099999999999999999999999999999999999999999999999999999999999999999999999999986

julia> setprecision(40) do
           BigFloat(1) + parse(BigFloat, "0.1")
       end
1.1000000000004
```

`[](## [Numeric Literal Coefficients](@id man-numeric-literal-coefficients))
## [数値リテラル係数](@id man-numeric-literal-coefficients)

```@raw html
<!--
To make common numeric formulae and expressions clearer, Julia allows variables to be immediately
preceded by a numeric literal, implying multiplication. This makes writing polynomial expressions
much cleaner:
-->
```
数式一般を明快にするために、Juliaでは変数の直前に数値リテラルを書いて掛け算を表すことができます。
これを使って多項式を書くとわかりやすくなります。


```jldoctest numeric-coefficients
julia> x = 3
3

julia> 2x^2 - 3x + 1
10

julia> 1.5x^2 - .5x + 1
13.0
```

```@raw html
<!--
It also makes writing exponential functions more elegant:
-->
```
巾関数もきれいに書けます。

```jldoctest numeric-coefficients
julia> 2^2x
64
```

```@raw html
<!--
The precedence of numeric literal coefficients is slightly lower than that of
unary operators such as negation.
So `-2x` is parsed as `(-2) * x` and `√2x` is parsed as `(√2) * x`.
However, numeric literal coefficients parse similarly to unary operators when
combined with exponentiation.
For example `2^3x` is parsed as `2^(3x)`, and `2x^3` is parsed as `2*(x^3)`.

Numeric literals also work as coefficients to parenthesized expressions:
-->
```

数値リテラル係数の優先順位はマイナスなどの単項演算子低いです。
そのため `-2x`は`(-2) * x`に解析され、 `√2x` は `(√2) * x`と解析されます。
しかし巾関数と結合するときは、単項演算子と同様に解析されます。
例えば、 `2^3x` は `2^(3x)`、 `2x^3` は `2*(x^3)`というように解析されます。


```jldoctest numeric-coefficients
julia> 2(x-1)^2 - 3(x-1) + 1
3
```
```@raw html
<!--
!!! note
    The precedence of numeric literal coefficients used for implicit
    multiplication is higher than other binary operators such as multiplication
    (`*`), and division (`/`, `\`, and `//`).  This means, for example, that
    `1 / 2im` equals `-0.5im` and `6 // 2(2 + 1)` equals `1 // 1`.
-->
```
!!! 注意
    掛け算を暗に示す数値リテラル係数は他の2項演算子、例えば、乗法(`*`)や除法 (`/`, `\`, `//`)よりも
    優先順位は高いです。
    例えば、`1 / 2im`は`-0.5im`と等しく、`6 // 2(2 + 1)`は`1 // 1`と等しいです。


```@raw html
<!--
Additionally, parenthesized expressions can be used as coefficients to variables, implying multiplication
of the expression by the variable:
-->
```

さらに、括弧でくくった式を変数に対する係数として扱い、式と変数の掛け算を行うことができます。

```jldoctest numeric-coefficients
julia> (x-1)x
6
```

```@raw html
<!--
Neither juxtaposition of two parenthesized expressions, nor placing a variable before a parenthesized
expression, however, can be used to imply multiplication:
-->
```

しかし、括弧でくくった式を２つ並べたり、括弧でくくった式の前に変数を置いても、掛け算とはみなされません。


```jldoctest numeric-coefficients
julia> (x-1)(x+1)
ERROR: MethodError: objects of type Int64 are not callable

julia> x(x+1)
ERROR: MethodError: objects of type Int64 are not callable
```

```@raw html
<!--
Both expressions are interpreted as function application: any expression that is not a numeric
literal, when immediately followed by a parenthetical, is interpreted as a function applied to
the values in parentheses (see [Functions](@ref) for more about functions). Thus, in both of these
cases, an error occurs since the left-hand value is not a function.

The above syntactic enhancements significantly reduce the visual noise incurred when writing common
mathematical formulae. Note that no whitespace may come between a numeric literal coefficient
and the identifier or parenthesized expression which it multiplies.
-->
```
どちらの式も関数適用として解釈されます。
数値リテラルではない式に括弧でくくったものを続けると、括弧の中のものに対する関数適用だと解釈されます
( 関数に関する詳細は[関数](@ref)を参照)。
どちらのケースも左側が関数ではないためエラーがおこります。

こうした構文の拡張によって、普通に書いた数式の見た目の煩わしさが大幅に減っています。
掛け算をおこなうには、数値リテラル係数と、識別子や括弧でくくった式の間に、空白を入れてはいけないことに注意してください。




`[](### Syntax Conflicts)
### 構文の競合

```@raw html
<!--
Juxtaposed literal coefficient syntax may conflict with two numeric literal syntaxes: hexadecimal
integer literals and engineering notation for floating-point literals. Here are some situations
where syntactic conflicts arise:
-->
```

リテラル係数を並べる構文と競合しうる構文が２つあります。
16進整数リテラルと浮動小数点数リテラルの指数表記です。
競合する例を挙げてみると

```@raw html
<!--
  * The hexadecimal integer literal expression `0xff` could be interpreted as the numeric literal
    `0` multiplied by the variable `xff`.
  * The floating-point literal expression `1e10` could be interpreted as the numeric literal `1` multiplied
    by the variable `e10`, and similarly with the equivalent `E` form.
  * The 32-bit floating-point literal expression `1.5f22` could be interpreted as the numeric literal
    `1.5` multiplied by the variable `f22`.

-->
```
  * 16進整数リテラルの`0xff`は、数値リテラルの`0`と変数`xff`の掛け算と解釈できる。
  * 浮動小数点リテラルの`1e10`は数値リテラル`1`と変数`e10`の掛け算と解釈できる。`E`形式も同様。
  * 32bit浮動小数点リテラルの`1.5f22`数値リテラル`1.5`と変数`f22`の掛け算と解釈できる。

```@raw html
<!--
In all cases, we resolve the ambiguity in favor of interpretation as numeric literals:

  * Expressions starting with `0x` are always hexadecimal literals.
  * Expressions starting with a numeric literal followed by `e` or `E` are always floating-point literals.
  * Expressions starting with a numeric literal followed by `f` are always 32-bit floating-point literals.

-->
```

すべてのケースで、数値リテラルとしての解釈を好んで、曖昧さを解決しています。

  * `0x`で始まる式は、常に16進リテラルである。
  * `e`か`E`を伴う数値リテラルで始まる式は、常に浮動小数点リテラルである。
  * `f`を伴う数値リテラルで始まる式は、常に32bit浮動小数点リテラルである。

```@raw html
<!--
Unlike `E`, which is equivalent to `e` in numeric literals for historical reasons, `F` is just another
letter and does not behave like `f` in numeric literals. Hence, expressions starting with a numeric literal
followed by `F` are interpreted as the numerical literal multiplied by a variable, which means that, for
example, `1.5F22` is equal to `1.5 * F22`.
-->
```

 `E`は歴史的な理由もあって、数値リテラルの中で`e`と同等に扱われますが、`F`の場合はまた別で数値リテラルの中でも`f`のようには扱われません。
なので、`F`を伴う数値リテラルで始まる式は、数値リテラルと変数の掛け算として解釈されます。
例えば、 `1.5F22`は`1.5 * F22`と等しくなります。



`[](## Literal zero and one)
## 0と1のリテラル

```@raw html
<!--
Julia provides functions which return literal 0 and 1 corresponding to a specified type or the
type of a given variable.
-->
```

Juliaには、0や1のリテラルを返す関数で、指定した型や、変数と同じ型となるものがあります。

```@raw html
<!--

| Function          | Description                                      |
|:----------------- |:------------------------------------------------ |
| [`zero(x)`](@ref) | Literal zero of type `x` or type of variable `x` |
| [`one(x)`](@ref)  | Literal one of type `x` or type of variable `x`  |
-->
```

| 関数              | 説明                                      |
|:----------------- |:------------------------------------------------ |
| [`zero(x)`](@ref) | 型`x`の0リテラルか、変数`x`と同じ0リテラル |
| [`one(x)`](@ref)  | 型`x`の1リテラルか、変数`x`と同じ1リテラル  |


```@raw html
<!--
These functions are useful in [Numeric Comparisons](@ref) to avoid overhead from unnecessary
[type conversion](@ref conversion-and-promotion).
-->
```

これらの関数は [数値の比較](@ref)を行うときに、 [型変換](@ref conversion-and-promotion)の不要なオーバーヘッドを
避けるために役立ちます。


Examples:

```jldoctest
julia> zero(Float32)
0.0f0

julia> zero(1.0)
0.0

julia> one(Int32)
1

julia> one(BigFloat)
1.0
```
