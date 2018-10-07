`[](# Methods)
# メソッド

```@raw html
<!--
Recall from [Functions](@ref man-functions) that a function is an object that maps a tuple of arguments to a
return value, or throws an exception if no appropriate value can be returned. It is common for
the same conceptual function or operation to be implemented quite differently for different types
of arguments: adding two integers is very different from adding two floating-point numbers, both
of which are distinct from adding an integer to a floating-point number. Despite their implementation
differences, these operations all fall under the general concept of "addition". Accordingly, in
Julia, these behaviors all belong to a single object: the `+` function.
-->
```

[関数](@ref man-functions) の章を思い出してください。
関数とは、引数のタプルを受け取り、戻り値を返す、または適切な値を返せない場合は例外を投げる、オブジェクトです。
概念的には等しい関数や演算が、引数の型によって実装がまったく異なる、ということはよくあります。
2つの整数を足すことと、2つの浮動小数点数を足すことは、全く異なるし、整数に浮動小数点数を足すこととも異なります。
実装が違っていても、これらの操作はすべて、一般的な概念の「足し算」に当てはまります。
したがって、Juliaでは、これらの挙動はすべて1つのオブジェクト「`+`関数」に属します。


```@raw html
<!--
To facilitate using many different implementations of the same concept smoothly, functions need
not be defined all at once, but can rather be defined piecewise by providing specific behaviors
for certain combinations of argument types and counts. A definition of one possible behavior for
a function is called a *method*. Thus far, we have presented only examples of functions defined
with a single method, applicable to all types of arguments. However, the signatures of method
definitions can be annotated to indicate the types of arguments in addition to their number, and
more than a single method definition may be provided. When a function is applied to a particular
tuple of arguments, the most specific method applicable to those arguments is applied. Thus, the
overall behavior of a function is a patchwork of the behaviors of its various method definitions.
If the patchwork is well designed, even though the implementations of the methods may be quite
different, the outward behavior of the function will appear seamless and consistent.
-->
```

同じ概念の異なる多くの実装を、円滑に利用するためには、関数を一度にすべて定義する必要はなく、引数の型と個数の組み合わせごとに、挙動を指定して、区分的に定義したほうがいいでしょう。
こうした関数の、とりうる挙動の定義のひとつひとつは、**メソッド** と呼ばれます。
今までに例示した関数は、単一のメソッドで定義された、引数に対してすべての型を適用可能な関数だけです。
しかし、メソッド定義のシグネチャに、引数の型と数を指定するために型注釈をつけることも可能で、
メソッドの定義も複数あっても構いません。
関数を具体的な引数の組に適用する場合、その引数に適用可能なものから最も特化したメソッドが適用されます。
つまり、関数全体の挙動は、さまざまなメソッドで定義された挙動のパッチワークです。
パッチワークがうまく設計されていれば、メソッドの実装が全く異なっていても、外側からの関数の挙動は継ぎ目なく一貫しているように見えます。


```@raw html
<!--
The choice of which method to execute when a function is applied is called *dispatch*. Julia allows
the dispatch process to choose which of a function's methods to call based on the number of arguments
given, and on the types of all of the function's arguments. This is different than traditional
object-oriented languages, where dispatch occurs based only on the first argument, which often
has a special argument syntax, and is sometimes implied rather than explicitly written as an argument.
[^1] Using all of a function's arguments to choose which method should be invoked, rather than
just the first, is known as [multiple dispatch](https://en.wikipedia.org/wiki/Multiple_dispatch).
Multiple dispatch is particularly useful for mathematical code, where it makes little sense to
artificially deem the operations to "belong" to one argument more than any of the others: does
the addition operation in `x + y` belong to `x` any more than it does to `y`? The implementation
of a mathematical operator generally depends on the types of all of its arguments. Even beyond
mathematical operations, however, multiple dispatch ends up being a powerful and convenient paradigm
for structuring and organizing programs.
-->
```

関数を適用する際に、実行するメソッドを選択することを、 **ディスパッチ** と呼びます。
Juliaでは、ディスパッチの過程で、関数のメソッドのどれを呼び出すか、関数のすべての引数の型と個数に基づいて、選択することができます。
これは、従来のオブジェクト指向言語とは異なります。従来のオブジェクト指向言語では、通常は最初の引数のみに基づいてディスパッチが行われ、最初の引数を特別視して引数に見えないような構文を持つものもあります。
[^ 1]
関数に対して、最初の引数だけではなく、すべての引数を利用して、呼び出すメソッドを選択することは、[多重ディスパッチ](https://en.wikipedia.org/wiki/Multiple_dispatch)として知られています。
多重ディスパッチは特に数学的なコードで有用です。
演算が、他の引数よりもどの引数に、より「属している」かと不自然なことを考えても、意味がないからです。
`x + y` の式の中の足し算は、 `x` に `y`よりも属してると思いますか。
数学的な演算子の実装は、通常は、すべての引数の型に依存しています。
しかし、数学的な操作以外でも、多重ディスパッチは、プログラムを構築し組織化する強力で便利なパラダイムです。


```@raw html
<!--
[^1]:
    In C++ or Java, for example, in a method call like `obj.meth(arg1,arg2)`, the object obj "receives"
    the method call and is implicitly passed to the method via the `this` keyword, rather than as
    an explicit method argument. When the current `this` object is the receiver of a method call,
    it can be omitted altogether, writing just `meth(arg1,arg2)`, with `this` implied as the receiving
    object.

-->
```
[^ 1]：
　　例えば、C ++やJavaでは、`obj.meth(arg1,arg2)`のようなメソッド呼びだしの時は、オブジェクトobjはメソッド呼び出しを「受け取　　り」、暗黙のうちに`this`キーワードを介して、引数だと明示せずに、メソッドに引き渡します。
　　`this`オブジェクトがメソッド呼び出しのレシーバである場合は省略できます。`meth(arg1,arg2)`と書くだけで、`this`は受け取るオ　　ブジェクトを暗に示します。



`[](## Defining Methods)
## メソッドの定義

```@raw html
<!--
Until now, we have, in our examples, defined only functions with a single method having unconstrained
argument types. Such functions behave just like they would in traditional dynamically typed languages.
Nevertheless, we have used multiple dispatch and methods almost continually without being aware
of it: all of Julia's standard functions and operators, like the aforementioned `+` function,
have many methods defining their behavior over various possible combinations of argument type
and count.

When defining a function, one can optionally constrain the types of parameters it is applicable
to, using the `::` type-assertion operator, introduced in the section on [Composite Types](@ref):
-->
```

今までの例では、引数に型の制約がない、単一のメソッドしかない関数しか定義していませんでした。
そのような関数は、従来の動的型付け言語と同じように動作します。
にもかかわらず、私たちは知らない間に、ずっと多重ディスパッチとメソッドを使っていました。
前述の`+`関数のような、Juliaの標準的な関数と演算子はすべて、多くのメソッドを持ち、引数の型と個数のさまざまな組み合わせに対して動作が定義されています。

関数を定義するときには、必要に応じて、[複合型](@ref)に関するセクションで紹介した`::`型注釈演算子を使って、適用可能なパラメータの型を、制限することができます。


```jldoctest fofxy
julia> f(x::Float64, y::Float64) = 2x + y
f (generic function with 1 method)
```

```@raw html
<!--
This function definition applies only to calls where `x` and `y` are both values of type
[`Float64`](@ref):
-->
```
この関数の定義は、`x`と`y`が共に[`Float64`](@ref)型の値のときだけ、適用されます。


```jldoctest fofxy
julia> f(2.0, 3.0)
7.0
```

```@raw html
<!--
Applying it to any other types of arguments will result in a [`MethodError`](@ref):
-->
```
この関数定義を他の型の引数に適用すると、次のような[`MethodError`](@ref)が生じます。


```jldoctest fofxy
julia> f(2.0, 3)
ERROR: MethodError: no method matching f(::Float64, ::Int64)
Closest candidates are:
  f(::Float64, !Matched::Float64) at none:1

julia> f(Float32(2.0), 3.0)
ERROR: MethodError: no method matching f(::Float32, ::Float64)
Closest candidates are:
  f(!Matched::Float64, ::Float64) at none:1

julia> f(2.0, "3.0")
ERROR: MethodError: no method matching f(::Float64, ::String)
Closest candidates are:
  f(::Float64, !Matched::Float64) at none:1

julia> f("2.0", "3.0")
ERROR: MethodError: no method matching f(::String, ::String)
```

```@raw html
<!--
As you can see, the arguments must be precisely of type [`Float64`](@ref). Other numeric
types, such as integers or 32-bit floating-point values, are not automatically converted
to 64-bit floating-point, nor are strings parsed as numbers. Because `Float64` is a concrete
type and concrete types cannot be subclassed in Julia, such a definition can only be applied
to arguments that are exactly of type `Float64`. It may often be useful, however, to write
more general methods where the declared parameter types are abstract:
-->
```
ご覧のように、引数は正確に[`Float64`](@ref)型でなければなりません。
他の整数や32ビット浮動小数点数などの数値型では、自動的に64ビット浮動小数点数に変換されず、文字列も数字として解析されません。
`Float64`は具象型で、Juliaでは、7具象型はサブクラス化できないので、このような定義は、型が正確に`Float64`の引数のみに適用可能です。
しかし、宣言する引数の型が抽象的な、汎化メソッドを書くと、けっこう役に立つかもしれません。


```jldoctest fofxy
julia> f(x::Number, y::Number) = 2x - y
f (generic function with 2 methods)

julia> f(2.0, 3)
1.0
```

```@raw html
<!--
This method definition applies to any pair of arguments that are instances of [`Number`](@ref).
They need not be of the same type, so long as they are each numeric values. The problem of
handling disparate numeric types is delegated to the arithmetic operations in the
expression `2x - y`.
-->
```

このメソッド定義は[`Number`](@ref)のインスタンスである任意の引数のペアに適用されます。
それらはそれぞれ数値である限り、同じ型である必要はありません。
異なる数値型を処理する問題は、式`2x - y`の算術演算に委譲されます。


```@raw html
<!--
To define a function with multiple methods, one simply defines the function multiple times, with
different numbers and types of arguments. The first method definition for a function creates the
function object, and subsequent method definitions add new methods to the existing function object.
The most specific method definition matching the number and types of the arguments will be executed
when the function is applied. Thus, the two method definitions above, taken together, define the
behavior for `f` over all pairs of instances of the abstract type `Number` -- but with a different
behavior specific to pairs of [`Float64`](@ref) values. If one of the arguments is a 64-bit
float but the other one is not, then the `f(Float64,Float64)` method cannot be called and
the more general `f(Number,Number)` method must be used:
-->
```

複数のメソッドを持つ関数を定義するには、単に関数の定義を、数と型の異なる引数に対して、複数回行うだけです。
最初のメソッド定義では、関数オブジェクトが生成し、次回以降のメソッド定義では、既存の関数オブジェクトに新しいメソッドが追加されます。
関数の適用時には、引数の数と型が一致している最も特化したメソッド定義が実行されます。
このように、上記の2つのメソッド定義が一緒になって、抽象型`Number`のインスタンスの組すべてに対する`f`の挙動を定義しますが、
共に[`Float64`](@ref)の値の組の場合は挙動が異なります。
引数の一方が64ビット浮動小数点数で、他方が違う場合、この`f(Float64,Float64)`メソッドは呼び出し不可能で、より一般的な`f(Number,Number)`メソッドを使う必要があります。


```jldoctest fofxy
julia> f(2.0, 3.0)
7.0

julia> f(2, 3.0)
1.0

julia> f(2.0, 3)
1.0

julia> f(2, 3)
1
```

```@raw html
<!--
The `2x + y` definition is only used in the first case, while the `2x - y` definition is used
in the others. No automatic casting or conversion of function arguments is ever performed: all
conversion in Julia is non-magical and completely explicit. [Conversion and Promotion](@ref conversion-and-promotion),
however, shows how clever application of sufficiently advanced technology can be indistinguishable
from magic. [^Clarke61]

For non-numeric values, and for fewer or more than two arguments, the function `f` remains undefined,
and applying it will still result in a [`MethodError`](@ref):
-->
```

`2x + y`という定義は、最初の場合にだけ使われていますが、`2x - y`という定義が、その他では使われています。
関数の引数の自動キャストや変換は一度も実行されません。
Juliaでは、すべての変換は魔法ではなく完全に明示的です。
しかし、[変換と昇格]（@ ref conversion-and-promotion）では、十分に高度な技術をうまく使うと、魔法と区別できなくなることを示しています。[^ Clarke61]

数値以外の値や引数の数が２より大きかったり小さかったりする場合、関数`f`は未定義のままであり、そのまま適用すると次のように[`MethodError`](@ref)が生じます。



```jldoctest fofxy
julia> f("foo", 3)
ERROR: MethodError: no method matching f(::String, ::Int64)
Closest candidates are:
  f(!Matched::Number, ::Number) at none:1

julia> f()
ERROR: MethodError: no method matching f()
Closest candidates are:
  f(!Matched::Float64, !Matched::Float64) at none:1
  f(!Matched::Number, !Matched::Number) at none:1
```

```@raw html
<!--
You can easily see which methods exist for a function by entering the function object itself in
an interactive session:
-->
```

対話セッションで関数オブジェクト自体を入力すると、関数にどんなメソッドが存在するかを簡単に確認できます。


```jldoctest fofxy
julia> f
f (generic function with 2 methods)
```

```@raw html
<!--
This output tells us that `f` is a function object with two methods. To find out what the signatures
of those methods are, use the [`methods`](@ref) function:
-->
```
この出力は、`f`が2つのメソッドを持つ関数オブジェクトであることを示しています。
これらのメソッドのシグネチャを調べるには、[`methods()`](@ref)関数を使用します。


```julia-repl
julia> methods(f)
# 2 methods for generic function "f":
[1] f(x::Float64, y::Float64) in Main at none:1
[2] f(x::Number, y::Number) in Main at none:1
```

```@raw html
<!--
which shows that `f` has two methods, one taking two `Float64` arguments and one taking arguments
of type `Number`. It also indicates the file and line number where the methods were defined: because
these methods were defined at the REPL, we get the apparent line number `none:1`.

In the absence of a type declaration with `::`, the type of a method parameter is `Any` by default,
meaning that it is unconstrained since all values in Julia are instances of the abstract type
`Any`. Thus, we can define a catch-all method for `f` like so:
-->
```

すると、`f`には二つのメソッドがあり、一方は2つの`Float64`の引数を取り、他方は型が`Number`の引数を取ることが表示されます。
また、メソッドの定義されたファイルと行番号も表示されます。
これらのメソッドはREPLで定義されているため、見かけの行番号`none:1`が表示されます。

`::`による型宣言がない場合、メソッドの引数の型はデフォルトでは`Any`です。
これは、Juliaのすべての値が抽象型`Any`のインスタンスなので、制約がないことを意味しています 。
したがって、`f`を全捕捉するメソッドを以下のように定義することができます。


```jldoctest fofxy
julia> f(x,y) = println("Whoa there, Nelly.")
f (generic function with 3 methods)

julia> f("foo", 1)
Whoa there, Nelly.
```

```@raw html
<!--
This catch-all is less specific than any other possible method definition for a pair of parameter
values, so it will only be called on pairs of arguments to which no other method definition applies.

Although it seems a simple concept, multiple dispatch on the types of values is perhaps the single
most powerful and central feature of the Julia language. Core operations typically have dozens
of methods:
-->
```

この全捕捉は、引数の組に対して他のどのメソッド定義よりも特化していないため、他のメソッド定義が適用されない引数の組に対してのみ呼び出されます。

単純な考え方のように見えますが、値型に対する多重ディスパッチは、おそらくJulia言語の最も強力で中心的な単一の機能です。中核の演算には通常数十種類のメソッドがあります。


```julia-repl
julia> methods(+)
# 180 methods for generic function "+":
[1] +(x::Bool, z::Complex{Bool}) in Base at complex.jl:227
[2] +(x::Bool, y::Bool) in Base at bool.jl:89
[3] +(x::Bool) in Base at bool.jl:86
[4] +(x::Bool, y::T) where T<:AbstractFloat in Base at bool.jl:96
[5] +(x::Bool, z::Complex) in Base at complex.jl:234
[6] +(a::Float16, b::Float16) in Base at float.jl:373
[7] +(x::Float32, y::Float32) in Base at float.jl:375
[8] +(x::Float64, y::Float64) in Base at float.jl:376
[9] +(z::Complex{Bool}, x::Bool) in Base at complex.jl:228
[10] +(z::Complex{Bool}, x::Real) in Base at complex.jl:242
[11] +(x::Char, y::Integer) in Base at char.jl:40
[12] +(c::BigInt, x::BigFloat) in Base.MPFR at mpfr.jl:307
[13] +(a::BigInt, b::BigInt, c::BigInt, d::BigInt, e::BigInt) in Base.GMP at gmp.jl:392
[14] +(a::BigInt, b::BigInt, c::BigInt, d::BigInt) in Base.GMP at gmp.jl:391
[15] +(a::BigInt, b::BigInt, c::BigInt) in Base.GMP at gmp.jl:390
[16] +(x::BigInt, y::BigInt) in Base.GMP at gmp.jl:361
[17] +(x::BigInt, c::Union{UInt16, UInt32, UInt64, UInt8}) in Base.GMP at gmp.jl:398
...
[180] +(a, b, c, xs...) in Base at operators.jl:424
```

```@raw html
<!--
Multiple dispatch together with the flexible parametric type system give Julia its ability to
abstractly express high-level algorithms decoupled from implementation details, yet generate efficient,
specialized code to handle each case at run time.
-->
```

多重ディスパッチと柔軟なパラメータ化可能な型システムによって、高水準のアルゴリズムを抽象的に表現し、実装の詳細から切り離し、実行時に各条件に特化して処理する効率的なコードを生成することが、Juliaでは可能になったのです。



`[](## [Method Ambiguities](@id man-ambiguities))
## [メソッドの曖昧さ](@id man-ambiguities)

```@raw html
<!--
It is possible to define a set of function methods such that there is no unique most specific
method applicable to some combinations of arguments:
-->
```
複数の関数メソッドに対する定義によっては、ある種の引数の組み合わせに対しては、
最も特化するメソッドが一意に定まらないこともありえます。

```jldoctest gofxy
julia> g(x::Float64, y) = 2x + y
g (generic function with 1 method)

julia> g(x, y::Float64) = x + 2y
g (generic function with 2 methods)

julia> g(2.0, 3)
7.0

julia> g(2, 3.0)
8.0

julia> g(2.0, 3.0)
ERROR: MethodError: g(::Float64, ::Float64) is ambiguous. Candidates:
  g(x, y::Float64) in Main at none:1
  g(x::Float64, y) in Main at none:1
Possible fix, define
  g(::Float64, ::Float64)
```

```@raw html
<!--
Here the call `g(2.0, 3.0)` could be handled by either the `g(Float64, Any)` or the `g(Any, Float64)`
method, and neither is more specific than the other. In such cases, Julia raises a [`MethodError`](@ref)
rather than arbitrarily picking a method. You can avoid method ambiguities by specifying an appropriate
method for the intersection case:
-->
```

ここでの関数呼び出し`g(2.0, 3.0)`は、`g(Float64, Any)`と`g(Any, Float64)` のメソッドによって処理可能ですが、どちらがより特化しているかは決められません。
このような場合、Juliaは勝手にメソッドを選択せずに、[`MethodError`](@ref)を発生します。 
共通する場合に特化したメソッドを指定することで、メソッドの曖昧さをなくすことができます。


```jldoctest gofxy
julia> g(x::Float64, y::Float64) = 2x + 2y
g (generic function with 3 methods)

julia> g(2.0, 3)
7.0

julia> g(2, 3.0)
8.0

julia> g(2.0, 3.0)
10.0
```

```@raw html
<!--
It is recommended that the disambiguating method be defined first, since otherwise the ambiguity
exists, if transiently, until the more specific method is defined.

In more complex cases, resolving method ambiguities involves a certain
element of design; this topic is explored further [below](@ref man-method-design-ambiguities).
-->
```

曖昧さのないメソッドは最初に定義することをお勧めします。でなければ、一時的にせよ、より特化したメソッドが定義されるまで、曖昧さが残るからです。

より複雑なケースでは、メソッドの曖昧さを解決するにはある種の設計要素が必要になります。この話題は[以下で]（@ ref man-method-design-ambiguities）さらに詳しく解説しています。



`[](## Parametric Methods)
## パラメータメソッド


```@raw html
<!--
Method definitions can optionally have type parameters qualifying the signature:
-->
```

メソッド定義では、必要に応じて型パラメータを使い、シグネチャを細かく指定することができます。


```jldoctest same_typefunc
julia> same_type(x::T, y::T) where {T} = true
same_type (generic function with 1 method)

julia> same_type(x,y) = false
same_type (generic function with 2 methods)
```

```@raw html
<!--
The first method applies whenever both arguments are of the same concrete type, regardless of
what type that is, while the second method acts as a catch-all, covering all other cases. Thus,
overall, this defines a boolean function that checks whether its two arguments are of the same
type:
-->
```

第1のメソッドは、両方の引数が同じ具象型であれば、どんな型であっても適用できます。
第2のメソッドは他のすべての場合に対する全捕捉として作用します。
結局これは、2つの引数が同じ型であるかどうかを検査するブール関数を定義しています。


```jldoctest same_typefunc
julia> same_type(1, 2)
true

julia> same_type(1, 2.0)
false

julia> same_type(1.0, 2.0)
true

julia> same_type("foo", 2.0)
false

julia> same_type("foo", "bar")
true

julia> same_type(Int32(1), Int64(2))
false
```

```@raw html
<!--
Such definitions correspond to methods whose type signatures are `UnionAll` types
(see [UnionAll Types](@ref)).

This kind of definition of function behavior by dispatch is quite common -- idiomatic, even --
in Julia. Method type parameters are not restricted to being used as the types of arguments:
they can be used anywhere a value would be in the signature of the function or body of the function.
Here's an example where the method type parameter `T` is used as the type parameter to the parametric
type `Vector{T}` in the method signature:
-->
```

このような定義はシグネチャが`UnionAll`型であるメソッドに対応します([全合併型](@ref) 参照)。

このようにディスパッチを使って関数の動作を定義することは、Juliaでは、まったく普通で、慣用的でさえあります。
メソッドの型パラメータは、引数の型に対する利用に制限されているわけではなく、
関数本体や関数のシグネチャなど、値がある場所であればどこでも利用できます。
メソッドの型パラメータ`T`が、メソッドのシグネチャの中で、パラメータ型`Vector{T}`に対する型パラメータとして利用される例を示します。


```jldoctest
julia> myappend(v::Vector{T}, x::T) where {T} = [v..., x]
myappend (generic function with 1 method)

julia> myappend([1,2,3],4)
4-element Array{Int64,1}:
 1
 2
 3
 4

julia> myappend([1,2,3],2.5)
ERROR: MethodError: no method matching myappend(::Array{Int64,1}, ::Float64)
Closest candidates are:
  myappend(::Array{T,1}, !Matched::T) where T at none:1

julia> myappend([1.0,2.0,3.0],4.0)
4-element Array{Float64,1}:
 1.0
 2.0
 3.0
 4.0

julia> myappend([1.0,2.0,3.0],4)
ERROR: MethodError: no method matching myappend(::Array{Float64,1}, ::Int64)
Closest candidates are:
  myappend(::Array{T,1}, !Matched::T) where T at none:1
```

```@raw html
<!--
As you can see, the type of the appended element must match the element type of the vector it
is appended to, or else a [`MethodError`](@ref) is raised. In the following example, the method type parameter
`T` is used as the return value:
-->
```

ご覧のように、追加する要素の型は、追加されるベクトルの要素の型と一致しなければなりません。でなければ、[`MethodError`](@ref) が生じます。
次の例では、メソッドの型パラメータ `T`が戻り値として使用されています。



```jldoctest
julia> mytypeof(x::T) where {T} = T
mytypeof (generic function with 1 method)

julia> mytypeof(1)
Int64

julia> mytypeof(1.0)
Float64
```

```@raw html
<!--
Just as you can put subtype constraints on type parameters in type declarations (see [Parametric Types](@ref)),
you can also constrain type parameters of methods:
-->
```

型宣言（[パラメータ型](@ref)型を参照）に出てくる型パラメータにサブタイプ制約をかけることができるのと同様に、メソッドの型パラメータにも制約をかけることができます。


```jldoctest
julia> same_type_numeric(x::T, y::T) where {T<:Number} = true
same_type_numeric (generic function with 1 method)

julia> same_type_numeric(x::Number, y::Number) = false
same_type_numeric (generic function with 2 methods)

julia> same_type_numeric(1, 2)
true

julia> same_type_numeric(1, 2.0)
false

julia> same_type_numeric(1.0, 2.0)
true

julia> same_type_numeric("foo", 2.0)
ERROR: MethodError: no method matching same_type_numeric(::String, ::Float64)
Closest candidates are:
  same_type_numeric(!Matched::T<:Number, ::T<:Number) where T<:Number at none:1
  same_type_numeric(!Matched::Number, ::Number) at none:1

julia> same_type_numeric("foo", "bar")
ERROR: MethodError: no method matching same_type_numeric(::String, ::String)

julia> same_type_numeric(Int32(1), Int64(2))
false
```

```@raw html
<!--
The `same_type_numeric` function behaves much like the `same_type` function defined above, but
is only defined for pairs of numbers.

Parametric methods allow the same syntax as `where` expressions used to write types
(see [UnionAll Types](@ref)).
If there is only a single parameter, the enclosing curly braces (in `where {T}`) can be omitted,
but are often preferred for clarity.
Multiple parameters can be separated with commas, e.g. `where {T, S<:Real}`, or written using
nested `where`, e.g. `where S<:Real where T`.
-->
```

この`same_type_numeric`関数は、以前に定義した関数`same_type`と非常によく似た挙動をしますが、数値の組に対してのみ定義されています。

パラメータメソッドでは、型の生成に使われる`where`式と同じ構文を使って型を書くことが可能です（[全合併型](@ref)を参照）。
パラメータが1つしかない場合は、中括弧（`where {T}`）を省略することができますが、わかりやくするために、好んでよく使われます。
複数のパラメータは、カンマで区切ったり(例`where {T, S<:Real}`)、`where`をネストしたり（例`where S<:Real where T`）して記述します。


```@raw html
<!--
Redefining Methods
------------------
-->
```

メソッドの再定義
------------------


```@raw html
<!--
When redefining a method or adding new methods,
it is important to realize that these changes don't take effect immediately.
This is key to Julia's ability to statically infer and compile code to run fast,
without the usual JIT tricks and overhead.
Indeed, any new method definition won't be visible to the current runtime environment,
including Tasks and Threads (and any previously defined `@generated` functions).
Let's start with an example to see what this means:
-->
```
メソッドを再定義したり、新しく追加したりする時は、変更がすぐには反映されない、ということを理解しておくことが重要です。
これは、静的にコードを推論・コンパイルし、よくあるJITトリックやオーバーヘッドなしで実行速度が速い、というJuliaの能力の鍵となります。
実際、新しいメソッド定義は、実行時環境からは、タスクおよびスレッド（そして以前に定義された`@generated`関数）を含め、まったく見えません。
これが何を意味するかを例をあげて見てみましょう。


```julia-repl
julia> function tryeval()
           @eval newfun() = 1
           newfun()
       end
tryeval (generic function with 1 method)

julia> tryeval()
ERROR: MethodError: no method matching newfun()
The applicable method may be too new: running in world age xxxx1, while current world is xxxx2.
Closest candidates are:
  newfun() at none:1 (method too new to be called from this world context.)
 in tryeval() at none:1
 ...

julia> newfun()
1
```

```@raw html
<!--
In this example, observe that the new definition for `newfun` has been created,
but can't be immediately called.
The new global is immediately visible to the `tryeval` function,
so you could write `return newfun` (without parentheses).
But neither you, nor any of your callers, nor the functions they call, or etc.
can call this new method definition!
-->
```

この例では、`newfun`の新しい定義は作成されましたが、すぐには呼び出せないことに注意してください。
この新しいグローバルな関数はすぐに`tryeval`関数からは見えるので、`return newfun`と(括弧を付けずに）書くことができます。
しかし、あなたも、呼ぶ関数からも、呼ばれる関数からも、この新しいメソッド定義を呼び出すことはできません！




```@raw html
<!--
But there's an exception: future calls to `newfun` *from the REPL* work as expected,
being able to both see and call the new definition of `newfun`.

However, future calls to `tryeval` will continue to see the definition of `newfun` as it was
*at the previous statement at the REPL*, and thus before that call to `tryeval`.

You may want to try this for yourself to see how it works.
-->
```
しかし、例外はあります。**REPLからの** `newfun`への今後行うの呼び出しは、期待通りに動作し、`newfun`の新しい定義を参照して呼び出すことができます。

しかし、`tryeval`への今後行う呼び出しは、**REPLで前回行った** `newfun`の定義を参照し続けます。これは前回の`tryeval`の呼び出しも同じです。

これを自分で試して、どのように動作するかを見てみたいと思うかもしれません。


```@raw html
<!--
The implementation of this behavior is a "world age counter".
This monotonically increasing value tracks each method definition operation.
This allows describing "the set of method definitions visible to a given runtime environment"
as a single number, or "world age".
It also allows comparing the methods available in two worlds just by comparing their ordinal value.
In the example above, we see that the "current world" (in which the method `newfun` exists),
is one greater than the task-local "runtime world" that was fixed when the execution of `tryeval` started.

Sometimes it is necessary to get around this (for example, if you are implementing the above REPL).
Fortunately, there is an easy solution: call the function using [`Base.invokelatest`](@ref):
-->
```

この挙動の実装は「世界の年齢のカウンター」です。
この単調に増加する値は、各メソッド定義の操作を追跡します。
これにより、「実行時環境から見えるメソッド定義の集合」を、単一の数値「世界の年齢」として記述することができます。
また、年齢を比較するだけで、2つの世界で利用できるメソッドを比較することもできます。
上記の例では、メソッド`newfun()`が存在する「現在の世界」が、`tryeval`開始時に設定されたタスクローカルの「実行時の世界」よりも１つ大きいことがわかります。

場合によってはこれを回避する必要があります（たとえば、上記のREPLを実装している場合など）。
幸いにも、簡単な解決策があって、[`Base.invokelatest`](@ref)を使って関数を呼び出せば、いいのです。


```jldoctest
julia> function tryeval2()
           @eval newfun2() = 2
           Base.invokelatest(newfun2)
       end
tryeval2 (generic function with 1 method)

julia> tryeval2()
2
```

```@raw html
<!--
Finally, let's take a look at some more complex examples where this rule comes into play.
Define a function `f(x)`, which initially has one method:
-->
```
最後に、このルールが適用されるより複雑な例を、いくつか見てみましょう。最初は1つのメソッドを持つ関数`f(x)`を定義します。


```jldoctest redefinemethod
julia> f(x) = "original definition"
f (generic function with 1 method)
```

```@raw html
<!--
Start some other operations that use `f(x)`:
-->
```
他の`f(x)`を使う操作を開始します。

```jldoctest redefinemethod
julia> g(x) = f(x)
g (generic function with 1 method)

julia> t = @async f(wait()); yield();
```

```@raw html
<!--
Now we add some new methods to `f(x)`:
-->
```
ここでいくつかの新しいメソッドを`f(x)`に追加します。

```jldoctest redefinemethod
julia> f(x::Int) = "definition for Int"
f (generic function with 2 methods)

julia> f(x::Type{Int}) = "definition for Type{Int}"
f (generic function with 3 methods)
```

```@raw html
<!--
Compare how these results differ:
-->
```
これらの結果がどのように異なるかを比較します。

```jldoctest redefinemethod
julia> f(1)
"definition for Int"

julia> g(1)
"definition for Int"

julia> fetch(schedule(t, 1))
"original definition"

julia> t = @async f(wait()); yield();

julia> fetch(schedule(t, 1))
"definition for Int"
```

`[](## Design Patterns with Parametric Methods)
## パラメータメソッドのデザインパターン


```@raw html
<!--
While complex dispatch logic is not required for performance or usability,
sometimes it can be the best way to express some algorithm.
Here are a few common design patterns that come up sometimes when using dispatch in this way.
-->
```
複雑なディスパッチの論理は、パフォーマンスや利便性のためには、必要ありませんが、
ある種のアルゴリズムを表現するための最善の方法となることがあります。
そのような用途によく使われるデザインパターンを挙げていきます。


`[](### Extracting the type parameter from a super-type)
### スーパータイプからの型パラメータの抽出


```@raw html
<!--
Here is the correct code template for returning the element-type `T`
of any arbitrary subtype of `AbstractArray`:
-->
```
ここに挙げるのは、`AbstractArray`の任意のサブタイプの要素の型`T`を返す正しいコードのテンプレートです。


```julia
abstract type AbstractArray{T, N} end
eltype(::Type{<:AbstractArray{T}}) where {T} = T
```

```@raw html
<!--
using so-called triangular dispatch.  Note that if `T` is a `UnionAll`
type, as e.g. `eltype(Array{T} where T <: Integer)`, then `Any` is
returned (as does the the version of `eltype` in `Base`).

Another way, which used to be the only correct way before the advent of
triangular dispatch in Julia v0.6, is:
-->
```
いわゆる三角ディスパッチを使います。
`T`が例えば`eltype(Array{T} where T <: Integer)`のような`全合併型`のときには、
`Any`が返される点に注意してください。（`Base`の中の`eltype`の変形版のような働きをします）

別の方法で、Julia v0.6で三角ディスパッチが使えるようになる以前の唯一正しい方法は、


```julia
abstract type AbstractArray{T, N} end
eltype(::Type{AbstractArray}) = Any
eltype(::Type{AbstractArray{T}}) where {T} = T
eltype(::Type{AbstractArray{T, N}}) where {T, N} = T
eltype(::Type{A}) where {A<:AbstractArray} = eltype(supertype(A))
```

```@raw html
<!--
Another possibility is the following, which could useful to adapt
to cases where the parameter `T` would need to be matched more
narrowly:
-->
```
下記のような方法も可能で、パラメータ`T`の一致する範囲をもっと狭める必要がある時に
役立ちます。

```julia
eltype(::Type{AbstractArray{T, N} where {T<:S, N<:M}}) where {M, S} = Any
eltype(::Type{AbstractArray{T, N} where {T<:S}}) where {N, S} = Any
eltype(::Type{AbstractArray{T, N} where {N<:M}}) where {M, T} = T
eltype(::Type{AbstractArray{T, N}}) where {T, N} = T
eltype(::Type{A}) where {A <: AbstractArray} = eltype(supertype(A))
```


```@raw html
<!--
One common mistake is to try and get the element-type by using introspection:
-->
```
よくある間違いとして、イントロスペクション（実行時にオブジェクトの情報を参照・変更すること）を使って要素の型を
得ることです。


```julia
eltype_wrong(::Type{A}) where {A<:AbstractArray} = A.parameters[1]
```

```@raw html
<!--
However, it is not hard to construct cases where this will fail:
-->
```
しかし、これが失敗する場合を実現するのは難しくありません。


```julia
struct BitVector <: AbstractArray{Bool, 1}; end
```

```@raw html
<!--
Here we have created a type `BitVector` which has no parameters,
but where the element-type is still fully specified, with `T` equal to `Bool`!
-->
```

ここでは、パラメータを持たない`BitVector`を生成しましたが、
`T`を`Bool`と同等にすると、要素の型を完全に指定することができます。


`[](### Building a similar type with a different type parameter)
### 別の型パラメータに似た型の構成

```@raw html
<!--
When building generic code, there is often a need for constructing a similar
object with some change made to the layout of the type, also
necessitating a change of the type parameters.
For instance, you might have some sort of abstract array with an arbitrary element type
and want to write your computation on it with a specific element type.
We must implement a method for each `AbstractArray{T}` subtype that describes how to compute this type transform.
There is no general transform of one subtype into another subtype with a different parameter.
(Quick review: do you see why this is?)
-->
```
汎化的なコードを作る際に、型の構成を少し変えたオブジェクトを構成する必要が生じて、
型パラメータも変更する必要が出てくる、といったことがよく起こります。
例えば、要素の型が任意のある種の抽象型の配列を作り、これを使って特定の要素の型に対する計算を書きたいとします。
この時実装すべき`AbstractArray{T}`のサブタイプに対するメソッドには、型の変換をどのように計算するか記述しなければなりません。
パラメータの違うサブタイプどうしに対する一般的な変換はありません。
(おさらい：どうしてだかわかりますか)


```@raw html
<!--
The subtypes of `AbstractArray` typically implement two methods to
achieve this:
A method to convert the input array to a subtype of a specific `AbstractArray{T, N}` abstract type;
and a method to make a new uninitialized array with a specific element type.
Sample implementations of these can be found in Julia Base.
Here is a basic example usage of them, guaranteeing that `input` and
`output` are of the same type:
-->
```

これを達成するには、通常`AbstractArray`のサブタイプに２つのメソッドを実装します。
入力の配列を特定の抽象型`AbstractArray{T, N}` のサブタイプに変換するメソッドと、
特定の要素型の初期化されていない配列を作るメソッドです。
例となる実装が、JuliaのBaseライブラリにあります。
これを使った、`input`と`output`が同じ型になることを保証する基本的な例を挙げます。


```julia
input = convert(AbstractArray{Eltype}, input)
output = similar(input, Eltype)
```

```@raw html
<!--
As an extension of this, in cases where the algorithm needs a copy of
the input array,
[`convert`](@ref) is insufficient as the return value may alias the original input.
Combining [`similar`](@ref) (to make the output array) and [`copyto!`](@ref) (to fill it with the input data)
is a generic way to express the requirement for a mutable copy of the input argument:
-->
```
この拡張に、入力の配列のコピーが必要なアルゴリズムを使う場合、
もとの入力のエイリアスを戻り値として使うには、[`convert`](@ref)だけでは不十分です。
 [`similar`](@ref) (出力用の配列を生成する)と[`copyto!`](@ref)（それを入力データで埋める）を組み合わせるのが、
 入力の引数の可変なコピーを必要がとする場合の汎化的な表現法です。



```julia
copy_with_eltype(input, Eltype) = copyto!(similar(input, Eltype), input)
```

`[](### Iterated dispatch)
### ディスパッチの反復

```@raw html
<!--
In order to dispatch a multi-level parametric argument list,
often it is best to separate each level of dispatch into distinct functions.
This may sound similar in approach to single-dispatch, but as we shall see below, it is still more flexible.

For example, trying to dispatch on the element-type of an array will often run into ambiguous situations.
Instead, commonly code will dispatch first on the container type,
then recurse down to a more specific method based on eltype.
In most cases, the algorithms lend themselves conveniently to this hierarchical approach,
while in other cases, this rigor must be resolved manually.
This dispatching branching can be observed, for example, in the logic to sum two matrices:
-->
```
様々なレベルのパラメータ引数のリストをディスパッチするには、
それぞれのディスパッチのレベルを別の関数に分離するのが最善策で有ることがよくあります。
これは、単一のディスパッチ方式と似ているように聞こえるかもしれませんが、以下に示すように、より柔軟です。

例えば、配列の要素の型によるディスパッチを行なおうとすると、曖昧な状態に陥ることがよくあります。
この代わりに、まずコンテナの型によるディスパッチを行い、次に再帰的に要素型に対してメソッドを特化させます。
大抵の場合、アルゴリズムはこの階層的な方式を便利に利用するでしょうが、手動で厳密に解決しなければならないこともあるでしょう。
こうしたディスパッチの分岐は、２つの行列の和をおこなう場合などで見られます。


```julia
# First dispatch selects the map algorithm for element-wise summation.
+(a::Matrix, b::Matrix) = map(+, a, b)
# Then dispatch handles each element and selects the appropriate
# common element type for the computation.
+(a, b) = +(promote(a, b)...)
# Once the elements have the same type, they can be added.
# For example, via primitive operations exposed by the processor.
+(a::Float64, b::Float64) = Core.add(a, b)
```

`[](### Trait-based dispatch)
### トレイトに基づくディスパッチ


```@raw html
<!--
A natural extension to the iterated dispatch above is to add a layer to
method selection that allows to dispatch on sets of types which are
independent from the sets defined by the type hierarchy.
We could construct such a set by writing out a `Union` of the types in question,
but then this set would not be extensible as `Union`-types cannot be
altered after creation.
However, such an extensible set can be programmed with a design pattern
often referred to as a
["Holy-trait"](https://github.com/JuliaLang/julia/issues/2345#issuecomment-54537633).
-->
```
上記の反復するディスパッチの自然な拡張として、型の階層が定義された集合とは無関係に型の集合に対するディスパッチを行う層を追加することがあります。
そのような集合を構築するには、問題としている型に対する`合併型`を書けばいいですが、
`合併型`は生成後に変更できないので、この集合は拡張できません。
しかし、拡張可能な集合は、["Holy-trait"](https://github.com/JuliaLang/julia/issues/2345#issuecomment-54537633)とよくよばれるデザインパターンを使ってプログラム可能です。






```@raw html
<!--
This pattern is implemented by defining a generic function which
computes a different singleton value (or type) for each trait-set to which the
function arguments may belong to.  If this function is pure there is
no impact on performance compared to normal dispatch.

The example in the previous section glossed over the implementation details of
[`map`](@ref) and [`promote`](@ref), which both operate in terms of these traits.
When iterating over a matrix, such as in the implementation of `map`,
one important question is what order to use to traverse the data.
When `AbstractArray` subtypes implement the [`Base.IndexStyle`](@ref) trait,
other functions such as `map` can dispatch on this information to pick
the best algorithm (see [Abstract Array Interface](@ref man-interface-array)).
This means that each subtype does not need to implement a custom version of `map`,
since the generic definitions + trait classes will enable the system to select the fastest version.
Here a toy implementation of `map` illustrating the trait-based dispatch:
-->
```
このパターンでは、汎化関数の定義を実装します。
この汎化関数は、各引数が所属可能なトレイトの集合ごとに異なるシングルトンの値(または型)を算出するものです。
この関数が純粋である場合、通常のディスパッチと比較してパフォーマンス上全く影響はありません。

前のセクションでは、実装の詳細をごまかしていた[`map`](@ref)や[`promote`](@ref)は、共にこのトレイトを使って動作しています。

`map`の実装などで、行列に対して反復を行う場合に、重要な問題は、データに対してどういう順序を使って渡り歩くかということです。
`AbstractArray`のサブタイプを[`Base.IndexStyle`](@ref)トレイトを使って実装する時、
他の`map`などの関数は、この情報に基づいて最善のアルゴリズムを選ぶようにディスパッチを行うことができます。
（[抽象配列インターフェイス](@ref man-interface-array)参照）
これは、サブタイプそれぞれが独自の`map`の変種の実装を行う必要はないことを意味します。
というのも、汎化的な定義の`+`トレイトのクラスによって、システムが最速のバージョンを選択できるからです。
トレイトに基づくディスパッチの解説用に`map`の実装のおもちゃ版を示します。



```julia
map(f, a::AbstractArray, b::AbstractArray) = map(Base.IndexStyle(a, b), f, a, b)
# generic implementation:
map(::Base.IndexCartesian, f, a::AbstractArray, b::AbstractArray) = ...
# linear-indexing implementation (faster)
map(::Base.IndexLinear, f, a::AbstractArray, b::AbstractArray) = ...
```

```@raw html
<!--
This trait-based approach is also present in the [`promote`](@ref)
mechanism employed by the scalar `+`.
It uses [`promote_type`](@ref), which returns the optimal common type to
compute the operation given the two types of the operands.
This makes it possible to reduce the problem of implementing every function for every pair of possible type arguments,
to the much smaller problem of implementing a conversion operation from each type to a common type,
plus a table of preferred pair-wise promotion rules.
-->
```

このトレイトに基づく方式は、スカラーの`+`に対する[`promote`](@ref)のしくみにも採用されています。
これに使われる[`promote_type`](@ref)は、２種類の被演算子最適な共通の型を、の与えられた演算から算出して返します。
これによって、すべての関数に対して、引数の取りうる型の組み合わせすべてに対する実装を行うという問題を、
それぞれの型から共通の型への変換を実装し、組み合わせごとの望ましい昇格規則の表を作るという、より小さな問題に縮小できます。



`[](### Output-type computation)
### 出力型の算出

```@raw html
<!--
The discussion of trait-based promotion provides a transition into our next design pattern:
computing the output element type for a matrix operation.

For implementing primitive operations, such as addition,
we use the [`promote_type`](@ref) function to compute the desired output type.
(As before, we saw this at work in the `promote` call in the call to `+`).

For more complex functions on matrices, it may be necessary to compute the expected return
type for a more complex sequence of operations.
This is often performed by the following steps:
-->
```

トレイトに基づく昇格の議論から次のデザインパターンに移行します。
行列演算に対する出力要素の型の算出です。

足し算のような原始的な演算に対しては、[`promote_type`](@ref)関数を使って所望の出力の型を算出します。
（以前に、`+`を呼び出した時に起こる`promote`の呼び出しでこの動作を見ました）

もっと行列に対する関数が複雑な場合は、期待する型の戻り値を算出するために、もっと複雑ない一連の操作が必要かもしれません。
これは次のような段階を経てよく実行されます。




```@raw html
<!--
1. Write a small function `op` that expresses the set of operations performed by the kernel of the algorithm.
2. Compute the element type `R` of the result matrix as `promote_op(op, argument_types...)`,
   where `argument_types` is computed from `eltype` applied to each input array.
3. Build the output matrix as `similar(R, dims)`, where `dims` are the desired dimensions of the output array.
-->
```
1.アルゴリズムの中核で行われる操作の集合を表す小さな関数`op`を書く。
2.計算結果の行列の要素の型`R`を、`promote_op(op, argument_types...)`を使って計算する。
ここで`argument_types`は`eltype`を入力行列それぞれに適用して算出する。
3.`similar(R, dims)`によって出力行列を構成する。`dims`は出力行列の所望の次元とする。


```@raw html
<!--
For a more specific example, a generic square-matrix multiply pseudo-code might look like:
-->
```
更に具体的な例として、以下のような汎化的な正方行列の掛け算の擬似コードは、以下のようになります。


```julia
function matmul(a::AbstractMatrix, b::AbstractMatrix)
    op = (ai, bi) -> ai * bi + ai * bi

    ## this is insufficient because it assumes `one(eltype(a))` is constructable:
    # R = typeof(op(one(eltype(a)), one(eltype(b))))

    ## this fails because it assumes `a[1]` exists and is representative of all elements of the array
    # R = typeof(op(a[1], b[1]))

    ## this is incorrect because it assumes that `+` calls `promote_type`
    ## but this is not true for some types, such as Bool:
    # R = promote_type(ai, bi)

    # this is wrong, since depending on the return value
    # of type-inference is very brittle (as well as not being optimizable):
    # R = Base.return_types(op, (eltype(a), eltype(b)))

    ## but, finally, this works:
    R = promote_op(op, eltype(a), eltype(b))
    ## although sometimes it may give a larger type than desired
    ## it will always give a correct type

    output = similar(b, R, (size(a, 1), size(b, 2)))
    if size(a, 2) > 0
        for j in 1:size(b, 2)
            for i in 1:size(b, 1)
                ## here we don't use `ab = zero(R)`,
                ## since `R` might be `Any` and `zero(Any)` is not defined
                ## we also must declare `ab::R` to make the type of `ab` constant in the loop,
                ## since it is possible that typeof(a * b) != typeof(a * b + a * b) == R
                ab::R = a[i, 1] * b[1, j]
                for k in 2:size(a, 2)
                    ab += a[i, k] * b[k, j]
                end
                output[i, j] = ab
            end
        end
    end
    return output
end
```

`[](### Separate convert and kernel logic)
### 変換とカーネルロジックの分離

```@raw html
<!--
One way to significantly cut down on compile-times and testing complexity is to isolate
the logic for converting to the desired type and the computation.
This lets the compiler specialize and inline the conversion logic independent
from the rest of the body of the larger kernel.

This is a common pattern seen when converting from a larger class of types
to the one specific argument type that is actually supported by the algorithm:
-->
```
コンパイル時間と検査の複雑さを大幅に減少させる方法に、所望の型への変換と計算を分離することがあります。
これにより、変換をインラインで最適化し、のこりの大きなカーネル本体と独立します。


数多くの種類の型から、特定の引数の型でアルゴリズムが実際に対応しているものへと変換する際によく見られるパターンです。

```julia
complexfunction(arg::Int) = ...
complexfunction(arg::Any) = complexfunction(convert(Int, arg))

matmul(a::T, b::T) = ...
matmul(a, b) = matmul(promote(a, b)...)
```

`[](## Parametrically-constrained Varargs methods)
## パラメータ制限つきの可変引数メソッド

```@raw html
<!--
Function parameters can also be used to constrain the number of arguments that may be supplied
to a "varargs" function ([Varargs Functions](@ref)).  The notation `Vararg{T,N}` is used to indicate
such a constraint.  For example:
-->
```

関数のパラメータは、 "varargs"関数([可変引数関数](@ref))が受け取る引数の数を制限するためにも利用できます。
`Vararg{T,N}`という記法は、そういう制約を指定するために使われます。例えば：


```jldoctest
julia> bar(a,b,x::Vararg{Any,2}) = (a,b,x)
bar (generic function with 1 method)

julia> bar(1,2,3)
ERROR: MethodError: no method matching bar(::Int64, ::Int64, ::Int64)
Closest candidates are:
  bar(::Any, ::Any, ::Any, !Matched::Any) at none:1

julia> bar(1,2,3,4)
(1, 2, (3, 4))

julia> bar(1,2,3,4,5)
ERROR: MethodError: no method matching bar(::Int64, ::Int64, ::Int64, ::Int64, ::Int64)
Closest candidates are:
  bar(::Any, ::Any, ::Any, ::Any) at none:1
```

```@raw html
<!--
More usefully, it is possible to constrain varargs methods by a parameter. For example:
-->
```
さらに便利なことに、パラメータで可変引数メソッドを制約することができます。例えば：


```julia
function getindex(A::AbstractArray{T,N}, indices::Vararg{Number,N}) where {T,N}
```

```@raw html
<!--
would be called only when the number of `indices` matches the dimensionality of the array.

When only the type of supplied arguments needs to be constrained `Vararg{T}` can be equivalently
written as `T...`. For instance `f(x::Int...) = x` is a shorthand for `f(x::Vararg{Int}) = x`.
-->
```
`indexes`の数が配列の次元の数と一致する場合にのみ呼び出されます。

受け取る引数の型を`Vararg{T}`に制限したいだけなら、同等な`T...`という書き方も可能です。
例えば、`f(x::Int...) = x`は`f(x::Vararg{Int}) = x`の略記法です。


`[](## Note on Optional and keyword Arguments)
## オプション引数・キーワード引数に関する注記

```@raw html
<!--
As mentioned briefly in [Functions](@ref man-functions), optional arguments are implemented as syntax for multiple
method definitions. For example, this definition:
-->
```
[関数]（@ ref man-functions）で簡単に述べたように、オプション引数は複数のメソッドを定義するための構文として実装されています。
例として、この定義を見てみましょう。



```julia
f(a=1,b=2) = a+2b
```

```@raw html
<!--
translates to the following three methods:
-->
```
これは以下の3つのメソッドに変換されます。


```julia
f(a,b) = a+2b
f(a) = f(a,2)
f() = f(1,2)
```

```@raw html
<!--
This means that calling `f()` is equivalent to calling `f(1,2)`. In this case the result is `5`,
because `f(1,2)` invokes the first method of `f` above. However, this need not always be the case.
If you define a fourth method that is more specialized for integers:
-->
```

これは、`f()`の呼び出しと`f(1,2)`の呼び出しは同等なことを意味します。
この場合、計算結果が`5`となるのは、`f(1,2)`が上記の`f`の１番目のメソッドを呼び出すからです。
しかし、必ずこうする必要はなく、整数に特化した4番目のメソッドを定義した場合は、次のようになります。


```julia
f(a::Int,b::Int) = a-2b
```

```@raw html
<!--
then the result of both `f()` and `f(1,2)` is `-3`. In other words, optional arguments are tied
to a function, not to any specific method of that function. It depends on the types of the optional
arguments which method is invoked. When optional arguments are defined in terms of a global variable,
the type of the optional argument may even change at run-time.

Keyword arguments behave quite differently from ordinary positional arguments. In particular,
they do not participate in method dispatch. Methods are dispatched based only on positional arguments,
with keyword arguments processed after the matching method is identified.
-->
```
`f()`と`f(1,2)`の結果は両方とも`-3`です。
換言すると、オプション引数が紐付けられるのは、関数自体であって、関数の特定のメソッドではありません。
オプション引数の型によって、どのメソッドが呼び出されるかが変わります。
オプション引数がグローバル変数を使って定義されている場合、オプション引数の型は実行時に変更されることさえあります。

キーワード引数は、普通の位置による引数とはまったく異なった挙動になります。
特に、メソッドディスパッチには参加しません。
メソッドは、位置による引数だけに基づいてディスパッチされ、一致するメソッドが特定された後にキーワード引数が処理されます。



`[](## Function-like objects)
## 関数のようなオブジェクト

```@raw html
<!--
Methods are associated with types, so it is possible to make any arbitrary Julia object "callable"
by adding methods to its type. (Such "callable" objects are sometimes called "functors.")

For example, you can define a type that stores the coefficients of a polynomial, but behaves like
a function evaluating the polynomial:
-->
```
メソッドは型に関連付けられているので、その型に対するメソッドを追加することで、任意のJuliaオブジェクトを「呼び出し可能」にすることができます。（このような「呼び出し可能な」オブジェクトは、「ファンクタ」と呼ばれることもあります）

たとえば、係数を格納する多項式の型は定義可能ですが、多項式を評価する関数のような挙動になります。



```jldoctest polynomial
julia> struct Polynomial{R}
           coeffs::Vector{R}
       end

julia> function (p::Polynomial)(x)
           v = p.coeffs[end]
           for i = (length(p.coeffs)-1):-1:1
               v = v*x + p.coeffs[i]
           end
           return v
       end

julia> (p::Polynomial)() = p(5)
```

```@raw html
<!--
Notice that the function is specified by type instead of by name. As with normal functions
there is a terse syntax form. In the function body, `p` will refer to the object that was
called. A `Polynomial` can be used as follows:
-->
```
関数が名前ではなく型によって指定されていることに注意してください。
通常の関数と同様に、簡略型の構文があります。
関数本体で`p`は、呼ばれたオブジェクトを参照しています。`Polynomial`は以下のように使います。


```jldoctest polynomial
julia> p = Polynomial([1,10,100])
Polynomial{Int64}([1, 10, 100])

julia> p(3)
931

julia> p()
2551
```

```@raw html
<!--
This mechanism is also the key to how type constructors and closures (inner functions that refer
to their surrounding environment) work in Julia.
-->
```
このしくみは、型のコンストラクタとクロージャ（周囲の環境を参照する内部関数）がJuliaでどのように作用するかの鍵でもあります。


`[](## Empty generic functions)
## 空の汎化関数


```@raw html
<!--
Occasionally it is useful to introduce a generic function without yet adding methods. This can
be used to separate interface definitions from implementations. It might also be done for the
purpose of documentation or code readability. The syntax for this is an empty `function` block
without a tuple of arguments:
-->
```
汎化関数を、なにもメソッドを追加しないままで導入すると、便利になる時があります。
これは、インタフェースの定義を実装から分離するために利用できます。
また、ドキュメントをつけたり、コードの読みやすくしたりするためにも利用可能です。
この構文は、引数のタプルがない空の`関数`のブロックです。


```julia
function emptyfunc
end
```

`[](## [Method design and the avoidance of ambiguities](@id man-method-design-ambiguities))
## [メソッドの設計と曖昧さの回避](@id man-method-design-ambiguities)

```@raw html
<!--
Julia's method polymorphism is one of its most powerful features, yet
exploiting this power can pose design challenges.  In particular, in
more complex method hierarchies it is not uncommon for
[ambiguities](@ref man-ambiguities) to arise.

Above, it was pointed out that one can resolve ambiguities like
-->
```
Juliaのメソッドの多相性は、最も強力な機能の1つですが、この力を利用した時に、設計上の課題が顕在化する可能性があります。
特に、メソッドの階層がより複雑なときは、[曖昧さ](@ref man-ambiguities) が生じることは珍しくありません。

以前、以下のように曖昧さを解決できると指摘しましたが

```julia
f(x, y::Int) = 1
f(x::Int, y) = 2
```

```@raw html
<!--
by defining a method
-->
```
メソッドの定義によって

```julia
f(x::Int, y::Int) = 3
```

```@raw html
<!--
This is often the right strategy; however, there are circumstances
where following this advice blindly can be counterproductive. In
particular, the more methods a generic function has, the more
possibilities there are for ambiguities. When your method hierarchies
get more complicated than this simple example, it can be worth your
while to think carefully about alternative strategies.

Below we discuss particular challenges and some alternative ways to resolve such issues.
-->
```

これが正しい戦略となることはよくあります。
しかし、この助言に闇雲に従うと、逆効果になる場合があります。
特に、汎化関数のメソッドが多くなるほど、曖昧さが増す可能性が高まります。
この単純な例よりもメソッドの階層が複雑になったときは、別の戦略も注意深く検討する価値があるかもしれません。

以下では、個々の課題と、その問題を解決する別の方法について説明します。


`[](### Tuple and NTuple arguments)
### タプル引数・Nタプル引数

```@raw html
<!--
`Tuple` (and `NTuple`) arguments present special challenges. For example,
-->
```
`Tuple`（および`NTuple`）の引数には、特殊な課題があります。例えば、

```julia
f(x::NTuple{N,Int}) where {N} = 1
f(x::NTuple{N,Float64}) where {N} = 2
```

```@raw html
<!--
are ambiguous because of the possibility that `N == 0`: there are no
elements to determine whether the `Int` or `Float64` variant should be
called. To resolve the ambiguity, one approach is define a method for
the empty tuple:
-->
```
これが曖昧なのは、`N == 0`の可能性があるからです。
`Int`と`Float64`のどちらを呼び出すのか、決め手がありません。
空のタプルに対するメソッドを定義して、この曖昧さを解決するという方法があります。


```julia
f(x::Tuple{}) = 3
```

```@raw html
<!--
Alternatively, for all methods but one you can insist that there is at
least one element in the tuple:
-->
```

また、1つのメソッドを除くすべてのメソッドが、少なくとも1つの要素がタプルにあることを前提とする定義にするという方法もあります。

```julia
f(x::NTuple{N,Int}) where {N} = 1           # this is the fallback
f(x::Tuple{Float64, Vararg{Float64}}) = 2   # this requires at least one Float64
```

`[](### [Orthogonalize your design](@id man-methods-orthogonalize))
### [設計の直交化](@id man-methods-orthogonalize)

```@raw html
<!--
When you might be tempted to dispatch on two or more arguments,
consider whether a "wrapper" function might make for a simpler
design. For example, instead of writing multiple variants:
-->
```
ディスパッチに2つ以上の引数を使いたい場合は、「ラッパー」関数を使うと、設計が単純になるかどうかを検討してください。
たとえば、複数のメソッドを記述する代わりに


```julia
f(x::A, y::A) = ...
f(x::A, y::B) = ...
f(x::B, y::A) = ...
f(x::B, y::B) = ...
```

```@raw html
<!--
you might consider defining
-->
```
このように定義するかもしれません。

```julia
f(x::A, y::A) = ...
f(x, y) = f(g(x), g(y))
```

```@raw html
<!--
where `g` converts the argument to type `A`. This is a very specific
example of the more general principle of
[orthogonal design](https://en.wikipedia.org/wiki/Orthogonality_(programming)),
in which separate concepts are assigned to separate methods. Here, `g`
will most likely need a fallback definition
-->
```

この例では、`g`が引数を型`A`に変換します。
これは、一般的な原理である[直交設計](https://en.wikipedia.org/wiki/Orthogonality_(programming))のとても具体的な例であり、 この設計では、別々の概念ごとに別々のメソッドが割り当てられています。
ここで`g`には、おそらくフォールバックの定義が必要でしょう。


```julia
g(x::A) = x
```

```@raw html
<!--
A related strategy exploits `promote` to bring `x` and `y` to a common
type:
-->
```
関連する戦略として、`昇格`を利用して`x`と`y`を共通の型に変換する、というのもあります。


```julia
f(x::T, y::T) where {T} = ...
f(x, y) = f(promote(x, y)...)
```

```@raw html
<!--
One risk with this design is the possibility that if there is no
suitable promotion method converting `x` and `y` to the same type, the
second method will recurse on itself infinitely and trigger a stack
overflow. The non-exported function `Base.promote_noncircular` can be
used as an alternative; when promotion fails it will still throw an
error, but one that fails faster with a more specific error message.
-->
```

この設計のリスクの1つは、`x`と`y`を同じ型に変換する適切な昇格メソッドがないために、
2番目のメソッドが再帰的に無限に繰り返し実行され、スタックオーバーフローが発生する可能性です。
公開されていない関数`Base.promote_noncircular`を代わりに利用できます。
昇格に失敗した場合でもエラーを投げますが、もっと早く失敗してより具体的なエラーメッセージが表示されます。


`[](### Dispatch on one argument at a time)
### 一度に１引数のディスパッチ

```@raw html
<!--
If you need to dispatch on multiple arguments, and there are many
fallbacks with too many combinations to make it practical to define
all possible variants, then consider introducing a "name cascade"
where (for example) you dispatch on the first argument and then call
an internal method:
-->
```

複数の引数に対してディスパッチを行う必要があっても、メソッドの組み合わせが多すぎて、フォールバックを現実的には定義しきれない場合は、（たとえば）最初の引数に対するディスパッチを行ってから内部のメソッドを呼び出すという「名前のカスケード」の導入を検討してください。


```julia
f(x::A, y) = _fA(x, y)
f(x::B, y) = _fB(x, y)
```

```@raw html
<!--
Then the internal methods `_fA` and `_fB` can dispatch on `y` without
concern about ambiguities with each other with respect to `x`.

Be aware that this strategy has at least one major disadvantage: in
many cases, it is not possible for users to further customize the
behavior of `f` by defining further specializations of your exported
function `f`. Instead, they have to define specializations for your
internal methods `_fA` and `_fB`, and this blurs the lines between
exported and internal methods.
-->
```

そうすれば、内部メソッドの `_fA`と`_fB`は、`x`に関しては曖昧さを気にせずに、`y`に対してディスパッチできます。

この戦略には、少なくとも1つの重要な欠点があることに注意してください。
多くの場合、公開された関数`f`に対してさらに特化した定義を行うようなカスタマイズをユーザーがすることはできません。
代わりに、自作の内部メソッド`_fA`と`_fB`の特化を定義する必要があり、これによって、公開した関数と内部メソッドの境界がぼやけます。


`[](### Abstract containers and element types)
### 抽象コンテナと要素の型

```@raw html
<!--
Where possible, try to avoid defining methods that dispatch on
specific element types of abstract containers. For example,
-->
```

可能であれば、抽象型コンテナに特定の要素型に対してディスパッチするようなメソッドの定義は避けてください。例えば、


```julia
-(A::AbstractArray{T}, b::Date) where {T<:Date}
```

```@raw html
<!--
generates ambiguities for anyone who defines a method
-->
```


```julia
-(A::MyArrayType{T}, b::T) where {T}
```

```@raw html
<!--
The best approach is to avoid defining *either* of these methods:
instead, rely on a generic method `-(A::AbstractArray, b)` and make
sure this method is implemented with generic calls (like `similar` and
`-`) that do the right thing for each container type and element type
*separately*. This is just a more complex variant of the advice to
[orthogonalize](@ref man-methods-orthogonalize) your methods.
-->
```
最良の方法は、これらのメソッドの **いずれかを** 定義することを避けることです。
代わりに、汎化メソッドの`-(A::AbstractArray, b)`を使い、このメソッドが各コンテナ型や要素の型ごとに **別々に** 汎化呼び出し（`similar`や `-`など）を適切に行うような実装であることを確認します。
これはメソッドを[直交化]（@ ref man-methods-orthogonalize）するという助言のちょっと複雑な変形版です。


```@raw html
<!--
When this approach is not possible, it may be worth starting a
discussion with other developers about resolving the ambiguity; just
because one method was defined first does not necessarily mean that it
can't be modified or eliminated.  As a last resort, one developer can
define the "band-aid" method
-->
```

この手法が不可能な場合、曖昧さを解決する議論を他の開発者と始めることは、十分価値があります。
始めにメソッドが１つ定義されていても、必ずしもそのメソッドを変更や削除できないわけではないからです。
最後の手段として、1人の開発者が「救済」メソッドを定義するという手があります。


```julia
-(A::MyArrayType{T}, b::Date) where {T<:Date} = ...
```

```@raw html
<!--
that resolves the ambiguity by brute force.
-->
```


`[](### Complex method "cascades" with default arguments)
### 複雑なメソッドに"多段的に"デフォルト引数を使う


```@raw html
<!--
If you are defining a method "cascade" that supplies defaults, be
careful about dropping any arguments that correspond to potential
defaults. For example, suppose you're writing a digital filtering
algorithm and you have a method that handles the edges of the signal
by applying padding:
-->
```

"多段的に"デフォルト値を設定するメソッドを定義する場合、ありうるデフォルト値に対応する引数を見落とさないように注意してください。たとえば、デジタルフィルタリングアルゴリズムを作成していて、信号のエッジをパディングを適用して処理する方法があるとします。


```julia
function myfilter(A, kernel, ::Replicate)
    Apadded = replicate_edges(A, size(kernel))
    myfilter(Apadded, kernel)  # now perform the "real" computation
end
```

```@raw html
<!--
This will run afoul of a method that supplies default padding:
-->
```
これは、デフォルトのパディングを行うメソッドと衝突します：


```julia
myfilter(A, kernel) = myfilter(A, kernel, Replicate()) # replicate the edge by default
```

```@raw html
<!--
Together, these two methods generate an infinite recursion with `A` constantly growing bigger.

The better design would be to define your call hierarchy like this:
-->
```
これらの2つのメソッドは、一緒になって、常に大きくなる`A`の無限再帰を生成します。

より良い設計は、次のように呼び出しの階層を定義することです。


```julia
struct NoPad end  # indicate that no padding is desired, or that it's already applied

myfilter(A, kernel) = myfilter(A, kernel, Replicate())  # default boundary conditions

function myfilter(A, kernel, ::Replicate)
    Apadded = replicate_edges(A, size(kernel))
    myfilter(Apadded, kernel, NoPad())  # indicate the new boundary conditions
end

# other padding methods go here

function myfilter(A, kernel, ::NoPad)
    # Here's the "real" implementation of the core computation
end
```

```@raw html
<!--
`NoPad` is supplied in the same argument position as any other kind of
padding, so it keeps the dispatch hierarchy well organized and with
reduced likelihood of ambiguities. Moreover, it extends the "public"
`myfilter` interface: a user who wants to control the padding
explicitly can call the `NoPad` variant directly.
-->
```
`NoPad`は、他の種類のパディングと同じ引数の位置で指定されているため、ディスパッチの階層を整理しやすく、曖昧になる可能性が低くなります。さらに、"パブリック"の`myfilter`インターフェースを拡張します。パディングを明示的にコントロールしたいユーザーは、`NoPad`のメソッドを直接呼び出すことができます。


[^Clarke61]: Arthur C. Clarke, *Profiles of the Future* (1961): Clarke's Third Law.

