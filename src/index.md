<<<<<<< HEAD
# Julia 1.0 Documentation

Welcome to the documentation for Julia 1.0.


=======
```@eval
io = IOBuffer()
release = isempty(VERSION.prerelease)
v = "$(VERSION.major).$(VERSION.minor)"
!release && (v = v*"-$(first(VERSION.prerelease))")
print(io, """
    # Julia $(v) ドキュメント

    Julia $(v)　のドキュメントへようこそ。

    """)
if !release
    print(io,"""
        !!! 警告 "進行中!"
            このドキュメントは、リリース前で策定中のJuliaのバージョンのものです。
        """)
end
import Markdown
Markdown.parse(String(take!(io)))
```
>>>>>>> tmp

```@raw html
<!--
Please read the [release blog post](https://julialang.org/blog/2018/08/one-point-zero) for a general overview of the language and
many of the changes since Julia v0.6. Note that version 0.7 was released alongside
1.0 to provide an upgrade path for packages and code that predates the 1.0 release.
The only difference between 0.7 and 1.0 is the removal of deprecation warnings.
For a complete list of all the changes since 0.6, see the [release notes for version 0.7](https://docs.julialang.org/en/v0.7.0/NEWS/)
-->
```
[リリースのブログ投稿](https://julialang.org/blog/2018/08/one-point-zero)を読んで、
juliaの概要をつかみ、v0.6との違いを知りましょう。
バージョン1.0と並行してバージョン0.7がリリースされていますが、これはバージョン1.0以前のパッケージやコードの
アップデート用です。
1.0と0.7の違いは非推奨の警告を消しているかどうかだけです。
0.6との相違点すべての完全なリストは [バージョン0.7のリリースノート](https://docs.julialang.org/en/v0.7.0/NEWS/)
をみて下さい。

### [Introduction](@id man-introduction)

`[](### [Introduction](@id man-introduction))
### [まえおき]](@id man-introduction)

```@raw html
<!--
Scientific computing has traditionally required the highest performance, yet domain experts have
largely moved to slower dynamic languages for daily work. We believe there are many good reasons
to prefer dynamic languages for these applications, and we do not expect their use to diminish.
Fortunately, modern language design and compiler techniques make it possible to mostly eliminate
the performance trade-off and provide a single environment productive enough for prototyping and
efficient enough for deploying performance-intensive applications. The Julia programming language
fills this role: it is a flexible dynamic language, appropriate for scientific and numerical computing,
with performance comparable to traditional statically-typed languages.
-->
```

科学的な計算には昔から高パフォーマンスが必要でしたが、日常的な作業を行う場合は、
各領域の専門家もずっと遅い動的言語におおかた移行してしまいました。
そうした用途には動的言語が好まれる理由が数多くあるので、利用が減ることはないでしょう。
幸い、現代的な言語設計とコンパイラ技術を使えば、パフォーマンスのトレードオフをほとんど解消できるので、
試験的開発に向いた生産性と、パフォーマンス重視のアプリケーションの利用に向いた効率性を兼ね備えた
単一の開発環境を実現できます。
Julia言語ならその役割を果たせます。
柔軟な動的言語でありながら、静的型付言語に匹敵するパーフォーマンスを持ち、科学的・数値的計算に向いているのです。


```@raw html
<!--
Because Julia's compiler is different from the interpreters used for languages like Python or
R, you may find that Julia's performance is unintuitive at first. If you find that something is
slow, we highly recommend reading through the [Performance Tips](@ref man-performance-tips) section before trying anything
else. Once you understand how Julia works, it's easy to write code that's nearly as fast as C.
-->
```

JuliaのコンパイラはPythonやRなどに使われるインタプリタと異なるので、
初めはJuliaのパフォーマンスは直観に反するかもしれません。
何か遅いと思ったら 、いろいろ試行錯誤するより、まず[パフォーマンス・ティップス](@ref man-performance-tips)を通読することを
お薦めします。
一旦Juliaの動き方を理解すれば、C言語に迫る速さのコードを書くのも容易でしょう。



```@raw html
<!--
Julia features optional typing, multiple dispatch, and good performance, achieved using type inference
and [just-in-time (JIT) compilation](https://en.wikipedia.org/wiki/Just-in-time_compilation),
implemented using [LLVM](https://en.wikipedia.org/wiki/Low_Level_Virtual_Machine). It is multi-paradigm,
combining features of imperative, functional, and object-oriented programming. Julia provides
ease and expressiveness for high-level numerical computing, in the same way as languages such
as R, MATLAB, and Python, but also supports general programming. To achieve this, Julia builds
upon the lineage of mathematical programming languages, but also borrows much from popular dynamic
languages, including [Lisp](https://en.wikipedia.org/wiki/Lisp_(programming_language)), [Perl](https://en.wikipedia.org/wiki/Perl_(programming_language)),
[Python](https://en.wikipedia.org/wiki/Python_(programming_language)), [Lua](https://en.wikipedia.org/wiki/Lua_(programming_language)),
and [Ruby](https://en.wikipedia.org/wiki/Ruby_(programming_language)).

-->
```
Juliaの目玉となる特徴としては、省略可能な型付け、多重ディスパッチ、高パフォーマンスがあります。
この性能は、型推論と[ジャスト-イン-タイム (JIT) コンパイル](https://en.wikipedia.org/wiki/Just-in-time_compilation)によって
達成され、[LLVM](https://en.wikipedia.org/wiki/Low_Level_Virtual_Machine)を使って実装されています。
Juliaはマルチパラダイム言語で、手続型、関数型、オブジェクト指向プログラムをかけ合わせています。
Juliaでの高水準の数値計算が、容易で表現力も高いのは、R・Matlab・Pythonといった言語と同様ですが、
汎用的なプログラミングも可能です。
これは、Juliaが数学的プログラム言語の系譜にありながら、人気の動的言語から多くを借り入れているからです。
 例を挙げると、[Lisp](https://en.wikipedia.org/wiki/Lisp_(programming_language)), [Perl](https://en.wikipedia.org/wiki/Perl_(programming_language)),
[Python](https://en.wikipedia.org/wiki/Python_(programming_language)), [Lua](https://en.wikipedia.org/wiki/Lua_(programming_language)),
 [Ruby](https://en.wikipedia.org/wiki/Ruby_(programming_language)) などがあります。


```@raw html
<!--
The most significant departures of Julia from typical dynamic languages are:

  * The core language imposes very little; Julia Base and the standard library is written in Julia itself, including
    primitive operations like integer arithmetic
  * A rich language of types for constructing and describing objects, that can also optionally be
    used to make type declarations
  * The ability to define function behavior across many combinations of argument types via [multiple dispatch](https://en.wikipedia.org/wiki/Multiple_dispatch)
  * Automatic generation of efficient, specialized code for different argument types
  * Good performance, approaching that of statically-compiled languages like C

-->
```

典型的な動的言語とJuliaとの著しい違いは

  * コア言語の責務は極めて小さく、Base・StandardライブラリはJulia自身で書かれています。整数の算術演算などの原始的な演算がそうです。
  * オブジェクトを構成・記述するための豊富な型があり、必要に応じて型宣言することもできます。
  * [多重ディスパッチ](https://en.wikipedia.org/wiki/Multiple_dispatch)を使って、関数を多くの引数の型の組み合わせに対して
    定義することができます。
  * 異なる引数の型に対して効率的で特化されたコードが自動生成されます。
  * パフォーマンスが高く、C言語のような静的なコンパイル言語に迫るほどです。


```@raw html
<!--
Although one sometimes speaks of dynamic languages as being "typeless", they are definitely not:
every object, whether primitive or user-defined, has a type. The lack of type declarations in
most dynamic languages, however, means that one cannot instruct the compiler about the types of
values, and often cannot explicitly talk about types at all. In static languages, on the other
hand, while one can -- and usually must -- annotate types for the compiler, types exist only at
compile time and cannot be manipulated or expressed at run time. In Julia, types are themselves
run-time objects, and can also be used to convey information to the compiler.

-->
```
動的言語には「型がない」という人もいますが、そんなことはありません。
すべてのオブジェクトは、原始的であれ、ユーザー定義型であれ、型があります。
しかしたいていの動的言語では、型宣言をしなければコンパイラに値の型を伝えることができません。
明示的に宣言できないものも多数あります。
一方、静的言語ではコンパイラに対して型注釈をつけることが可能(たいていは必須)です。
型はコンパイル時にのみ存在し、実行時には操作・表現することができません。
Juliaでは、型は実行時のオブジェクトであり、コンパイラに情報を伝えることができます。


```@raw html
<!--
While the casual programmer need not explicitly use types or multiple dispatch, they are the core
unifying features of Julia: functions are defined on different combinations of argument types,
and applied by dispatching to the most specific matching definition. This model is a good fit
for mathematical programming, where it is unnatural for the first argument to "own" an operation
as in traditional object-oriented dispatch. Operators are just functions with special notation
-- to extend addition to new user-defined data types, you define new methods for the `+` function.
Existing code then seamlessly applies to the new data types.
-->
```

ちょっとプログラムを書きたいだけなら、わざわざ使う必要のない型や多重ディスパッチですが、
実はJuliaの特徴を統合する中核です。
関数は引数の型のいろいろな組み合わせに対して定義され、最も特化・適合したものが選択・適用されます。
この方式は数学的なプログラミングと相性がいいです。
1番目の引数が演算を「所有」するという従来のオブジェクト指向的なディスパッチは不自然でしょう。
演算子は記法の特殊な単なる関数です。
足し算を新しいユーザー定義のデータ型に拡張するには、関数`+`に対して新しいメソッドを定義します。
すると既存のコードは自然と新しいデータ型に適用されます。



```@raw html
<!--
Partly because of run-time type inference (augmented by optional type annotations), and partly
because of a strong focus on performance from the inception of the project, Julia's computational
efficiency exceeds that of other dynamic languages, and even rivals that of statically-compiled
languages. For large scale numerical problems, speed always has been, continues to be, and probably
always will be crucial: the amount of data being processed has easily kept pace with Moore's Law
over the past decades.

-->
```

(必要に応じて型注釈を補った)実行時の型推論や、プロジェクト発足以来のパフォーマンスの強化に焦点を当てたおかげで、
Juliaの計算効率は他の動的言語、さらにライバルの静的コンパイル言語を勝るほどです。
スピードは、大規模な数値問題において、現在に至るまで、おそらくは今後においても常に重要です。
処理すべきデータ量は、過去数十年間にわたってムーアの法則をみたすような増加速度を保っています。



```@raw html
<!--
Julia aims to create an unprecedented combination of ease-of-use, power, and efficiency in a single
language. In addition to the above, some advantages of Julia over comparable systems include:

  * Free and open source ([MIT licensed](https://github.com/JuliaLang/julia/blob/master/LICENSE.md))
  * User-defined types are as fast and compact as built-ins
  * No need to vectorize code for performance; devectorized code is fast
  * Designed for parallelism and distributed computation
  * Lightweight "green" threading ([coroutines](https://en.wikipedia.org/wiki/Coroutine))
  * Unobtrusive yet powerful type system
  * Elegant and extensible conversions and promotions for numeric and other types
  * Efficient support for [Unicode](https://en.wikipedia.org/wiki/Unicode), including but not limited
    to [UTF-8](https://en.wikipedia.org/wiki/UTF-8)
  * Call C functions directly (no wrappers or special APIs needed)
  * Powerful shell-like capabilities for managing other processes
  * Lisp-like macros and other metaprogramming facilities

-->
```

Juliaは、使いやすさ・力・効率という前例のない組み合わせを単一の言語で創りだそうとしています。
さらに、他の言語に対する長所をあげてみます。

  * フリーでオープンソース ([MIT ライセンス](https://github.com/JuliaLang/julia/blob/master/LICENSE.md))
  * ユーザー定義型が組み込み型のように速くてコンパクト
  * パフォーマンスのためにベクトル化する必要がない。ベクトル化しないコードも速い
  * 並列・分散計算を考慮した設計
  * 軽量の "グリーン" スレッディング ([コルーチン](https://en.wikipedia.org/wiki/Coroutine))
  * 控え目ながら強力な型システム
  * 数値その他の型に対する、エレガントで拡張可能な型変換・型昇格
  *  [ユニコード](https://en.wikipedia.org/wiki/Unicode)を効率的に利用可能、 
    [UTF-8](https://en.wikipedia.org/wiki/UTF-8)も利用可能だがこれだけではない。
  * C言語の関数を直接呼び出せる(ラッパーや特殊なAPIを必要としない)
  * 他のプロセスをシェルのように強力に管理できる
  * Lispのようにマクロやメタプログラミングを利用できる