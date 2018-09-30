`[](# Control Flow)
# 制御フロー


```@raw html
<!--
Julia provides a variety of control flow constructs:

  * [Compound Expressions](@ref man-compound-expressions): `begin` and `(;)`.
  * [Conditional Evaluation](@ref man-conditional-evaluation): `if`-`elseif`-`else` and `?:` (ternary operator).
  * [Short-Circuit Evaluation](@ref): `&&`, `||` and chained comparisons.
  * [Repeated Evaluation: Loops](@ref man-loops): `while` and `for`.
  * [Exception Handling](@ref): `try`-`catch`, [`error`](@ref) and [`throw`](@ref).
  * [Tasks (aka Coroutines)](@ref man-tasks): [`yieldto`](@ref).
-->
```

Juliaには様々な制御フローがあります。
  * [複合式](@ref man-compound-expressions): `begin`と `(;)`
  * [条件評価](@ref man-conditional-evaluation): `if`-`elseif`-`else` と `?:` (三項演算子)
  * [短絡評価](@ref): `&&`, `||` と　比較の連鎖
  * [反復評価：ループ](@ref man-loops): `while` と `for`.
  * [例外処理](@ref): `try`-`catch`, [`error`](@ref) と [`throw`](@ref).
  * [タスク (別名コルーチン)](@ref man-tasks): [`yieldto`](@ref).


```@raw html
<!--
The first five control flow mechanisms are standard to high-level programming languages. [`Task`](@ref)s
are not so standard: they provide non-local control flow, making it possible to switch between
temporarily-suspended computations. This is a powerful construct: both exception handling and
cooperative multitasking are implemented in Julia using tasks. Everyday programming requires no
direct usage of tasks, but certain problems can be solved much more easily by using tasks.
-->
```

最初の５つの制御フローのしくみは高水準のプログラム言語に標準的なものです。
タスクはそれほど標準的ではありませんが、非ローカルの制御フローで一時的に中断した計算を切り替える事ができます。
これは強力で、例外処理や協調的マルチタスクは、Juliaでは、タスクを使って実装されています。
日々のプログラムでタスクを直接使うわけではないですが、ある種のプログラムの問題ではタスクを使うと簡単に解決できます。


`[](## [Compound Expressions](@id man-compound-expressions))
## [複合式](@id man-compound-expressions)

```@raw html
<!--
Sometimes it is convenient to have a single expression which evaluates several subexpressions
in order, returning the value of the last subexpression as its value. There are two Julia constructs
that accomplish this: `begin` blocks and `(;)` chains. The value of both compound expression constructs
is that of the last subexpression. Here's an example of a `begin` block:
-->
```

単一の式で、何個かの部分式を順に評価し、最後の部分式の値をその式の値として返す、といったことができると便利なことが時々あります。
Juliaにはこれを達成する２つの構文があります。`begin`ブロックとセミコロン`(;)`連鎖です。 
共に複合式の値は、最後の部分式の値です。
ここに`begin`ブロックの例を挙げます。


```jldoctest
julia> z = begin
           x = 1
           y = 2
           x + y
       end
3
```

```@raw html
<!--
Since these are fairly small, simple expressions, they could easily be placed onto a single line,
which is where the `(;)` chain syntax comes in handy:
-->
```

これはかなり小さな単一式なので、簡単にセミコロン`(;)`連鎖の構文を使って一行にまとめることができます。


```jldoctest
julia> z = (x = 1; y = 2; x + y)
3
```

```@raw html
<!--
This syntax is particularly useful with the terse single-line function definition form introduced
in [Functions](@ref). Although it is typical, there is no requirement that `begin` blocks be multiline
or that `(;)` chains be single-line:
-->
```
この構文は、特に[関数](@ref)で紹介した、簡潔な１行での関数の定義に役立ちます。
`begin`ブロックは複数行で、セミコロン`(;)`連鎖は１行で使うのが普通ですが、必ずしもこれに従う必要はありません。


```jldoctest
julia> begin x = 1; y = 2; x + y end
3

julia> (x = 1;
        y = 2;
        x + y)
3
```

`[](## [Conditional Evaluation](@id man-conditional-evaluation))
## [条件評価](@id man-conditional-evaluation)

```@raw html
<!--
Conditional evaluation allows portions of code to be evaluated or not evaluated depending on the
value of a boolean expression. Here is the anatomy of the `if`-`elseif`-`else` conditional syntax:
-->
```
条件評価を使うと、コードの一部を評価するかどうかを、ブール式の値によって決めることができます。
ここで`if`-`elseif`-`else`の条件構文を解析してみます。


```julia
if x < y
    println("x is less than y")
elseif x > y
    println("x is greater than y")
else
    println("x is equal to y")
end
```

```@raw html
<!--
If the condition expression `x < y` is `true`, then the corresponding block is evaluated; otherwise
the condition expression `x > y` is evaluated, and if it is `true`, the corresponding block is
evaluated; if neither expression is true, the `else` block is evaluated. Here it is in action:
-->
```

条件式`x < y`が`true`のとき、対応するブロックが評価されます。
これが成り立たない時は、`x > y`が評価されて、これが`true`のときに、対応するブロックが評価されます。
どちらの式も真ではない時、`else`ブロックが評価されます。
実際に動作させてみると、

```jldoctest
julia> function test(x, y)
           if x < y
               println("x is less than y")
           elseif x > y
               println("x is greater than y")
           else
               println("x is equal to y")
           end
       end
test (generic function with 1 method)

julia> test(1, 2)
x is less than y

julia> test(2, 1)
x is greater than y

julia> test(1, 1)
x is equal to y
```

```@raw html
<!--
The `elseif` and `else` blocks are optional, and as many `elseif` blocks as desired can be used.
The condition expressions in the `if`-`elseif`-`else` construct are evaluated until the first
one evaluates to `true`, after which the associated block is evaluated, and no further condition
expressions or blocks are evaluated.
-->
```
`elseif`と`else`のブロックは省略可能で、`elseif`ブロックは好きな数だけ使うことができます。
`if`-`elseif`-`else`構文の条件式は、初めて`true`に評価されるものが出てくるまで続き、あとはその真の条件式に対応するブロックが評価され、
さらに条件式やブロックが評価されることはありません。



```@raw html
<!--
`if` blocks are "leaky", i.e. they do not introduce a local scope. This means that new variables
defined inside the `if` clauses can be used after the `if` block, even if they weren't defined
before. So, we could have defined the `test` function above as
-->
```

`if`ブロックには、"漏れ"があります。
つまり、ローカルスコープを採用していません。
これは、`if`節の中で定義した新しい変数は、`if`句の後ろで、たとえ`if`文の前に定義がないときでさえ、利用できることを意味します。
そのため、上述の`test`関数を以下のようにも定義できるのです。


```jldoctest
julia> function test(x,y)
           if x < y
               relation = "less than"
           elseif x == y
               relation = "equal to"
           else
               relation = "greater than"
           end
           println("x is ", relation, " y.")
       end
test (generic function with 1 method)

julia> test(2, 1)
x is greater than y.
```

```@raw html
<!--
The variable `relation` is declared inside the `if` block, but used outside. However, when depending
on this behavior, make sure all possible code paths define a value for the variable. The following
change to the above function results in a runtime error
-->
```

変数`relation`は`if`ブロックの中で宣言していますが、外側でも使えます。
しかし、この挙動を利用する時は、総ての取りうる分岐に対して変数が定義されているかどうか確かめる必要があります。
以下のように上述の関数を書換えると、実行時エラーが発生します。


```jldoctest; filter = r"Stacktrace:(\n \[[0-9]+\].*)*"
julia> function test(x,y)
           if x < y
               relation = "less than"
           elseif x == y
               relation = "equal to"
           end
           println("x is ", relation, " y.")
       end
test (generic function with 1 method)

julia> test(1,2)
x is less than y.

julia> test(2,1)
ERROR: UndefVarError: relation not defined
Stacktrace:
 [1] test(::Int64, ::Int64) at ./none:7
```

```@raw html
<!--
`if` blocks also return a value, which may seem unintuitive to users coming from many other languages.
This value is simply the return value of the last executed statement in the branch that was chosen,
so

-->
```
また、`if`ブロックは値を返しますが、他のプログラム言語出身のユーザーには、直観に反するかもしれません。
この値は、選択した分岐の中で最後に実行した文の単なる戻り値です。



```jldoctest
julia> x = 3
3

julia> if x > 0
           "positive!"
       else
           "negative..."
       end
"positive!"
```

```@raw html
<!--
Note that very short conditional statements (one-liners) are frequently expressed using Short-Circuit
Evaluation in Julia, as outlined in the next section.

Unlike C, MATLAB, Perl, Python, and Ruby -- but like Java, and a few other stricter, typed languages
-- it is an error if the value of a conditional expression is anything but `true` or `false`:

-->
```
とても短い（１行の）条件文は、Juliaではよく短絡評価を使って表現される点に注意してください。
これは、次のセクションで概説します。

 C, MATLAB, Perl, Python, Rubyなどとは異なり、しかしJavaやその他少数の型付き言語と同様に、
条件式の値が `true`や`false`以外の場合は、エラーになります。

```jldoctest
julia> if 1
           println("true")
       end
ERROR: TypeError: non-boolean (Int64) used in boolean context
```

```@raw html
<!--
This error indicates that the conditional was of the wrong type: [`Int64`](@ref) rather
than the required [`Bool`](@ref).
-->
```
このエラーは、条件式の値の型が、求められる[`Bool`](@ref)ではなく不当な[`Int64`](@ref)であることを示します。

```@raw html
<!--
The so-called "ternary operator", `?:`, is closely related to the `if`-`elseif`-`else` syntax,
but is used where a conditional choice between single expression values is required, as opposed
to conditional execution of longer blocks of code. It gets its name from being the only operator
in most languages taking three operands:
-->
```
いわゆる"三項演算子"の`?:`は`if`-`elseif`-`else`構文にとても近いですが、
条件式の選択が単一式の値からだけの場合に限られます。
長いブロックのコードを持つ条件文を実行する場合は使えません。
この名前の由来は、多くの言語で被演算子が３個の唯一の演算子だからです。

```julia
a ? b : c
```

```@raw html
<!--
The expression `a`, before the `?`, is a condition expression, and the ternary operation evaluates
the expression `b`, before the `:`, if the condition `a` is `true` or the expression `c`, after
the `:`, if it is `false`. Note that the spaces around `?` and `:` are mandatory: an expression
like `a?b:c` is not a valid ternary expression (but a newline is acceptable after both the `?` and
the `:`).
-->
```

`?`の前の式`a`は条件式で、`a`が`true`の時は、`:`の前の式`b`を評価し、`a`が`false`の時は式`c`を評価します。
`?`や`:`の周りの空白は必須である点に注意してください。
 `a?b:c`のように書いた式は、無効な三項演算子です。
 （しかし`?`や`:`のあとに改行を入れるのは構いません）

```@raw html
<!--
The easiest way to understand this behavior is to see an example. In the previous example, the
`println` call is shared by all three branches: the only real choice is which literal string to
print. This could be written more concisely using the ternary operator. For the sake of clarity,
let's try a two-way version first:
-->
```

この挙動を理解する一番簡単な方法は、例をみることです。
前述の例では、`println`の呼び出しは３つの分岐で共有しています。
実際に選択しているのは、印字する文字列リテラルです。
これは、三項演算子を使ってもっと簡潔に書くことができます。
もっとはっきりさせるために、先に２つの選択の場合をやってみましょう。



```jldoctest
julia> x = 1; y = 2;

julia> println(x < y ? "less than" : "not less than")
less than

julia> x = 1; y = 0;

julia> println(x < y ? "less than" : "not less than")
not less than
```

```@raw html
<!--
If the expression `x < y` is true, the entire ternary operator expression evaluates to the string
`"less than"` and otherwise it evaluates to the string `"not less than"`. The original three-way
example requires chaining multiple uses of the ternary operator together:
-->
```

式`x < y`が真の時は、文字列`"less than"`、そうではない場合は文字列`"not less than"`に
三項演算子全体が評価されます。
もともとの３選択の例には、三項演算子を複数連鎖させる必要があります。


```jldoctest
julia> test(x, y) = println(x < y ? "x is less than y"    :
                            x > y ? "x is greater than y" : "x is equal to y")
test (generic function with 1 method)

julia> test(1, 2)
x is less than y

julia> test(2, 1)
x is greater than y

julia> test(1, 1)
x is equal to y
```

```@raw html
<!--
To facilitate chaining, the operator associates from right to left.

It is significant that like `if`-`elseif`-`else`, the expressions before and after the `:` are
only evaluated if the condition expression evaluates to `true` or `false`, respectively:
-->
```
連鎖を簡単にするために、この演算子は右から左へと結合します。

重要なことですが、 `if`-`elseif`-`else`と同じように、`:`の前と後だけが条件式の評価値が`true`か`false`に従って評価されます。


```jldoctest
julia> v(x) = (println(x); x)
v (generic function with 1 method)

julia> 1 < 2 ? v("yes") : v("no")
yes
"yes"

julia> 1 > 2 ? v("yes") : v("no")
no
"no"
```

`[](## Short-Circuit Evaluation)
## 短絡評価

```@raw html
<!--
Short-circuit evaluation is quite similar to conditional evaluation. The behavior is found in
most imperative programming languages having the `&&` and `||` boolean operators: in a series
of boolean expressions connected by these operators, only the minimum number of expressions are
evaluated as are necessary to determine the final boolean value of the entire chain. Explicitly,
this means that:
-->
```

短絡評価は条件評価ととても良く似ています。
この挙動は、ほとんどの命令型言語が持っているブール値の演算子`&&`と`||`で見られます。
これらの演算子でつなげたブール式の中で、連鎖全体の最終的なブール値を決めるのに必要な最低限の数の式だけが評価されます。
この意味を具体的に書くと、

```@raw html
<!--
  * In the expression `a && b`, the subexpression `b` is only evaluated if `a` evaluates to `true`.
  * In the expression `a || b`, the subexpression `b` is only evaluated if `a` evaluates to `false`.
-->
```

  * 式`a && b`の部分式`b`は、`a`の評価が`true`の時だけ評価される。
  * 式`a || b`の部分式`b`は、`a`の評価が`false`の時だけ評価される。


```@raw html
<!--
The reasoning is that `a && b` must be `false` if `a` is `false`, regardless of the value of
`b`, and likewise, the value of `a || b` must be true if `a` is `true`, regardless of the value
of `b`. Both `&&` and `||` associate to the right, but `&&` has higher precedence than `||` does.
It's easy to experiment with this behavior:
-->
```
この論拠としては、`a && b`は`a`が`false`の時は`b`の値にかかわらず必ず`false`になり、同様に
`a && b`は`a`が`ture`の時は`b`の値にかかわらず必ず`true`からです。
`&&`と`||`は両方とも右結合ですが、`&&`のほうが `||`より優先順位が高いです。
この挙動は簡単に実験できます。


```jldoctest tandf
julia> t(x) = (println(x); true)
t (generic function with 1 method)

julia> f(x) = (println(x); false)
f (generic function with 1 method)

julia> t(1) && t(2)
1
2
true

julia> t(1) && f(2)
1
2
false

julia> f(1) && t(2)
1
false

julia> f(1) && f(2)
1
false

julia> t(1) || t(2)
1
true

julia> t(1) || f(2)
1
true

julia> f(1) || t(2)
1
2
true

julia> f(1) || f(2)
1
2
false
```

```@raw html
<!--
You can easily experiment in the same way with the associativity and precedence of various combinations
of `&&` and `||` operators.

This behavior is frequently used in Julia to form an alternative to very short `if` statements.
Instead of `if <cond> <statement> end`, one can write `<cond> && <statement>` (which could be
read as: <cond> *and then* <statement>). Similarly, instead of `if ! <cond> <statement> end`,
one can write `<cond> || <statement>` (which could be read as: <cond> *or else* <statement>).

For example, a recursive factorial routine could be defined like this:
-->
```

`&&`や`||`の様々な結合について、結合性や優先順位を、同じ方法で簡単に実験することができます。

この挙動は、Juliaではよく利用されて、とても短い`if`文の代わりになっています。
`if <条件> <文> end`, の代わりに`<条件> && <文>`と書くことができます。
これは（<条件>ならば<文>）と読むことができます。
同様に `if ! <条件> <文> end`は `<条件> || <文>`と書くことができます。
これは（<条件>でなけば<文>）と読むことができます。

例えば、再帰的な階乗の計算はこのように定義できます。

```jldoctest; filter = r"Stacktrace:(\n \[[0-9]+\].*)*"
julia> function fact(n::Int)
           n >= 0 || error("n must be non-negative")
           n == 0 && return 1
           n * fact(n-1)
       end
fact (generic function with 1 method)

julia> fact(5)
120

julia> fact(0)
1

julia> fact(-1)
ERROR: n must be non-negative
Stacktrace:
 [1] error at ./error.jl:33 [inlined]
 [2] fact(::Int64) at ./none:2
 [3] top-level scope
```

```@raw html
<!--
Boolean operations *without* short-circuit evaluation can be done with the bitwise boolean operators
introduced in [Mathematical Operations and Elementary Functions](@ref): `&` and `|`. These are
normal functions, which happen to support infix operator syntax, but always evaluate their arguments:
-->
```
短絡評価を **しない** ブール演算子は、[算術演算子と初等関数](@ref)で紹介したビット演算子の`&`と`|`を使って処理することができます。
これらは通常の関数で、たまたま中置記法の演算子を持ち、しかし引数を常に評価します。


```jldoctest tandf
julia> f(1) & t(2)
1
2
false

julia> t(1) | t(2)
1
2
true
```

```@raw html
<!--
Just like condition expressions used in `if`, `elseif` or the ternary operator, the operands of
`&&` or `||` must be boolean values (`true` or `false`). Using a non-boolean value anywhere except
for the last entry in a conditional chain is an error:
-->
```

`if`、`elseif`や三項演算子の中で使われる条件式と同じように、`&&`や`||`で使われる被演算子はブール値(`true`か `false`)
でなければなりません。
ブール値以外を条件連鎖の末尾以外で使うとエラーが発生します。


```jldoctest
julia> 1 && true
ERROR: TypeError: non-boolean (Int64) used in boolean context
```

```@raw html
<!--
On the other hand, any type of expression can be used at the end of a conditional chain. It will
be evaluated and returned depending on the preceding conditionals:
-->
```
一方、条件連鎖の末尾では、どんな型の式でも使えます。
これは先行する条件式に応じて評価され、戻り値として返されます。


```jldoctest
julia> true && (x = (1, 2, 3))
(1, 2, 3)

julia> false && (x = (1, 2, 3))
false
```

`[](## [Repeated Evaluation: Loops](@id man-loops))
## [反復評価：ループ](@id man-loops)

```@raw html
<!--
There are two constructs for repeated evaluation of expressions: the `while` loop and the `for`
loop. Here is an example of a `while` loop:
-->
```
式の反復評価をする構文は２つあります。
`while`ループと`for`ループです。
`while`ループの例を示します。


```jldoctest
julia> i = 1;

julia> while i <= 5
           println(i)
           global i += 1
       end
1
2
3
4
5
```

```@raw html
<!--
The `while` loop evaluates the condition expression (`i <= 5` in this case), and as long it remains
`true`, keeps also evaluating the body of the `while` loop. If the condition expression is `false`
when the `while` loop is first reached, the body is never evaluated.

The `for` loop makes common repeated evaluation idioms easier to write. Since counting up and
down like the above `while` loop does is so common, it can be expressed more concisely with a
`for` loop:
-->
```

`while`ループは条件式を評価し（この場合は`i <= 5`）、それが`true`である限り`while`ループの本体の評価を続けます。
条件式の評価が初めて`false`になった時、それ以降、本体の評価はまったく行いません。

`for`ループはよくある反復評価を簡単に書くための慣用表現です。
上記の`while`ループのようなカウントアップ・カウントダウンは、よく使うために、もっと簡潔な`for`ループで表現できるのです。


```jldoctest
julia> for i = 1:5
           println(i)
       end
1
2
3
4
5
```

```@raw html
<!--
Here the `1:5` is a range object, representing the sequence of numbers 1, 2, 3, 4, 5. The `for`
loop iterates through these values, assigning each one in turn to the variable `i`. One rather
important distinction between the previous `while` loop form and the `for` loop form is the scope
during which the variable is visible. If the variable `i` has not been introduced in another
scope, in the `for` loop form, it is visible only inside of the `for` loop, and not
outside/afterwards. You'll either need a new interactive session instance or a different variable
name to test this:
-->
```
ここで、`1:5`は範囲オブジェクトで、1, 2, 3, 4, 5という数列を表しています。
`for`ループはこれらの値に対する反復処理を行い、各値を変数`i`に代入します。
前述の`while`ループと形式と`for`ループ形式のかなり重要な違いは、変数の見えるスコープです。
変数`i`が別のスコープに導入されていない場合、`for`ループ形式では、`i`の見えるのは、for`ループの中だけで、外側や
for`ループ以降からは見えません。
よって検査をするためには、新しい対話セッションを始めるか、別の変数名を使う必要があります。



```jldoctest
julia> for j = 1:5
           println(j)
       end
1
2
3
4
5

julia> j
ERROR: UndefVarError: j not defined
```

```@raw html
<!--
See [Scope of Variables](@ref scope-of-variables) for a detailed explanation of variable scope and how it works in
Julia.

In general, the `for` loop construct can iterate over any container. In these cases, the alternative
(but fully equivalent) keyword `in` or `∈` is typically used instead of `=`, since it makes
the code read more clearly:
-->
```
Juliaでの変数のスコープと挙動の詳細な説明と[変数のスコープ](@ref scope-of-variables)を参照してください。

一般に、`for`ループ構文はどんなコンテナに対しても反復を行うことができます。
代替の（しかし完全に等価な）キーワードの`in`や`∈`は、`=`の代わりによく使われます。
というのも、コードがもっと分かりやすくなるからです。


```jldoctest
julia> for i in [1,4,0]
           println(i)
       end
1
4
0

julia> for s ∈ ["foo","bar","baz"]
           println(s)
       end
foo
bar
baz
```

```@raw html
<!--
Various types of iterable containers will be introduced and discussed in later sections of the
manual (see, e.g., [Multi-dimensional Arrays](@ref man-multi-dim-arrays)).

It is sometimes convenient to terminate the repetition of a `while` before the test condition
is falsified or stop iterating in a `for` loop before the end of the iterable object is reached.
This can be accomplished with the `break` keyword:
-->
```
様々なタイプのイテラブルなコンテナについて、マニュアルのあとのセクションで紹介と議論を行います。
（例えば [多次元配列](@ref man-multi-dim-arrays)を参照してください）

`while`ループで条件式の検査が偽になる前や、`for`ループがイテラブルなオブジェクトの最後に達する前に
終了できると便利なことがよくあります。
`break`キーワードを使うとこれを達成できます。

```jldoctest
julia> i = 1;

julia> while true
           println(i)
           if i >= 5
               break
           end
           global i += 1
       end
1
2
3
4
5

julia> for j = 1:1000
           println(j)
           if j >= 5
               break
           end
       end
1
2
3
4
5
```

```@raw html
<!--
Without the `break` keyword, the above `while` loop would never terminate on its own, and the `for` loop would iterate up to 1000. These loops are both exited early by using `break`.

In other circumstances, it is handy to be able to stop an iteration and move on to the next one
immediately. The `continue` keyword accomplishes this:
-->
```

`break`キーワードがなければ、上記の`while`ループは、決して勝手に終わらず、`for`ループは1000まで反復を行うでしょう。
これらのループは両方とも`break`を使って、早い段階で終了しています。

他の状況では、反復を止めて、すぐに次に移ることができると便利なことがあります。
`continue`キーワードによってこれを達成できます。


```jldoctest
julia> for i = 1:10
           if i % 3 != 0
               continue
           end
           println(i)
       end
3
6
9
```

```@raw html
<!--
This is a somewhat contrived example since we could produce the same behavior more clearly by
negating the condition and placing the `println` call inside the `if` block. In realistic usage
there is more code to be evaluated after the `continue`, and often there are multiple points from
which one calls `continue`.

Multiple nested `for` loops can be combined into a single outer loop, forming the cartesian product
of its iterables:
-->
```

この例はちょっと不自然です。
というのも、条件を否定し、`println`の呼び出しを`if`ブロックの中においたほうが、同じ挙動をもっと明快に実現できるからです。
現実的な用法では、`continue`のあとに評価すべきコードがもっとあって、`continue`を呼び出す箇所も複数あるでしょう。

多重にネストした`for`ループは、統合して、各イテラブルオブジェクトの直積に対する、1つの外側のループにすることができます。

```jldoctest
julia> for i = 1:2, j = 3:4
           println((i, j))
       end
(1, 3)
(1, 4)
(2, 3)
(2, 4)
```

```@raw html
<!--
With this syntax, iterables may still refer to outer loop variables; e.g. `for i = 1:n, j = 1:i`
is valid.
However a `break` statement inside such a loop exits the entire nest of loops, not just the inner one.
Both variables (`i` and `j`) are set to their current iteration values each time the inner loop runs.
Therefore, assignments to `i` will not be visible to subsequent iterations:
-->
```
この構文では、イテラブルが外側のループの変数を参照することが可能です。
例えば、`for i = 1:n, j = 1:i`は有効です。
しかし、このようなループの中にある`break`文によって、内側だけでなく、ネストしたループ全体を脱出します。
両方の変数(`i` と`j`)とも、内側のループが実行されるごとに、その回の値が代入されます。
そのため`i`に対する代入は以降の回の反復からは見えません。

```jldoctest
julia> for i = 1:2, j = 3:4
           println((i, j))
           i = 0
       end
(1, 3)
(1, 4)
(2, 3)
(2, 4)
```

```@raw html
<!--
If this example were rewritten to use a `for` keyword for each variable, then the output would
be different: the second and fourth values would contain `0`.
-->
```
この例で各変数ごとに`for`キーワード使うように書き直したとすると、出力は変わるでしょう。
2番目と4番目の出力は`0`を含むでしょう。

`[](## Exception Handling)
## 例外処理

```@raw html
<!--
When an unexpected condition occurs, a function may be unable to return a reasonable value to
its caller. In such cases, it may be best for the exceptional condition to either terminate the
program while printing a diagnostic error message, or if the programmer has provided code to handle
such exceptional circumstances then allow that code to take the appropriate action.
-->
```
不測の事態が発生した場合、関数は呼び出し側に応えて、適切な値を返すことができないかもしれません。
そんな場合、例外的な状況に対する最善策は、プログラムを終了する一方で、状況を診断するエラーメッセージを表示することかもしれないし、
例外的な事態に対処するコードが用意されている場合は、そのコードを適切に動作させることかもしれません。


`[](### Built-in `Exception`s)
### 組込みの`例外`

```@raw html
<!--
`Exception`s are thrown when an unexpected condition has occurred. The built-in `Exception`s listed
below all interrupt the normal flow of control.
-->
```
`例外`は不測の事態が発生した時に投げられます。
下記の組込みの`例外`のリストはすべて、通常の制御フローを中断します。


```@raw html
<!--
| `Exception`                   |
|:----------------------------- |
| [`ArgumentError`](@ref)       |
| [`BoundsError`](@ref)         |
| [`CompositeException`](@ref)  |
| [`DivideError`](@ref)         |
| [`DomainError`](@ref)         |
| [`EOFError`](@ref)            |
| [`ErrorException`](@ref)      |
| [`InexactError`](@ref)        |
| [`InitError`](@ref)           |
| [`InterruptException`](@ref)  |
| `InvalidStateException`       |
| [`KeyError`](@ref)            |
| [`LoadError`](@ref)           |
| [`OutOfMemoryError`](@ref)    |
| [`ReadOnlyMemoryError`](@ref) |
| [`RemoteException`](@ref)     |
| [`MethodError`](@ref)         |
| [`OverflowError`](@ref)       |
| [`Meta.ParseError`](@ref)     |
| [`SystemError`](@ref)         |
| [`TypeError`](@ref)           |
| [`UndefRefError`](@ref)       |
| [`UndefVarError`](@ref)       |
| [`StringIndexError`](@ref)    |

-->
```

| `例外`                   |
|:----------------------------- |
| [`ArgumentError`](@ref)       |
| [`BoundsError`](@ref)         |
| [`CompositeException`](@ref)  |
| [`DivideError`](@ref)         |
| [`DomainError`](@ref)         |
| [`EOFError`](@ref)            |
| [`ErrorException`](@ref)      |
| [`InexactError`](@ref)        |
| [`InitError`](@ref)           |
| [`InterruptException`](@ref)  |
| `InvalidStateException`       |
| [`KeyError`](@ref)            |
| [`LoadError`](@ref)           |
| [`OutOfMemoryError`](@ref)    |
| [`ReadOnlyMemoryError`](@ref) |
| [`RemoteException`](@ref)     |
| [`MethodError`](@ref)         |
| [`OverflowError`](@ref)       |
| [`Meta.ParseError`](@ref)     |
| [`SystemError`](@ref)         |
| [`TypeError`](@ref)           |
| [`UndefRefError`](@ref)       |
| [`UndefVarError`](@ref)       |
| [`StringIndexError`](@ref)    |

```@raw html
<!--
For example, the [`sqrt`](@ref) function throws a [`DomainError`](@ref) if applied to a negative
real value:
-->
```
例えば、[`sqrt`](@ref)関数は、負の実数に適用しようとすると [`DomainError`](@ref)を投げます。


```jldoctest
julia> sqrt(-1)
ERROR: DomainError with -1.0:
sqrt will only return a complex result if called with a complex argument. Try sqrt(Complex(x)).
Stacktrace:
[...]
```

```@raw html
<!--
You may define your own exceptions in the following way:
-->
```
以下のようにして、自分で例外を定義することができます。


```jldoctest
julia> struct MyCustomException <: Exception end
```

`[](### The [`throw`](@ref) function)
### [`throw`](@ref)関数

```@raw html
<!--
Exceptions can be created explicitly with [`throw`](@ref). For example, a function defined only
for nonnegative numbers could be written to [`throw`](@ref) a [`DomainError`](@ref) if the argument
is negative:
-->
```

例外は[`throw`](@ref)を使って明示的に生成することができます。
例えば、非負の数にのみ定義されている関数を、引数が負の時に [`DomainError`](@ref)を[`throw`](@ref)することで定義できます。


```jldoctest; filter = r"Stacktrace:(\n \[[0-9]+\].*)*"
julia> f(x) = x>=0 ? exp(-x) : throw(DomainError(x, "argument must be nonnegative"))
f (generic function with 1 method)

julia> f(1)
0.36787944117144233

julia> f(-1)
ERROR: DomainError with -1:
argument must be nonnegative
Stacktrace:
 [1] f(::Int64) at ./none:1
```

```@raw html
<!--
Note that [`DomainError`](@ref) without parentheses is not an exception, but a type of exception.
It needs to be called to obtain an `Exception` object:
-->
```

[`DomainError`](@ref)に括弧を付けないと例外ではなく、例外の型になります。
`Exception`オブジェクトを補足する時に必要となり呼び出されます。

```jldoctest
julia> typeof(DomainError(nothing)) <: Exception
true

julia> typeof(DomainError) <: Exception
false
```

```@raw html
<!--
Additionally, some exception types take one or more arguments that are used for error reporting:
-->
```
さらに、例外の型の中には、エラー報告のために、1個以上の引数を取るものがあります。

```jldoctest
julia> throw(UndefVarError(:x))
ERROR: UndefVarError: x not defined
```

```@raw html
<!--
This mechanism can be implemented easily by custom exception types following the way [`UndefVarError`](@ref)
is written:
-->
```
このしくみは、[`UndefVarError`](@ref)の書き方に従って、独自の型を作ると、簡単に実装できます。



```jldoctest
julia> struct MyUndefVarError <: Exception
           var::Symbol
       end

julia> Base.showerror(io::IO, e::MyUndefVarError) = print(io, e.var, " not defined")
```

```@raw html
<!--
!!! note
    When writing an error message, it is preferred to make the first word lowercase. For example,
    `size(A) == size(B) || throw(DimensionMismatch("size of A not equal to size of B"))`

    is preferred over

    `size(A) == size(B) || throw(DimensionMismatch("Size of A not equal to size of B"))`.

    However, sometimes it makes sense to keep the uppercase first letter, for instance if an argument
    to a function is a capital letter: `size(A,1) == size(B,2) || throw(DimensionMismatch("A has first dimension..."))`.

-->
```

!!! 注意
    エラーメッセージを書く時は、小文字で始めるのが好ましいです。例えば、

    `size(A) == size(B) || throw(DimensionMismatch("size of A not equal to size of B"))`

    の方が、下記のものより好ましいです。

    `size(A) == size(B) || throw(DimensionMismatch("Size of A not equal to size of B"))`

    しかし、意図的に、出だしの文字を大文字のままにする場合もたまにあります。
    例えば、関数の引数が大文字の場合の時です。

     `size(A,1) == size(B,2) || throw(DimensionMismatch("A has first dimension..."))`


### エラー

```@raw html
<!--
The [`error`](@ref) function is used to produce an [`ErrorException`](@ref) that interrupts
the normal flow of control.

Suppose we want to stop execution immediately if the square root of a negative number is taken.
To do this, we can define a fussy version of the [`sqrt`](@ref) function that raises an error
if its argument is negative:
-->
```

[`error`](@ref)関数は、通常の制御フローを中断する[`ErrorException`](@ref)を生成するために利用されます。

平方根の関数の引数に負の数を受け取ると即座に実行を停止したいとします。
これを行うために、引数が負の時にエラーをおこす小うるさい[`sqrt`](@ref)を定義できます。


```jldoctest fussy_sqrt; filter = r"Stacktrace:(\n \[[0-9]+\].*)*"
julia> fussy_sqrt(x) = x >= 0 ? sqrt(x) : error("negative x not allowed")
fussy_sqrt (generic function with 1 method)

julia> fussy_sqrt(2)
1.4142135623730951

julia> fussy_sqrt(-1)
ERROR: negative x not allowed
Stacktrace:
 [1] error at ./error.jl:33 [inlined]
 [2] fussy_sqrt(::Int64) at ./none:1
 [3] top-level scope
```

```@raw html
<!--
If `fussy_sqrt` is called with a negative value from another function, instead of trying to continue
execution of the calling function, it returns immediately, displaying the error message in the
interactive session:
-->
```
`fussy_sqrt`が負の数と共に、他の関数から呼ばれると、関数を呼び出して実行しようとする代わりに、
即座に終了して対話セッションにエラーメッセージを表示します。 


```jldoctest fussy_sqrt; filter = r"Stacktrace:(\n \[[0-9]+\].*)*"
julia> function verbose_fussy_sqrt(x)
           println("before fussy_sqrt")
           r = fussy_sqrt(x)
           println("after fussy_sqrt")
           return r
       end
verbose_fussy_sqrt (generic function with 1 method)

julia> verbose_fussy_sqrt(2)
before fussy_sqrt
after fussy_sqrt
1.4142135623730951

julia> verbose_fussy_sqrt(-1)
before fussy_sqrt
ERROR: negative x not allowed
Stacktrace:
 [1] error at ./error.jl:33 [inlined]
 [2] fussy_sqrt at ./none:1 [inlined]
 [3] verbose_fussy_sqrt(::Int64) at ./none:3
 [4] top-level scope
```

`[](### The `try/catch` statement)
### `try/catch` 文

```@raw html
<!--
The `try/catch` statement allows for `Exception`s to be tested for. For example, a customized
square root function can be written to automatically call either the real or complex square root
method on demand using `Exception`s :
-->
```
`try/catch`文では、`例外`を試行することができます。
例えば、`例外`を使って、実数と複素数のどちらかの平方根メソッドを要求次第で自動で呼び出す、独自の平方根関数を書くことができます。


```jldoctest
julia> f(x) = try
           sqrt(x)
       catch
           sqrt(complex(x, 0))
       end
f (generic function with 1 method)

julia> f(1)
1.0

julia> f(-1)
0.0 + 1.0im
```

```@raw html
<!--
It is important to note that in real code computing this function, one would compare `x` to zero
instead of catching an exception. The exception is much slower than simply comparing and branching.

`try/catch` statements also allow the `Exception` to be saved in a variable. The following
contrived example calculates the square root of the second element of `x` if `x`
is indexable, otherwise assumes `x` is a real number and returns its square root:
-->
```

重要なので注意しておきたい点は、関数を実際のコードの中で計算する時は、例外を補足するのではなくて、`x`を０と比較する点です。
単に比較して分岐するよりも、はるかに例外は遅いのです。

`try/catch`文では、`例外`を変数に保存することができます。
以下の不自然な例では、`x`にインデックスがある場合には、`x`の第２要素の平方根を計算し、
そうでなければ`x`を実数とみなしてその平方根を返します。

```jldoctest
julia> sqrt_second(x) = try
           sqrt(x[2])
       catch y
           if isa(y, DomainError)
               sqrt(complex(x[2], 0))
           elseif isa(y, BoundsError)
               sqrt(x)
           end
       end
sqrt_second (generic function with 1 method)

julia> sqrt_second([1 4])
2.0

julia> sqrt_second([1 -4])
0.0 + 2.0im

julia> sqrt_second(9)
3.0

julia> sqrt_second(-9)
ERROR: DomainError with -9.0:
sqrt will only return a complex result if called with a complex argument. Try sqrt(Complex(x)).
Stacktrace:
[...]
```

```@raw html
<!--
Note that the symbol following `catch` will always be interpreted as a name for the exception,
so care is needed when writing `try/catch` expressions on a single line. The following code will
*not* work to return the value of `x` in case of an error:
-->
```

`catch`に続く記号は常に例外の名前として解釈されることに注意してください。
そのため、`try/catch`式を１行で書く場合は、注意が必要です。
以下のコードはエラーの場合には`x`の値を返しません。


```julia
try bad() catch x end
```

```@raw html
<!--
Instead, use a semicolon or insert a line break after `catch`:
-->
```
代替策として、セミコロンを使ったり、`catch`のあとに改行を入れたりします。

```julia
try bad() catch; x end

try bad()
catch
    x
end
```

```@raw html
<!--
The power of the `try/catch` construct lies in the ability to unwind a deeply nested computation
immediately to a much higher level in the stack of calling functions. There are situations where
no error has occurred, but the ability to unwind the stack and pass a value to a higher level
is desirable. Julia provides the [`rethrow`](@ref), [`backtrace`](@ref) and [`catch_backtrace`](@ref)
functions for more advanced error handling.
-->
```

`try/catch`構文の威力は、深くネストした計算を直ちに巻き戻して、
関数呼び出しスタックのはるかに高水準まで戻って来ることが可能な点にあります。



`[](### `finally` Clauses)
### `finally` 節

```@raw html
<!--
In code that performs state changes or uses resources like files, there is typically clean-up
work (such as closing files) that needs to be done when the code is finished. Exceptions potentially
complicate this task, since they can cause a block of code to exit before reaching its normal
end. The `finally` keyword provides a way to run some code when a given block of code exits, regardless
of how it exits.
-->
```

状態の変化やファイルのようなリソースの使用を伴うコードには、通常、コードの終了時にすべき整理作業(ファイルを閉じるなど)
があります。
例外があるとこの作業が複雑になる可能性があります。というのも、最後に達して正常終了する前に、例外がコードブロックを実行することもあるからです。
`finally`キーワードは、どのように終了しようとも、コードブロックが終了する前に、何らかのコードを実行する手段を提供します。


```@raw html
<!--
For example, here is how we can guarantee that an opened file is closed:
-->
```
ここで、開いたファイルを必ず閉じることを保証する例を挙げます。


```julia
f = open("file")
try
    # operate on file f
finally
    close(f)
end
```

```@raw html
<!--
When control leaves the `try` block (for example due to a `return`, or just finishing normally),
`close(f)` will be executed. If the `try` block exits due to an exception, the exception will
continue propagating. A `catch` block may be combined with `try` and `finally` as well. In this
case the `finally` block will run after `catch` has handled the error.
-->
```
制御が`try`ブロックを離れる時（例えば`return`による場合、正常終了の場合など）に、`close(f)` が実行されます。
`try`ブロックが例外によって終了する場合、例外は伝播を続けます。
`catch`ブロックを`try`や`finally`と組み合わせても構いません。
この場合は、`finally`ブロックは`catch`がエラー処理をしたあとに実行されます。


`[](## [Tasks (aka Coroutines)](@id man-tasks))
## [タスク (別名　コルーチン)](@id man-tasks)

```@raw html
<!--
Tasks are a control flow feature that allows computations to be suspended and resumed in a flexible
manner. This feature is sometimes called by other names, such as symmetric coroutines, lightweight
threads, cooperative multitasking, or one-shot continuations.
-->
```
タスクは計算の中断や再開を柔軟に行うことを可能にする制御フローの機能です。
この機能は、対称コルーチン、軽量スレッド、協調的マルチタスク、ワンショット継続などの別名でよばれることもあります。



```@raw html
<!--
When a piece of computing work (in practice, executing a particular function) is designated as
a [`Task`](@ref), it becomes possible to interrupt it by switching to another [`Task`](@ref).
The original [`Task`](@ref) can later be resumed, at which point it will pick up right where it
left off. At first, this may seem similar to a function call. However there are two key differences.
First, switching tasks does not use any space, so any number of task switches can occur without
consuming the call stack. Second, switching among tasks can occur in any order, unlike function
calls, where the called function must finish executing before control returns to the calling function.
-->
```
ひとまとまりの計算作業(実のところ、特定の関数の実行)を[`Task`](@ref)に指定して実行すると、
これを中断して別の [`Task`](@ref)に切り替えることができます。
もともとの [`Task`](@ref)は後で中断したところから再開することができます。
一見、これは関数呼び出しと同じように見えるかもしれません。
しかし、２つの重要な違いがあります。
まず、タスクの切り替えにはメモリ領域を使用しません。
このため、切り替えるタスクの数をいくら増やしても、コールスタックを消費しません。
次に、タスクの切り替えは、どんな順番でもよく、関数呼び出しとは異なります。
関数呼び出しの場合は、呼び出される関数は、呼び出す関数に制御が戻る前に、実行を終了する必要があります。




```@raw html
<!--
This kind of control flow can make it much easier to solve certain problems. In some problems,
the various pieces of required work are not naturally related by function calls; there is no obvious
"caller" or "callee" among the jobs that need to be done. An example is the producer-consumer
problem, where one complex procedure is generating values and another complex procedure is consuming
them. The consumer cannot simply call a producer function to get a value, because the producer
may have more values to generate and so might not yet be ready to return. With tasks, the producer
and consumer can both run as long as they need to, passing values back and forth as necessary.
-->
```
この種の制御フローのを使うと、ある種の問題は簡単に解決できます。
ある種の問題では、関数呼び出しでは、様々な種類の作業を、自然に関連付けることができません。
なすべき仕事の「呼び出す側」と「呼び出される側」がはっきりしないものがあります。
例として挙げる「生産者/消費者」問題では、複雑な処理が値を生成する一方で、別の複雑な処理がそれを消費します。
消費者は値を得るために、単に生産者関数を呼び出せばいいわけではありません。
生産者には他にも生産すべき値があり、まだ値を返す準備ができていないかもしれないからです。
タスクを使うと、生産者と消費者は必要に応じて値をやり取りしながら、両者とも必要なだけ作動することができます。


```@raw html
<!--
Julia provides a [`Channel`](@ref) mechanism for solving this problem.
A [`Channel`](@ref) is a waitable first-in first-out queue which can have
multiple tasks reading from and writing to it.

Let's define a producer task, which produces values via the [`put!`](@ref) call.
To consume values, we need to schedule the producer to run in a new task. A special [`Channel`](@ref)
constructor which accepts a 1-arg function as an argument can be used to run a task bound to a channel.
We can then [`take!`](@ref) values repeatedly from the channel object:
-->
```
Juliaにはこの問題を解決するために[`Channel`](@ref)の仕組みがあります。
[`Channel`](@ref)は待機可能な先入先出のキューで複数のタスクを読取り・書込みが可能です。

生産者タスクを定義しましょう。
これは、[`put!`](@ref)の呼び出しによって値の生産を行います。
値を消費するには、生産者が新しいタスクを実行するようにスケジュールする必要があります。
１引数の関数を引数とする[`Channel`](@ref)の特殊なコンストラクタを使って、チャネルに束縛したタスクを実行することができます。
[`take!`](@ref)を使ってチャネルオブジェクトがら繰り返し値を取得することができます。



```jldoctest producer
julia> function producer(c::Channel)
           put!(c, "start")
           for n=1:4
               put!(c, 2n)
           end
           put!(c, "stop")
       end;

julia> chnl = Channel(producer);

julia> take!(chnl)
"start"

julia> take!(chnl)
2

julia> take!(chnl)
4

julia> take!(chnl)
6

julia> take!(chnl)
8

julia> take!(chnl)
"stop"
```

```@raw html
<!--
One way to think of this behavior is that `producer` was able to return multiple times. Between
calls to [`put!`](@ref), the producer's execution is suspended and the consumer has control.

The returned [`Channel`](@ref) can be used as an iterable object in a `for` loop, in which case the
loop variable takes on all the produced values. The loop is terminated when the channel is closed.
-->
```

「生産者」は何回も値を返すことができる、というのはこの挙動の解釈の１つです。
[`put!`](@ref)の呼び出しの合間で、生産者の実行は中断し、制御が消費者に移ります。

戻り値の[`Channel`](@ref)は`for`ループの中でイテラブルオブジェクトとして利用可能で、
この場合、ループの変数は、生産される値すべてを取ります。
チャネルが閉じるとループは終了します。

```jldoctest producer
julia> for x in Channel(producer)
           println(x)
       end
start
2
4
6
8
stop
```

```@raw html
<!--
Note that we did not have to explicitly close the channel in the producer. This is because
the act of binding a [`Channel`](@ref) to a [`Task`](@ref) associates the open lifetime of
a channel with that of the bound task. The channel object is closed automatically when the task
terminates. Multiple channels can be bound to a task, and vice-versa.

While the [`Task`](@ref) constructor expects a 0-argument function, the [`Channel`](@ref)
method which creates a channel bound task expects a function that accepts a single argument of
type [`Channel`](@ref). A common pattern is for the producer to be parameterized, in which case a partial
function application is needed to create a 0 or 1 argument [anonymous function](@ref man-anonymous-functions).

For [`Task`](@ref) objects this can be done either directly or by use of a convenience macro:
-->
```
生産者のチャネルを明示的に閉じる必要はないことに注意してください。
これは、[`Channel`](@ref)が[`Task`](@ref)を束縛しているために、
チャネルの開いている生涯期間が、束縛したタスクの生涯期間に関連付けられているからです。
チャネルオブジェクトはタスクが終了すると自動的に閉じられます。
タスクは複数のチャネルに束縛可能で、逆も成り立ちます。

[`Task`](@ref)のコンストラクタの引数は、引数0個の関数ですが、
[`Channel`](@ref)のメソッドは、[`Channel`](@ref)型の引数1個を持つ関数で、
タスクを束縛したチャネルを生成します。
生産者をパラメータ化することは、よくあるパターンですが、この場合は引数が0個または１個の[無名関数](@ref man-anonymous-functions)
を作るために部分関数の適用が必要です。

[`Task`](@ref)オブジェクトに対して、これは、直接、または便利なマクロを使って行います。



```julia
function mytask(myarg)
    ...
end

taskHdl = Task(() -> mytask(7))
# or, equivalently
taskHdl = @task mytask(7)
```

```@raw html
<!--
To orchestrate more advanced work distribution patterns, [`bind`](@ref) and [`schedule`](@ref)
can be used in conjunction with [`Task`](@ref) and [`Channel`](@ref)
constructors to explicitly link a set of channels with a set of producer/consumer tasks.

Note that currently Julia tasks are not scheduled to run on separate CPU cores.
True kernel threads are discussed under the topic of [Parallel Computing](@ref).
-->
```
より高度な作業分配パターンを編成するために、[`bind`](@ref) や[`schedule`](@ref)を
[`Task`](@ref)や[`Channel`](@ref)と一緒に使って、チャネルの集合と生産者/消費者のタスクの集合を、
明示的に連携させることができます。

現時点では、Juliaのタスクは、別々のCPUのコアにスケジュールされない点に注意してください。
真のカーネルスレッドの関しては[並列コンピューティング](@ref)のトピックで議論します。



　`[](　### Core task operations)
### 核となるタスク演算子

```@raw html
<!--
Let us explore the low level construct [`yieldto`](@ref) to understand how task switching works.
`yieldto(task,value)` suspends the current task, switches to the specified `task`, and causes
that task's last [`yieldto`](@ref) call to return the specified `value`. Notice that [`yieldto`](@ref)
is the only operation required to use task-style control flow; instead of calling and returning
we are always just switching to a different task. This is why this feature is also called "symmetric
coroutines"; each task is switched to and from using the same mechanism.
-->
```


```@raw html
<!--
[`yieldto`](@ref) is powerful, but most uses of tasks do not invoke it directly. Consider why
this might be. If you switch away from the current task, you will probably want to switch back
to it at some point, but knowing when to switch back, and knowing which task has the responsibility
of switching back, can require considerable coordination. For example, [`put!`](@ref) and [`take!`](@ref)
are blocking operations, which, when used in the context of channels maintain state to remember
who the consumers are. Not needing to manually keep track of the consuming task is what makes [`put!`](@ref)
easier to use than the low-level [`yieldto`](@ref).
-->
```

```@raw html
<!--
In addition to [`yieldto`](@ref), a few other basic functions are needed to use tasks effectively.

  * [`current_task`](@ref) gets a reference to the currently-running task.
  * [`istaskdone`](@ref) queries whether a task has exited.
  * [`istaskstarted`](@ref) queries whether a task has run yet.
  * [`task_local_storage`](@ref) manipulates a key-value store specific to the current task.
-->
```


`[](### Tasks and events)
### タスクとイベント

```@raw html
<!--
Most task switches occur as a result of waiting for events such as I/O requests, and are performed
by a scheduler included in Julia Base. The scheduler maintains a queue of runnable tasks,
and executes an event loop that restarts tasks based on external events such as message arrival.

The basic function for waiting for an event is [`wait`](@ref). Several objects implement [`wait`](@ref);
for example, given a `Process` object, [`wait`](@ref) will wait for it to exit. [`wait`](@ref)
is often implicit; for example, a [`wait`](@ref) can happen inside a call to [`read`](@ref)
to wait for data to be available.
-->
```


```@raw html
<!--
In all of these cases, [`wait`](@ref) ultimately operates on a [`Condition`](@ref) object, which
is in charge of queueing and restarting tasks. When a task calls [`wait`](@ref) on a [`Condition`](@ref),
the task is marked as non-runnable, added to the condition's queue, and switches to the scheduler.
The scheduler will then pick another task to run, or block waiting for external events. If all
goes well, eventually an event handler will call [`notify`](@ref) on the condition, which causes
tasks waiting for that condition to become runnable again.

A task created explicitly by calling [`Task`](@ref) is initially not known to the scheduler. This
allows you to manage tasks manually using [`yieldto`](@ref) if you wish. However, when such
a task waits for an event, it still gets restarted automatically when the event happens, as you
would expect. It is also possible to make the scheduler run a task whenever it can, without necessarily
waiting for any events. This is done by calling [`schedule`](@ref), or using the [`@async`](@ref)
macro (see [Parallel Computing](@ref) for more details).
-->
```


`[](### Task states)
### タスクの状態

```@raw html
<!--
Tasks have a `state` field that describes their execution status. A [`Task`](@ref) `state` is one of the following
symbols:
-->
```


```@raw html
<!--
| Symbol      | Meaning                                            |
|:----------- |:-------------------------------------------------- |
| `:runnable` | Currently running, or available to be switched to  |
| `:waiting`  | Blocked waiting for a specific event               |
| `:queued`   | In the scheduler's run queue about to be restarted |
| `:done`     | Successfully finished executing                    |
| `:failed`   | Finished with an uncaught exception                |
-->
```

| Symbol      | Meaning                                            |
|:----------- |:-------------------------------------------------- |
| `:runnable` | Currently running, or available to be switched to  |
| `:waiting`  | Blocked waiting for a specific event               |
| `:queued`   | In the scheduler's run queue about to be restarted |
| `:done`     | Successfully finished executing                    |
| `:failed`   | Finished with an uncaught exception                |
