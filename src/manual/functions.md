`[](# [Functions](@id man-functions))
# [関数](@id man-functions)

```@raw html
<!--
In Julia, a function is an object that maps a tuple of argument values to a return value. Julia
functions are not pure mathematical functions, in the sense that functions can alter and be affected
by the global state of the program. The basic syntax for defining functions in Julia is:
-->
```

Juliaでは、関数とは引数の値のタプルに対して戻り値を返すオブジェクトです。
Juliaの関数は純粋に数学的な関数では、ありません。
これは、プログラムのグローバルな状態によって変更されたり、影響を受けたりするという意味です。
Juliaで関数を定義する基本構文は以下の様になります。

```jldoctest
julia> function f(x,y)
           x + y
       end
f (generic function with 1 method)
```

```@raw html
<!--
There is a second, more terse syntax for defining a function in Julia. The traditional function
declaration syntax demonstrated above is equivalent to the following compact "assignment form":
-->
```
Juliaには、第二の簡潔な関数定義の構文があります。
上記の従来の関数宣言の構文と下記のコンパクトな"代入形式"は同等です。



```jldoctest fofxy
julia> f(x,y) = x + y
f (generic function with 1 method)
```

```@raw html
<!--
In the assignment form, the body of the function must be a single expression, although it can
be a compound expression (see [Compound Expressions](@ref man-compound-expressions)). Short, simple function definitions
are common in Julia. The short function syntax is accordingly quite idiomatic, considerably reducing
both typing and visual noise.

A function is called using the traditional parenthesis syntax:
-->
```

代入形式では、関数の本体は単一の式でなければなりませんが、複合式[複合式](@ref man-compound-expressions))でも構いません。
短くて単純な定義はJuliaでよく使われます。
短い関数構文は慣用的によく使われ、打鍵数や見た目のわずらわしさをかなり減らしてくれます。

関数の呼び出しには、従来通り、括弧を使います。


```jldoctest fofxy
julia> f(2,3)
5
```

```@raw html
<!--
Without parentheses, the expression `f` refers to the function object, and can be passed around
like any value:
-->
```

括弧がない場合は、式`f`は関数オブジェクトを参照し、他の値と同じように受け渡しができます。

```jldoctest fofxy
julia> g = f;

julia> g(2,3)
5
```

```@raw html
<!--
As with variables, Unicode can also be used for function names:
-->
```

変数と同じように関数名にはユニコードを利用可能です。

```jldoctest
julia> ∑(x,y) = x + y
∑ (generic function with 1 method)

julia> ∑(2, 3)
5
```

`[](## Argument Passing Behavior)
## 引数引き渡しの挙動

```@raw html
<!--
Julia function arguments follow a convention sometimes called "pass-by-sharing", which means that
values are not copied when they are passed to functions. Function arguments themselves act as
new variable *bindings* (new locations that can refer to values), but the values they refer to
are identical to the passed values. Modifications to mutable values (such as `Array`s) made within
a function will be visible to the caller. This is the same behavior found in Scheme, most Lisps,
Python, Ruby and Perl, among other dynamic languages.
-->
```
Juliaの引数は"共有渡し"と呼ばれる慣例に従ています。
これは、関数に渡すときに複写をしないという意味です。
関数の引数自体は新しい変数 **束縛** のように振る舞いますが、値は受け取る値と同じものを参照しています。
（配列のような）可変な値を関数内で変更すると、呼び出し側からも見えます。
こうした挙動は、Scheme、ほとんどのLisp、Python、Ruby、Perlその他の動的言語でみられるものと同じです。


`[](## The `return` Keyword)
## `return` キーワード

```@raw html
<!--
The value returned by a function is the value of the last expression evaluated, which, by default,
is the last expression in the body of the function definition. In the example function, `f`, from
the previous section this is the value of the expression `x + y`. As in C and most other imperative
or functional languages, the `return` keyword causes a function to return immediately, providing
an expression whose value is returned:
-->
```

関数の戻り値は最後に評価された式の値で、デフォルトでは関数定義本体の最後の式です。
前セクションで例示した関数`f`の場合、式`x + y`の値がこれに当たります。
C言語その他の命令型・関数型言語の大部分が、`return`キーワードによって、即時終了し、指定した式の値を戻り値とします。



```julia
function g(x,y)
    return x * y
    x + y
end
```

```@raw html
<!--
Since function definitions can be entered into interactive sessions, it is easy to compare these
definitions:
-->
```

関数定義を対話セッションで入力できるので、これらの定義を比較するのは簡単です。


```jldoctest
julia> f(x,y) = x + y
f (generic function with 1 method)

julia> function g(x,y)
           return x * y
           x + y
       end
g (generic function with 1 method)

julia> f(2,3)
5

julia> g(2,3)
6
```

```@raw html
<!--
Of course, in a purely linear function body like `g`, the usage of `return` is pointless since
the expression `x + y` is never evaluated and we could simply make `x * y` the last expression
in the function and omit the `return`. In conjunction with other control flow, however, `return`
is of real use. Here, for example, is a function that computes the hypotenuse length of a right
triangle with sides of length `x` and `y`, avoiding overflow:
-->
```

当然、`g`のように順次処理だけを行う関数の本体で、`ruturn`という用法をつかっても意味がありません。
というのも、式`x + y` は決して評価されず、単に`x * y`を関数内の最後の式にして`return`を省いてもいいからです。
しかし他の制御フローとつなぎ合わせる場合、`return`は実用的です。
直角を挟む２辺が`x`、`y`の三角形の斜辺をオーバーフローを避けながら計算する関数を書いてみると、


```jldoctest
julia> function hypot(x,y)
           x = abs(x)
           y = abs(y)
           if x > y
               r = y/x
               return x*sqrt(1+r*r)
           end
           if y == 0
               return zero(x)
           end
           r = x/y
           return y*sqrt(1+r*r)
       end
hypot (generic function with 1 method)

julia> hypot(3, 4)
5.0
```

```@raw html
<!--
There are three possible points of return from this function, returning the values of three different
expressions, depending on the values of `x` and `y`. The `return` on the last line could be omitted
since it is the last expression.

A return type can also be specified in the function declaration using the `::` operator. This converts
the return value to the specified type.
-->
```

この関数には３箇所、終了しうる場所があり、３つの異なる式の値を返しますが、`x`と`y`の値に依存します。
最終行の`return`は最後の式なので省略可能です。

関数の戻り値の型は、関数宣言の中で、`::`演算子を使って指定できます。
これによって、戻り値を指定した型に変換します。



```jldoctest
julia> function g(x, y)::Int8
           return x * y
       end;

julia> typeof(g(1, 2))
Int8
```

```@raw html
<!--
This function will always return an `Int8` regardless of the types of `x` and `y`.
See [Type Declarations](@ref) for more on return types.
-->
```

この関数は`x`や`y`の型にかかわらず`Int8`を返します。
戻り値の型の詳細については、[型宣言](@ref)を参照してください。


`[](## Operators Are Functions)
## 演算子は関数

```@raw html
<!--
In Julia, most operators are just functions with support for special syntax. (The exceptions are
operators with special evaluation semantics like `&&` and `||`. These operators cannot be functions
since [Short-Circuit Evaluation](@ref) requires that their operands are not evaluated before evaluation
of the operator.) Accordingly, you can also apply them using parenthesized argument lists, just
as you would any other function:
-->
```
Juliaではほとんどの演算子が特殊な構文を利用できる単なる関数です。
（例外は特殊な評価セマンティックを持つ`&&`や`||`などの演算子です。
これらは、関数とはなりえません。
というのも、[短絡評価](@ref)では、演算子の評価の前に被演算子の評価はされないからです。）
したがって、演算子を、他の関数と同じように、パラメータ付きの引数リストに適用することができます。

```jldoctest
julia> 1 + 2 + 3
6

julia> +(1,2,3)
6
```

```@raw html
<!--
The infix form is exactly equivalent to the function application form -- in fact the former is
parsed to produce the function call internally. This also means that you can assign and pass around
operators such as [`+`](@ref) and [`*`](@ref) just like you would with other function values:
-->
```

中置形式は関数適用形式と全く同等です。
実のところ前者は内部で関数呼び出しを行っています。
これは、[`+`](@ref) や [`*`](@ref)といった演算子に対して、他の関数とおなじように、代入や受け渡しが可能だと言うことです。


```jldoctest
julia> f = +;

julia> f(1,2,3)
6
```

```@raw html
<!--
Under the name `f`, the function does not support infix notation, however.
-->
```

しかし、`f`という名前では、関数は中置記法を利用できません。


`[](## Operators With Special Names)
## 特殊な名前の演算子


```@raw html
<!--
A few special expressions correspond to calls to functions with non-obvious names. These are:

| Expression        | Calls                   |
|:----------------- |:----------------------- |
| `[A B C ...]`     | [`hcat`](@ref)          |
| `[A; B; C; ...]`  | [`vcat`](@ref)          |
| `[A B; C D; ...]` | [`hvcat`](@ref)         |
| `A'`              | [`adjoint`](@ref)       |
| `A[i]`            | [`getindex`](@ref)      |
| `A[i] = x`        | [`setindex!`](@ref)     |
| `A.n`             | [`getproperty`](@ref Base.getproperty) |
| `A.n = x`         | [`setproperty!`](@ref Base.setproperty!) |
-->
```
見た目からはわからない名前で関数呼び出しできる特殊な式がいくつかあります。それらは

| 式                | 呼び出し名               |
|:----------------- |:----------------------- |
| `[A B C ...]`     | [`hcat`](@ref)          |
| `[A; B; C; ...]`  | [`vcat`](@ref)          |
| `[A B; C D; ...]` | [`hvcat`](@ref)         |
| `A'`              | [`adjoint`](@ref)       |
| `A[i]`            | [`getindex`](@ref)      |
| `A[i] = x`        | [`setindex!`](@ref)     |
| `A.n`             | [`getproperty`](@ref Base.getproperty) |
| `A.n = x`         | [`setproperty!`](@ref Base.setproperty!) |


`[](## [Anonymous Functions](@id man-anonymous-functions))
## [無名関数](@id man-anonymous-functions)

```@raw html
<!--
Functions in Julia are [first-class objects](https://en.wikipedia.org/wiki/First-class_citizen):
they can be assigned to variables, and called using the standard function call syntax from the
variable they have been assigned to. They can be used as arguments, and they can be returned as
values. They can also be created anonymously, without being given a name, using either of these
syntaxes:
-->
```

Juliaにおいて関数は [第一級オブジェクトです](https://en.wikipedia.org/wiki/First-class_citizen)。
関数を変数に代入し、標準的な関数を呼び出す構文で、束縛されている変数から呼び出すことができます。
また関数は、引数としても戻り値としても利用することができます。
また無名のまま、つまり名前をつけないで生成することができ、下記の構文のうちのどれかで作成できます。

```jldoctest
julia> x -> x^2 + 2x - 1
#1 (generic function with 1 method)

julia> function (x)
           x^2 + 2x - 1
       end
#3 (generic function with 1 method)
```

```@raw html
<!--
This creates a function taking one argument `x` and returning the value of the polynomial `x^2 +
2x - 1` at that value. Notice that the result is a generic function, but with a compiler-generated
name based on consecutive numbering.

The primary use for anonymous functions is passing them to functions which take other functions
as arguments. A classic example is [`map`](@ref), which applies a function to each value of
an array and returns a new array containing the resulting values:
-->
```
この構文から、引数が`x`で、戻り値が その値に対する多項式`x^2 +2x - 1`の値となる関数が生成されます、。
ここで生成される関数は、汎化関数ですが、コンパイラの生成した通し番号が名付けられている点に注意してください。

無名関数の主な用途は、他の関数を引数にとる関数に渡すことです。
典型的な例は [`map`](@ref)で、この関数は、配列の各値に対して関数を適用し、その結果を新しい配列として返します。


```jldoctest
julia> map(round, [1.2,3.5,1.7])
3-element Array{Float64,1}:
 1.0
 4.0
 2.0
```



```@raw html
<!--
This is fine if a named function effecting the transform already exists to pass as the first argument
to [`map`](@ref). Often, however, a ready-to-use, named function does not exist. In these
situations, the anonymous function construct allows easy creation of a single-use function object
without needing a name:
-->
```

これは、変換を行う名前付き関数がすでに存在し、 [`map`](@ref)の第1引数に渡せる場合は申し分ありません。
しかし、すぐに使える既存の関数がないことは、よくあります。
そんな時は、無名関数の構文で、名前を必要としない使い捨ての関数オブジェクトを簡単に作ることができます。


```jldoctest
julia> map(x -> x^2 + 2x - 1, [1,3,-1])
3-element Array{Int64,1}:
  2
 14
 -2
```

```@raw html
<!--
An anonymous function accepting multiple arguments can be written using the syntax `(x,y,z)->2x+y-z`.
A zero-argument anonymous function is written as `()->3`. The idea of a function with no arguments
may seem strange, but is useful for "delaying" a computation. In this usage, a block of code is
wrapped in a zero-argument function, which is later invoked by calling it as `f`.
-->
```

複数の引数をとる無名関数は、 `(x,y,z)->2x+y-z`といった構文で書くことができます。
引数のない関数の場合は、`()->3`のように書けます。

引数のない関数という考えは奇妙に思えるかもしれませんが、計算を”遅らせる”時に役立ちます。
この記法で、コードの塊を引数のない関数で囲って、'f'のように後で呼び出します。



`[](## Tuples)
## タプル

```@raw html
<!--
Julia has a built-in data structure called a *tuple* that is closely related to function
arguments and return values.
A tuple is a fixed-length container that can hold any values, but cannot be modified
(it is *immutable*).
Tuples are constructed with commas and parentheses, and can be accessed via indexing:
-->
```

Juliaには、組込みの **タプル** と呼ばれるデータ型があり、関数の引数や戻り値と密接に関係しています。
タプルは長さの決まったコンテナで、任意の値を保持しますが、変更はできません。（つまり **不変** です）。
タプルはコンマと括弧で構成され、インデックスを使ってアクセスできます。



```jldoctest
julia> (1, 1+1)
(1, 2)

julia> (1,)
(1,)

julia> x = (0.0, "hello", 6*7)
(0.0, "hello", 42)

julia> x[2]
"hello"
```

```@raw html
<!--
Notice that a length-1 tuple must be written with a comma, `(1,)`, since `(1)` would just
be a parenthesized value.
`()` represents the empty (length-0) tuple.
-->
```

長さ１のタプルは、コンマをつけて`(1,)`のように書く必要がある点に気をつけてください。
`(1)`は括弧をつけた単なる値です。
`()`だと空の（長さ０）のタプルを表します。

`[](## Named Tuples)
## 名前付きタプル

```@raw html
<!--
The components of tuples can optionally be named, in which case a *named tuple* is
constructed:
-->
```
タプルの要素には、必要に応じて名前をつけることができます。
この場合 **名前付きタプル**が生成されます。


```jldoctest
julia> x = (a=1, b=1+1)
(a = 1, b = 2)

julia> x.a
1
```

```@raw html
<!--
Named tuples are very similar to tuples, except that fields can additionally be accessed by name
using dot syntax (`x.a`).
-->
```

名前付きタプルはタプルと非常に似ていますが、そのフィールドに対しては更に、ドット構文(`x.a`)を使って名前でアクセスできます。


`[](## Multiple Return Values)
## 複数の戻り値

```@raw html
<!--
In Julia, one returns a tuple of values to simulate returning multiple values. However, tuples
can be created and destructured without needing parentheses, thereby providing an illusion that
multiple values are being returned, rather than a single tuple value. For example, the following
function returns a pair of values:
-->
```

Juliaでは値をタプルを使って、擬似的に複数の値を返すことができますが、
タプルは括弧をつかわなくても、生成・分割ができるので、単一のタプルの値ではなく複数の値を返しているような錯覚を与えるでしょう。
下記の関数では値の組を返しています。


```jldoctest foofunc
julia> function foo(a,b)
           a+b, a*b
       end
foo (generic function with 1 method)
```

```@raw html
<!--
If you call it in an interactive session without assigning the return value anywhere, you will
see the tuple returned:
-->
```

この関数を対話セッションで戻り値をどこにも代入しない場合は、タプルが返ってくるのを確認できます。


```jldoctest foofunc
julia> foo(2,3)
(5, 6)
```

```@raw html
<!--
A typical usage of such a pair of return values, however, extracts each value into a variable.
Julia supports simple tuple "destructuring" that facilitates this:
-->
```

しかし、こういった戻り値を組みにして返す用法をよく使うのは、それぞれの値を取り出して変数に代入する場合でしょう。
Juliaでは、これを簡単にするタプルの"分割"に対応しています。


```jldoctest foofunc
julia> x, y = foo(2,3)
(5, 6)

julia> x
5

julia> y
6
```

```@raw html
<!--
You can also return multiple values via an explicit usage of the `return` keyword:
-->
```
Juliaでは`return`キーワードを明示した用法で、複数の値を返すこともできます。

```julia
function foo(a,b)
    return a+b, a*b
end
```

```@raw html
<!--
This has the exact same effect as the previous definition of `foo`.
-->
```

これは既出の`foo`の定義と全く同じ効果があります。


`[](## Argument destructuring)
## 引数分割

```@raw html
<!--
The destructuring feature can also be used within a function argument.
If a function argument name is written as a tuple (e.g. `(x, y)`) instead of just
a symbol, then an assignment `(x, y) = argument` will be inserted for you:
-->
```

分割の機能は、関数の引数の中でも利用できます。
関数の引数の位置にそれぞれの記号(例えば `(x, y)`)ではなくタプルを書くと、
`(x, y) = 引数のタプル`という代入が実行されます。


```julia
julia> minmax(x, y) = (y < x) ? (y, x) : (x, y)

julia> range((min, max)) = max - min

julia> range(minmax(10, 2))
8
```

```@raw html
<!--
Notice the extra set of parentheses in the definition of `range`.
Without those, `range` would be a two-argument function, and this example would
not work.
-->
```

 `range`の定義で余計な括弧があるのに注意してください。
 これがなければ、`range`は引数が２個の関数となり、この例はうまく動作しません。


`[](## Varargs Functions)
## 可変引数関数

```@raw html
<!--
It is often convenient to be able to write functions taking an arbitrary number of arguments.
Such functions are traditionally known as "varargs" functions, which is short for "variable number
of arguments". You can define a varargs function by following the last argument with an ellipsis:
-->
```

引数の数が任意個の関数が書けると便利なことがよくあります。
そういった関数は従来、”可変引数”関数として知られ、これは”可変個の引数”の略です。
可変引数関数は、最後の引数のあとに省略記号をつけると定義できます。



```jldoctest barfunc
julia> bar(a,b,x...) = (a,b,x)
bar (generic function with 1 method)
```

```@raw html
<!--
The variables `a` and `b` are bound to the first two argument values as usual, and the variable
`x` is bound to an iterable collection of the zero or more values passed to `bar` after its first
two arguments:
-->
```

変数`a`と`b`は、通常通り、最初の２つの変数に束縛されています。
変数`x`は、最初の２つの引数に続いて`bar`に渡された引数からなる、０個以上のイテラブルコレクションが束縛される。 


```jldoctest barfunc
julia> bar(1,2)
(1, 2, ())

julia> bar(1,2,3)
(1, 2, (3,))

julia> bar(1, 2, 3, 4)
(1, 2, (3, 4))

julia> bar(1,2,3,4,5,6)
(1, 2, (3, 4, 5, 6))
```

```@raw html
<!--
In all these cases, `x` is bound to a tuple of the trailing values passed to `bar`.

It is possible to constrain the number of values passed as a variable argument; this will be discussed
later in [Parametrically-constrained Varargs methods](@ref).

On the flip side, it is often handy to "splat" the values contained in an iterable collection
into a function call as individual arguments. To do this, one also uses `...` but in the function
call instead:
-->
```

すべての場合で、`x`は`bar`に渡された後続の値からなるタプルに束縛されます。

可変引数として渡される値の個数を制限することも可能ます。
これは後述の[パラメータ制限つきの可変引数メソッド](@ref)で議論します。

また、イテラブルコレクションに含まれる値と、関数呼び出しの各引数とは、簡単に"接合"できるので、よく使われます。


```jldoctest barfunc
julia> x = (3, 4)
(3, 4)

julia> bar(1,2,x...)
(1, 2, (3, 4))
```

```@raw html
<!--
In this case a tuple of values is spliced into a varargs call precisely where the variable number
of arguments go. This need not be the case, however:
-->
```

この接合は、引数の個数とタプルの要素数がちょう等しいので、する必要はありません。



```jldoctest barfunc
julia> x = (2, 3, 4)
(2, 3, 4)

julia> bar(1,x...)
(1, 2, (3, 4))

julia> x = (1, 2, 3, 4)
(1, 2, 3, 4)

julia> bar(x...)
(1, 2, (3, 4))
```

```@raw html
<!--
Furthermore, the iterable object splatted into a function call need not be a tuple:
-->
```

さらに、関数呼び出しと接合するイテラブルオブジェクトはタプルである必要はありません。

```jldoctest barfunc
julia> x = [3,4]
2-element Array{Int64,1}:
 3
 4

julia> bar(1,2,x...)
(1, 2, (3, 4))

julia> x = [1,2,3,4]
4-element Array{Int64,1}:
 1
 2
 3
 4

julia> bar(x...)
(1, 2, (3, 4))
```

```@raw html
<!--
Also, the function that arguments are splatted into need not be a varargs function (although it
often is):
-->
```

引数を接合する関数は可変引数関数でなくてもかまいません（可変引数関数である方が多いですが）。

```jldoctest
julia> baz(a,b) = a + b;

julia> args = [1,2]
2-element Array{Int64,1}:
 1
 2

julia> baz(args...)
3

julia> args = [1,2,3]
3-element Array{Int64,1}:
 1
 2
 3

julia> baz(args...)
ERROR: MethodError: no method matching baz(::Int64, ::Int64, ::Int64)
Closest candidates are:
  baz(::Any, ::Any) at none:1
```

```@raw html
<!--
As you can see, if the wrong number of elements are in the splatted container, then the function
call will fail, just as it would if too many arguments were given explicitly.
-->
```

接合するコンテナの要素の数が適切ではない場合は、関数呼び出しは失敗します。
明示的に与える引数が多すぎる場合と同じです。


`[](## Optional Arguments)
## オプション引数

```@raw html
<!--
In many cases, function arguments have sensible default values and therefore might not need to
be passed explicitly in every call. For example, the function [`Date(y, [m, d])`](@ref)
from `Dates` module constructs a `Date` type for a given year `y`, month `m` and day `d`.
However, `m` and `d` arguments are optional and their default value is `1`.
This behavior can be expressed concisely as:
-->
```

多くの場合、関数の引数には適切なデフォルト値があり、すべての呼び出しでわざわざ値を渡す必要はないかもしれません。
例えば、`Dates`モジュールにある[`Date(y, [m, d])`](@ref)関数は、年`y`・月`m`・日`d`から`Date`型を構成します。
しかし、`m`と`d`は省略可能で、デフォルト値は`1`です。
この挙動は簡単にこう表現できます。

```julia
function Date(y::Int64, m::Int64=1, d::Int64=1)
    err = validargs(Date, y, m, d)
    err === nothing || throw(err)
    return Date(UTD(totaldays(y, m, d)))
end
```

```@raw html
<!--
Observe, that this definition calls another method of `Date` function that takes one argument
of `UTInstant{Day}` type.

With this definition, the function can be called with either one, two or three arguments, and
`1` is automatically passed when any of the arguments is not specified:
-->
```
見ての通り、この定義では`Date`関数の別メソッドで、`UTInstant{Day}`型の1個引数にとるものを呼び出しています。
この定義によって、関数は１個または２個または３個の引数を取り、指定しなかった引数には、自動的に`1`を渡します。


```jldoctest
julia> using Dates

julia> Date(2000, 12, 12)
2000-12-12

julia> Date(2000, 12)
2000-12-01

julia> Date(2000)
2000-01-01
```

```@raw html
<!--
Optional arguments are actually just a convenient syntax for writing multiple method definitions
with different numbers of arguments (see [Note on Optional and keyword Arguments](@ref)).
This can be checked for our `Date` function example by calling `methods` function.
-->
```

省略可能な引数は実際には引数の異なる複数のメソッドを定義する、簡便な記法です。
([オプション引数・キーワード引数に関する注記](@ref)を参照)
これは`methods`関数から、例に挙げた`Date`関数呼び出して、確認することができます。

`[](## Keyword Arguments)
## キーワード引数
```@raw html
<!--
Some functions need a large number of arguments, or have a large number of behaviors. Remembering
how to call such functions can be difficult. Keyword arguments can make these complex interfaces
easier to use and extend by allowing arguments to be identified by name instead of only by position.

For example, consider a function `plot` that plots a line. This function might have many options,
for controlling line style, width, color, and so on. If it accepts keyword arguments, a possible
call might look like `plot(x, y, width=2)`, where we have chosen to specify only line width. Notice
that this serves two purposes. The call is easier to read, since we can label an argument with
its meaning. It also becomes possible to pass any subset of a large number of arguments, in any
order.

Functions with keyword arguments are defined using a semicolon in the signature:
-->
```

関数の中には引数の数が多いものや、挙動の数が多いものがあります。
そういった関数の呼び出し方を覚えるのは難しくなることがあります。
キーワード引数を使うと、位置ではなく名前で引数を指定できるので、使用や拡張が簡単になります。

例えば線を引く`plot`関数を考えてみます。
おそらく、この関数には、線の形状、幅、色など、たくさんのオプションがあるでしょう。
キーワード引数を使えば、線の幅だけを指定するような`plot(x, y, width=2)`といった呼び出し方が可能でしょう。
これには2つの役割がある点に注意してください。
まずは、関数呼び出しが読みやすくなります。
というのも、引数に何を意味するかラベル付けできるからです。
次に、多数の引数のなかから、任意の部分集合を任意の順序で受け渡しできます。

キーワード引数を持つ関数は、シグネチャの中でセミコロンを使って定義します。

```julia
function plot(x, y; style="solid", width=1, color="black")
    ###
end
```

```@raw html
<!--
When the function is called, the semicolon is optional: one can either call `plot(x, y, width=2)`
or `plot(x, y; width=2)`, but the former style is more common. An explicit semicolon is required
only for passing varargs or computed keywords as described below.

Keyword argument default values are evaluated only when necessary (when a corresponding keyword
argument is not passed), and in left-to-right order. Therefore default expressions may refer to
prior keyword arguments.

The types of keyword arguments can be made explicit as follows:
-->
```

関数を呼び出す時に、セミコロンは省略できます。
呼び出し方は`plot(x, y, width=2)`か`plot(x, y; width=2)`ですが、前者のほうがよく使われます。
明示的にセミコロンを使う必要があるのは、下記のような、可変引数を渡す場合か計算結果のキーワードを渡すときです。

キーワード引数のデフォルト値は必要な時だけ左から右へ評価されます（対応するキーワード引数が渡されないときです）。
そのため、デフォルト式は先にでたのキーワード参照可能です。



```julia
function f(;x::Int=1)
    ###
end
```

```@raw html
<!--
Extra keyword arguments can be collected using `...`, as in varargs functions:
-->
```

余分のキーワード引数は、可変引数関数を同じように`...`を使ってひとまとめにすることができます。

```julia
function f(x; y=0, kwargs...)
    ###
end
```

```@raw html
<!--
If a keyword argument is not assigned a default value in the method definition,
then it is *required*: an [`UndefKeywordError`](@ref) exception will be thrown
if the caller does not assign it a value:
-->
```

キーワード引数がメソッドの定義でデフォルト値を設定されていない場合は、 **入力必須** となります。
呼び出し側が値を代入しない時には、 [`UndefKeywordError`](@ref)が投げられます。

```julia
function f(x; y)
    ###
end
f(3, y=5) # ok, y is assigned
f(3)      # throws UndefKeywordError(:y)
```

```@raw html
<!--
Inside `f`, `kwargs` will be a named tuple. Named tuples (as well as dictionaries) can be passed as
keyword arguments using a semicolon in a call, e.g. `f(x, z=1; kwargs...)`.

One can also pass `key => value` expressions after a semicolon. For example, `plot(x, y; :width => 2)`
is equivalent to `plot(x, y, width=2)`. This is useful in situations where the keyword name is computed
at runtime.
-->
```

`f`の内部で`kwargs`は名前付きタプルになります。
名前付きタプルは（辞書と同じように）、キーワード引数として関数に渡すことができて、呼び出す時にセミコロンを使います。
`f(x, z=1; kwargs...)`のように。
 `key => value`といった式もセミコロンのあとに続けて、渡すこともできます。
 例えば、`plot(x, y; :width => 2)`は`plot(x, y, width=2)`と同等です。
 これは、キーワードが実行時に算出される状況で便利です。



```@raw html
<!--
The nature of keyword arguments makes it possible to specify the same argument more than once.
For example, in the call `plot(x, y; options..., width=2)` it is possible that the `options` structure
also contains a value for `width`. In such a case the rightmost occurrence takes precedence; in
this example, `width` is certain to have the value `2`. However, explicitly specifying the same keyword
argument multiple times, for example `plot(x, y, width=2, width=3)`, is not allowed and results in
a syntax error.
-->
```
キーワード変数の特質から、同一の引数に対して、複数回指定をすることが可能です。
例えば、`plot(x, y; options..., width=2)`のように関数を呼び出すとき、`options`の中にも`width`の値が含まれます。
こういう場合は、一番右側の出現が優先されます。この例では、`width`は必ず２になります。
しかし、例えば`plot(x, y, width=2, width=3)`のように、同一のキーワード引数を明示的に複数回指定することは、禁止されており
構文エラーとなります。



`[](## Evaluation Scope of Default Values)
## デフォルト値の評価スコープ

```@raw html
<!--
When optional and keyword argument default expressions are evaluated, only *previous* arguments are in
scope.
For example, given this definition:
-->
```

オプション引数やキーワード引数のデフォルトの式が評価される時、スコープに入るのは **既出の** 引数だけです。
例えば、この式の場合

```julia
function f(x, a=b, b=1)
    ###
end
```

```@raw html
<!--
the `b` in `a=b` refers to a `b` in an outer scope, not the subsequent argument `b`.
-->
```
`a=b` の`b`は外側のスコープの`b`を参照して、後続の引数`b`は参照しません。


`[](## Do-Block Syntax for Function Arguments)
## 関数の引数に対するDoブロック構文

```@raw html
<!--
Passing functions as arguments to other functions is a powerful technique, but the syntax for
it is not always convenient. Such calls are especially awkward to write when the function argument
requires multiple lines. As an example, consider calling [`map`](@ref) on a function with several
cases:
-->
```

他の関数に対して関数を引数として渡すことは、強力な技法ですが、その構文は必ずしも便利ではありません。
関数の引数が何行にも渡る場合、特に不格好になります。
例として、 [`map`](@ref)関数が、場合分けがいくつかある関数を呼び出す場合を考えてみると、




```julia
map(x->begin
           if x < 0 && iseven(x)
               return 0
           elseif x == 0
               return 1
           else
               return x
           end
       end,
    [A, B, C])
```

```@raw html
<!--
Julia provides a reserved word `do` for rewriting this code more clearly:
-->
```

Juliaにある予約語`do`を使ってこのコードをもっと明快に書き直すことができます。

```julia
map([A, B, C]) do x
    if x < 0 && iseven(x)
        return 0
    elseif x == 0
        return 1
    else
        return x
    end
end
```

```@raw html
<!--
The `do x` syntax creates an anonymous function with argument `x` and passes it as the first argument
to [`map`](@ref). Similarly, `do a,b` would create a two-argument anonymous function, and a
plain `do` would declare that what follows is an anonymous function of the form `() -> ...`.

How these arguments are initialized depends on the "outer" function; here, [`map`](@ref) will
sequentially set `x` to `A`, `B`, `C`, calling the anonymous function on each, just as would happen
in the syntax `map(func, [A, B, C])`.

This syntax makes it easier to use functions to effectively extend the language, since calls look
like normal code blocks. There are many possible uses quite different from [`map`](@ref), such
as managing system state. For example, there is a version of [`open`](@ref) that runs code ensuring
that the opened file is eventually closed:
-->
```

`do x`構文は、引数が`x`である無名関数を生成して、[`map`](@ref)に1番目の引数として渡します。
同様に、`do a,b`では、引数が２個の無名関数を生成します。
また、単に`do`と書いた場合、`do`に続く記述を `...`とすると、 その記述が`() -> ...`という形の無名関数だという宣言になります。


これらの引数がどのように初期化されるかは、"外側"の関数に依存します。
ここでは、[`map`](@ref)は、`x` に `A`、`B`、`C`を順に代入しから、無名関数を呼び出して、`map(func, [A, B, C])`
の構文とおなじような結果になります。

この構文を使うと、簡単に、関数を使ってげんごを効率的に拡張できます。というのも、関数呼び出しが通常のコードブロックのような
外見をしているからです。
 [`map`](@ref)とはかなり違う使い方も多数ありえます。、例えばシステムの状態の管理などです。
例えば、開いたファイルは最後に閉じることを保証する[`open`](@ref)のバージョンがあります。



```julia
open("outfile", "w") do io
    write(io, data)
end
```

```@raw html
<!--
This is accomplished by the following definition:
-->
```

これは、以下の定義で達成できます。

```julia
function open(f::Function, args...)
    io = open(args...)
    try
        f(io)
    finally
        close(io)
    end
end
```

```@raw html
<!--
Here, [`open`](@ref) first opens the file for writing and then passes the resulting output stream
to the anonymous function you defined in the `do ... end` block. After your function exits, [`open`](@ref)
will make sure that the stream is properly closed, regardless of whether your function exited
normally or threw an exception. (The `try/finally` construct will be described in [Control Flow](@ref).)

With the `do` block syntax, it helps to check the documentation or implementation to know how
the arguments of the user function are initialized.

A `do` block, like any other inner function, can "capture" variables from its
enclosing scope. For example, the variable `data` in the above example of
`open...do` is captured from the outer scope. Captured variables
can create performance challenges as discussed in [performance tips](@ref man-performance-tips).
-->
```
ここでは、[`open`](@ref)はまず書き込み用にファイルを開き、`do ... end`で定義した無名関数の演算結果を出力ストリームに渡します。
関数を終了したあとは、ストリームが適切に終了したかを確認します。
これは、正常終了の場合も、例外を投げた場合も同様です。
(`try/finally`構文については、[制御フロー](@ref)で記述します)

`do` ブロック構文に関しては、ユーザー関数の引数が同初期化されるのかを知るには、ドキュメントや実装を確認すると役に立ちます。

`do` ブロックは、他の内部関数と同様に、取り囲むスコープの変数を"捕捉"することができます。
例えば、上述の`open...do`の中の変数`data`は、外側のスコープから補足しています。
変数を捕捉すると、 [パフォーマンス・ティップス](@ref man-performance-tips)で議論するように、パフォーマンス上の困難が生じる可能性があります。


`[](## [Dot Syntax for Vectorizing Functions](@id man-vectorized))
## [関数をベクトル化するDot構文](@id man-vectorized)

In technical-computing languages, it is common to have "vectorized" versions of functions, which
simply apply a given function `f(x)` to each element of an array `A` to yield a new array via
`f(A)`. This kind of syntax is convenient for data processing, but in other languages vectorization
is also often required for performance: if loops are slow, the "vectorized" version of a function
can call fast library code written in a low-level language. In Julia, vectorized functions are
*not* required for performance, and indeed it is often beneficial to write your own loops (see
[Performance Tips](@ref man-performance-tips)), but they can still be convenient. Therefore, *any* Julia function
`f` can be applied elementwise to any array (or other collection) with the syntax `f.(A)`.
For example `sin` can be applied to all elements in the vector `A`, like so:

```jldoctest
julia> A = [1.0, 2.0, 3.0]
3-element Array{Float64,1}:
 1.0
 2.0
 3.0

julia> sin.(A)
3-element Array{Float64,1}:
 0.8414709848078965
 0.9092974268256817
 0.1411200080598672
```

Of course, you can omit the dot if you write a specialized "vector" method of `f`, e.g. via `f(A::AbstractArray) = map(f, A)`,
and this is just as efficient as `f.(A)`. But that approach requires you to decide in advance
which functions you want to vectorize.

More generally, `f.(args...)` is actually equivalent to `broadcast(f, args...)`, which allows
you to operate on multiple arrays (even of different shapes), or a mix of arrays and scalars (see
[Broadcasting](@ref)). For example, if you have `f(x,y) = 3x + 4y`, then `f.(pi,A)` will return
a new array consisting of `f(pi,a)` for each `a` in `A`, and `f.(vector1,vector2)` will return
a new vector consisting of `f(vector1[i],vector2[i])` for each index `i` (throwing an exception
if the vectors have different length).

```jldoctest
julia> f(x,y) = 3x + 4y;

julia> A = [1.0, 2.0, 3.0];

julia> B = [4.0, 5.0, 6.0];

julia> f.(pi, A)
3-element Array{Float64,1}:
 13.42477796076938
 17.42477796076938
 21.42477796076938

julia> f.(A, B)
3-element Array{Float64,1}:
 19.0
 26.0
 33.0
```

Moreover, *nested* `f.(args...)` calls are *fused* into a single `broadcast` loop. For example,
`sin.(cos.(X))` is equivalent to `broadcast(x -> sin(cos(x)), X)`, similar to `[sin(cos(x)) for x in X]`:
there is only a single loop over `X`, and a single array is allocated for the result. [In contrast,
`sin(cos(X))` in a typical "vectorized" language would first allocate one temporary array for
`tmp=cos(X)`, and then compute `sin(tmp)` in a separate loop, allocating a second array.] This
loop fusion is not a compiler optimization that may or may not occur, it is a *syntactic guarantee*
whenever nested `f.(args...)` calls are encountered. Technically, the fusion stops as soon as
a "non-dot" function call is encountered; for example, in `sin.(sort(cos.(X)))` the `sin` and `cos`
loops cannot be merged because of the intervening `sort` function.

Finally, the maximum efficiency is typically achieved when the output array of a vectorized operation
is *pre-allocated*, so that repeated calls do not allocate new arrays over and over again for
the results (see [Pre-allocating outputs](@ref)). A convenient syntax for this is `X .= ...`, which
is equivalent to `broadcast!(identity, X, ...)` except that, as above, the `broadcast!` loop is
fused with any nested "dot" calls. For example, `X .= sin.(Y)` is equivalent to `broadcast!(sin, X, Y)`,
overwriting `X` with `sin.(Y)` in-place. If the left-hand side is an array-indexing expression,
e.g. `X[2:end] .= sin.(Y)`, then it translates to `broadcast!` on a `view`, e.g.
`broadcast!(sin, view(X, 2:lastindex(X)), Y)`,
so that the left-hand side is updated in-place.

Since adding dots to many operations and function calls in an expression
can be tedious and lead to code that is difficult to read, the macro
[`@.`](@ref @__dot__) is provided to convert *every* function call,
operation, and assignment in an expression into the "dotted" version.

```jldoctest
julia> Y = [1.0, 2.0, 3.0, 4.0];

julia> X = similar(Y); # pre-allocate output array

julia> @. X = sin(cos(Y)) # equivalent to X .= sin.(cos.(Y))
4-element Array{Float64,1}:
  0.5143952585235492
 -0.4042391538522658
 -0.8360218615377305
 -0.6080830096407656
```

Binary (or unary) operators like `.+` are handled with the same mechanism:
they are equivalent to `broadcast` calls and are fused with other nested "dot" calls.
 `X .+= Y` etcetera is equivalent to `X .= X .+ Y` and results in a fused in-place assignment;
 see also [dot operators](@ref man-dot-operators).

You can also combine dot operations with function chaining using [`|>`](@ref), as in this example:
```jldoctest
julia> [1:5;] .|> [x->x^2, inv, x->2*x, -, isodd]
5-element Array{Real,1}:
    1
    0.5
    6
   -4
 true
```

`[](## Further Reading)
## 関連項目

We should mention here that this is far from a complete picture of defining functions. Julia has
a sophisticated type system and allows multiple dispatch on argument types. None of the examples
given here provide any type annotations on their arguments, meaning that they are applicable to
all types of arguments. The type system is described in [Types](@ref man-types) and defining a function
in terms of methods chosen by multiple dispatch on run-time argument types is described in [Methods](@ref).
