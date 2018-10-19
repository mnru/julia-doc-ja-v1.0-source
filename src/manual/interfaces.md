`[](# Interfaces)
# インターフェイス

```@raw html
<!--
A lot of the power and extensibility in Julia comes from a collection of informal interfaces.
 By extending a few specific methods to work for a custom type, objects of that type not only
receive those functionalities, but they are also able to be used in other methods that are written
to generically build upon those behaviors.
-->
```

Juliaは具象化されていない部分の残ったインターフェースの集まりによって、力と拡張性を得ています。
独自の型に特化して拡張すると、その型のオブジェクトは直接拡張した機能が使えるだけでなく、
汎用的に記述されたメソッドにもその機能が組み込まれて利用することができます。


`[](## [Iteration](@id man-interface-iteration))
## [反復](@id man-interface-iteration)

```@raw html
<!--
| Required methods               |                        | Brief description                                                                     |
|:------------------------------ |:---------------------- |:------------------------------------------------------------------------------------- |
| `iterate(iter)`                |                        | Returns either a tuple of the first item and initial state or [`nothing`](@ref) if empty        |
| `iterate(iter, state)`         |                        | Returns either a tuple of the next item and next state or `nothing` if no items remain  |
| **Important optional methods** | **Default definition** | **Brief description**                                                                 |
| `IteratorSize(IterType)`       | `HasLength()`          | One of `HasLength()`, `HasShape{N}()`, `IsInfinite()`, or `SizeUnknown()` as appropriate |
| `IteratorEltype(IterType)`     | `HasEltype()`          | Either `EltypeUnknown()` or `HasEltype()` as appropriate                              |
| `eltype(IterType)`             | `Any`                  | The type of the first entry of the tuple returned by `iterate()`                      |
| `length(iter)`                 | (*undefined*)          | The number of items, if known                                                         |
| `size(iter, [dim...])`         | (*undefined*)          | The number of items in each dimension, if known                                       |

| Value returned by `IteratorSize(IterType)` | Required Methods                           |
|:------------------------------------------ |:------------------------------------------ |
| `HasLength()`                              | [`length(iter)`](@ref)                     |
| `HasShape{N}()`                            | `length(iter)`  and `size(iter, [dim...])` |
| `IsInfinite()`                             | (*none*)                                   |
| `SizeUnknown()`                            | (*none*)                                   |

| Value returned by `IteratorEltype(IterType)` | Required Methods   |
|:-------------------------------------------- |:------------------ |
| `HasEltype()`                                | `eltype(IterType)` |
| `EltypeUnknown()`                            | (*none*)           |

-->
```

| 必須メソッド               |                        | 概説                                                                     |
|:------------------------------ |:---------------------- |:------------------------------------------------------------------------------------- |
| `iterate(iter)`                |                        | 最初のアイテムのタプルと初期状態を返すか、空の時は[`nothing`](@ref)を返す  |
| `iterate(iter, state)`         |                        | 次のアイテムと次の状態を返すか、残りのアイテムがないときは`nothing`を返す|
| **重要な追加可能なメソッド** | **デフォルトのメソッド** | **概説** |                                                                |
| `iteratorsize(IterType)`       | `HasLength()`          | `HasLength()`, `HasShape{N}()`, `IsInfinite()`,  `SizeUnknown()` の中で適切なもの一つ|
| `iteratoreltype(IterType)`     | `HasEltype()`          | `EltypeUnknown()`と`HasEltype()`のどちらか適切のもの                              |
| `eltype(IterType)`             | `Any`                  | `iterate()` が返すタプルの最初のエントリーの型|
| `length(iter)`                 | (*undefined*)          | アイテムの数(既知の場合)                                                         |
| `size(iter, [dim])`         | (*undefined*)          | 各次元のアイテムの数(既知の場合)                                           |

| `iteratorsize(IterType)`の戻り値 | 必要なメソッド                           |
|:------------------------------------------ |:------------------------------------------ |
| `HasLength()`                              | `length(iter)`                             |
| `HasShape{N}()`                               | `length(iter)`と`size(iter, [dim])` |
| `IsInfinite()`                             | (*none*)                                   |
| `SizeUnknown()`                            | (*none*)                                   |

|  `iteratoreltype(IterType)`の戻り値  |必要なメソッド  |
|:-------------------------------------------- |:------------------ |
| `HasEltype()`                                | `eltype(IterType)` |
| `EltypeUnknown()`                            | (*none*)           |


```@raw html
<!--
Sequential iteration is implemented by the [`iterate`](@ref) function. Instead
of mutating objects as they are iterated over, Julia iterators may keep track
of the iteration state externally from the object. The return value from iterate
is always either a tuple of a value and a state, or `nothing` if no elements remain.
The state object will be passed back to the iterate function on the next iteration
and is generally considered an implementation detail private to the iterable object.

Any object that defines this function is iterable and can be used in the [many functions that rely upon iteration](@ref lib-collections-iteration).
It can also be used directly in a [`for`](@ref) loop since the syntax:
-->
```

順次実行される反復処理は、 [`iterate`](@ref)関数によって実装されています。
Juliaでは、反復処理の状態の追跡は、このメソッドを使ってオブジェクトの外部から行い、反復の対象となるオブジェクトを変更するわけではありません。
iterateの戻り値は、通常は値と状態のタプルですが、要素が残っていない場合は`nothing`を返します。
次の反復の際に、`state`オブジェクトが反復の関数に渡されます。
`state`オブジェクトは、イテラブルオブジェクト内部の実装の詳細をあらわしていると一般には考えられています。

この関数が定義されたオブジェクトはすべてイテラブルであり、[反復に依存した多数の関数]（@ ref lib-collections-iteration）で使用できます。
また以下のような構文で [`for`](@ref)ループ内で直接使用することもできます。



```julia
for i in iter   # or  "for i = iter"
    # body
end
```

```@raw html
<!--
is translated into:
-->
```

上記の構文は以下のように変換されます。

```julia
next = iterate(iter)
while next !== nothing
    (i, state) = next
    # body
    next = iterate(iter, state)
end
```

```@raw html
<!--
A simple example is an iterable sequence of square numbers with a defined length:
-->
```
簡単な例は、長さの決まった平方数のイテラブルな数列です。

```jldoctest squaretype
julia> struct Squares
           count::Int
       end

julia> Base.iterate(S::Squares, state=1) = state > S.count ? nothing : (state*state, state+1)
```

```@raw html
<!--
With only [`iterate`](@ref) definition, the `Squares` type is already pretty powerful.
We can iterate over all the elements:
-->
```
[`iterate`](@ref) の定義だけでも、`Squares`型はすでにかなり強力です。
すべての要素に対する反復処理を実行できます。


```jldoctest squaretype
julia> for i in Squares(7)
           println(i)
       end
1
4
9
16
25
36
49
```

```@raw html
<!--
We can use many of the builtin methods that work with iterables,
like [`in`](@ref), or [`mean`](@ref) and [`std`](@ref) from the
`Statistics` standard library module:
-->
```
[`in`](@ref)など多くの組込みのメソッドが利用可能で、さらに`Statistics`標準ライブラリモジュールを使うと [`mean`](@ref)や[`std`](@ref)も利用できます。


```jldoctest squaretype
julia> 25 in Squares(10)
true

julia> using Statistics

julia> mean(Squares(100))
3383.5

julia> std(Squares(100))
3024.355854282583
```

```@raw html
<!--
There are a few more methods we can extend to give Julia more information about this iterable
collection.  We know that the elements in a `Squares` sequence will always be `Int`. By extending
the [`eltype`](@ref) method, we can give that information to Julia and help it make more specialized
code in the more complicated methods. We also know the number of elements in our sequence, so
we can extend [`length`](@ref), too:
-->
```
さらに、イテラブルコレクションに関する詳しい情報を伝えるために、拡張して使うメソッドがJuliaにはいくつかあります。
`Squares`の数列の要素は常に`Int`であることがわかっています。
 [`eltype`](@ref)メソッドを拡張して、この情報をJuliaに渡すと、もっと複雑なメソッドでも、もっと型に特化したコードを作成するのに役立てることができます。
シーケンスの要素数もわかっているので[`length`](@ref)も拡張することもできます。


```jldoctest squaretype
julia> Base.eltype(::Type{Squares}) = Int # Note that this is defined for the type

julia> Base.length(S::Squares) = S.count
```

```@raw html
<!--
Now, when we ask Julia to [`collect`](@ref) all the elements into an array it can preallocate a `Vector{Int}`
of the right size instead of blindly [`push!`](@ref)ing each element into a `Vector{Any}`:
-->
```

ここまでくれば、Juliaで、 すべての要素を[`collect()`](@ref) を使って配列化する際に、
`Vector{Int}`のように 正しいサイズに事前に割り当てることができます。[`push!`](@ref)を使って闇雲に各要素を`Vector{Any}`におしこまなくてもよいのです。


```jldoctest squaretype
julia> collect(Squares(4))
4-element Array{Int64,1}:
  1
  4
  9
 16
```

```@raw html
<!--
While we can rely upon generic implementations, we can also extend specific methods where we know
there is a simpler algorithm. For example, there's a formula to compute the sum of squares, so
we can override the generic iterative version with a more performant solution:
-->
```

汎化した実装のまま使うこともできますが、もっと単純なアルゴリズムがあると分かっている場合は、特化したメソッドに拡張することもできます。
たとえば、平方和を算出する公式があれば、汎化した反復をもっと効率的な解法で上書きすることができます。


```jldoctest squaretype
julia> Base.sum(S::Squares) = (n = S.count; return n*(n+1)*(2n+1)÷6)

julia> sum(Squares(1803))
1955361914
```

```@raw html
<!--
This is a very common pattern throughout Julia Base: a small set of required methods
define an informal interface that enable many fancier behaviors. In some cases, types will want
to additionally specialize those extra behaviors when they know a more efficient algorithm can
be used in their specific case.
-->
```

これは、JuliaのBaseライブラリ全体に非常によくあるパターンです。
少数の必須メソッドに対して定義をおこなうと、具象化されていない部分の残ったインターフェイスが多くの便利な動作するようになります。
さらに特別な場合に効率的なアルゴリズムを使用できる場合には、さらに特化させた挙動にすることができます。

```@raw html
<!--
It is also often useful to allow iteration over a collection in *reverse order*
by iterating over [`Iterators.reverse(iterator)`](@ref).  To actually support
reverse-order iteration, however, an iterator
type `T` needs to implement `iterate` for `Iterators.Reverse{T}`.
(Given `r::Iterators.Reverse{T}`, the underling iterator of type `T` is `r.itr`.)
In our `Squares` example, we would implement `Iterators.Reverse{Squares}` methods:
-->
```


```jldoctest squaretype
julia> Base.iterate(rS::Iterators.Reverse{Squares}, state=rS.itr.count) = state < 1 ? nothing : (state*state, state-1)

julia> collect(Iterators.reverse(Squares(4)))
4-element Array{Int64,1}:
 16
  9
  4
  1
```

`[](## Indexing)
## インデックスづけ

```@raw html
<!--
| Methods to implement | Brief description                |
|:-------------------- |:-------------------------------- |
| `getindex(X, i)`     | `X[i]`, indexed element access   |
| `setindex!(X, v, i)` | `X[i] = v`, indexed assignment   |
| `firstindex(X)`      | The first index                  |
| `lastindex(X)`        | The last index, used in `X[end]` |
-->
```
| 実装すべきメソッド | 概説                |
|:-------------------- |:-------------------------------- |
| `getindex(X, i)`     | `X[i]`, インデックスによる要素の参照   |
| `setindex!(X, v, i)` | `X[i] = v`,  インデックスによる代入   |
| `endof(X)`           | インデックスの最後尾,  `X[end]`で使われる |


```@raw html
<!--
For the `Squares` iterable above, we can easily compute the `i`th element of the sequence by squaring
it.  We can expose this as an indexing expression `S[i]`. To opt into this behavior, `Squares`
simply needs to define [`getindex`](@ref):
-->
```

上記の`Squares`イテラブルでは、数列の`i`番目は、2乗すれば簡単に算出できます。
`S[i]`というインデックスを使った式で、表すことができます。
`Squares` に[`getindex()`](@ref) を定義すればいいだけです。


```jldoctest squaretype
julia> function Base.getindex(S::Squares, i::Int)
           1 <= i <= S.count || throw(BoundsError(S, i))
           return i*i
       end

julia> Squares(100)[23]
529
```

```@raw html
<!--
Additionally, to support the syntax `S[end]`, we must define [`lastindex`](@ref) to specify the last
valid index. It is recommended to also define [`firstindex`](@ref) to specify the first valid index:
-->
```
さらに、`S[end]`構文を使えるようにするには、[`endof()`](@ref)を定義して有効な最後尾のインデックスを指定する必要があります。


```jldoctest squaretype
julia> Base.firstindex(S::Squares) = 1

julia> Base.lastindex(S::Squares) = length(S)

julia> Squares(23)[end]
529
```

```@raw html
<!--
Note, though, that the above *only* defines [`getindex`](@ref) with one integer index. Indexing with
anything other than an `Int` will throw a [`MethodError`](@ref) saying that there was no matching method.
In order to support indexing with ranges or vectors of `Int`s, separate methods must be written:
-->
```

ただし、上記の定義は [`getindex`](@ref) を1つの整数インデックス**のみ**であることに注意してください。
`Int`以外のものを使ってインデックスを使うと [`MethodError`](@ref) を投げて、適合するメソッドが存在しないというメッセージが表示されるでしょう。
範囲や`Int`のベクトルに対してインデックスをつかう場合は、別のメソッドを記述する必要があります。


```jldoctest squaretype
julia> Base.getindex(S::Squares, i::Number) = S[convert(Int, i)]

julia> Base.getindex(S::Squares, I) = [S[i] for i in I]

julia> Squares(10)[[3,4.,5]]
3-element Array{Int64,1}:
  9
 16
 25
```

```@raw html
<!--
While this is starting to support more of the [indexing operations supported by some of the builtin types](@ref man-array-indexing),
there's still quite a number of behaviors missing. This `Squares` sequence is starting to look
more and more like a vector as we've added behaviors to it. Instead of defining all these behaviors
ourselves, we can officially define it as a subtype of an [`AbstractArray`](@ref).
-->
```

[一部の組込みの型で可能なインデックス操作]（@ ref man-array-indexing）をがけっこう使えるようになりましたが、依然として動作しないものが多くあります。
この`Squares`シーケンスに、もっと動作を加えると、ますますベクトルのように見えます。
これらの動作はすべてを自前で定義しなくても、正式な [`AbstractArray`](@ref)のサブタイプとして定義することができます。


`[](## [Abstract Arrays](@id man-interface-array))
## [抽象配列](@id man-interface-array)

```@raw html
<!--
| Methods to implement                            |                                        | Brief description                                                                     |
|:----------------------------------------------- |:-------------------------------------- |:------------------------------------------------------------------------------------- |
| `size(A)`                                       |                                        | Returns a tuple containing the dimensions of `A`                                      |
| `getindex(A, i::Int)`                           |                                        | (if `IndexLinear`) Linear scalar indexing                                             |
| `getindex(A, I::Vararg{Int, N})`                |                                        | (if `IndexCartesian`, where `N = ndims(A)`) N-dimensional scalar indexing             |
| `setindex!(A, v, i::Int)`                       |                                        | (if `IndexLinear`) Scalar indexed assignment                                          |
| `setindex!(A, v, I::Vararg{Int, N})`            |                                        | (if `IndexCartesian`, where `N = ndims(A)`) N-dimensional scalar indexed assignment   |
| **Optional methods**                            | **Default definition**                 | **Brief description**                                                                 |
| `IndexStyle(::Type)`                            | `IndexCartesian()`                     | Returns either `IndexLinear()` or `IndexCartesian()`. See the description below.      |
| `getindex(A, I...)`                             | defined in terms of scalar `getindex`  | [Multidimensional and nonscalar indexing](@ref man-array-indexing)                    |
| `setindex!(A, I...)`                            | defined in terms of scalar `setindex!` | [Multidimensional and nonscalar indexed assignment](@ref man-array-indexing)          |
| `iterate`                                       | defined in terms of scalar `getindex`  | Iteration                                                                             |
| `length(A)`                                     | `prod(size(A))`                        | Number of elements                                                                    |
| `similar(A)`                                    | `similar(A, eltype(A), size(A))`       | Return a mutable array with the same shape and element type                           |
| `similar(A, ::Type{S})`                         | `similar(A, S, size(A))`               | Return a mutable array with the same shape and the specified element type             |
| `similar(A,  dims::Dims)`                 | `similar(A, eltype(A), dims)`          | Return a mutable array with the same element type and size *dims*                     |
| `similar(A, ::Type{S},  dims::Dims)`      | `Array{S}(undef, dims)`               | Return a mutable array with the specified element type and size                       |
| **Non-traditional indices**                     | **Default definition**                 | **Brief description**                                                                 |
| `axes(A)`                                    | `map(OneTo, size(A))`                  | Return the `AbstractUnitRange` of valid indices                                       |
| `Base.similar(A, ::Type{S}, inds::NTuple{Ind})` | `similar(A, S, Base.to_shape(inds))`   | Return a mutable array with the specified indices `inds` (see below)                  |
| `Base.similar(T::Union{Type,Function}, inds)`   | `T(Base.to_shape(inds))`               | Return an array similar to `T` with the specified indices `inds` (see below)          |
-->
```

| 実装すべきメソッド                            |                                          | 概説                                                                     |
|:----------------------------------------------- |:---------------------------------------- |:------------------------------------------------------------------------------------- |
| `size(A)`                                       |                                          | `A`の次元を含むタプルを返す                                      |
| `getindex(A, i::Int)`                           |                                          | ( `IndexLinear`)線形スカラインデックスによる参照                                              |
| `getindex(A, I::Vararg{Int, N})`                |                                          | ( `IndexCartesian`,`N = ndims(A)`) N次元のスカラインデックスにによる参照                |
| `setindex!(A, v, i::Int)`                       |                                          | ( `IndexLinear`) 線形スカラインデックスによる代入                                           |
| `setindex!(A, v, I::Vararg{Int, N})`            |                                          | ( `IndexCartesian`, `N = ndims(A)`) N次元のスカラインデックスによる代入       |
| **省略可能なメソッド**                            | **デフォルトの定義**                   | **概説**                                                                 |
| `IndexStyle(::Type)`                            | `IndexCartesian()`                       |  `IndexLinear()` と `IndexCartesian()`のどちらかを返す。下記参照      |
| `getindex(A, I...)`                             | スカラーの  `getindex()`を使った定義  | [多次元で非スカラーのインデックスによる参照](@ref man-array-indexing)                    |
| `setindex!(A, I...)`                            | スカラーの `setindex!()`を使った定義 | [多次元で非スカラーのインデックスによる代入](@ref man-array-indexing)          |
| `iterate`                                       | スカラーの  `getindex()`を使った定義  | 繰り返し                                                                             |
|| `length(A)`                                     | `prod(size(A))`                          | 要素の数                                                                    |
| `similar(A)`                                    | `similar(A, eltype(A), size(A))`         | 同形・同要素型の可変配列を返す                          |
| `similar(A, ::Type{S})`                         | `similar(A, S, size(A))`                 | 同形・指定要素型の可変配列を返す|
| `similar(A,  dims::Dims)`                 | `similar(A, eltype(A), dims)`            | 同要素型でサイズ**dims**の可変配列を返す |
| `similar(A, ::Type{S},  dims::Dims)`      | `Array{S}(undef, dims)`                         | 指定形・指定要素型の可変配列を返す|
| **通常とは異なるインデックス**                     | **デフォルトの定義**                   | **概説**                                                                 |
| `axes(A)`                                    | `map(OneTo, size(A))`                  | 有効なインデックスの`AbstractUnitRange`を返す                                      |

| `Base.similar(A, ::Type{S}, inds::NTuple{Ind})` | `similar(A, S, Base.to_shape(inds))`     | `inds`で指定したインデックスの可変配列を返す (下記参照)                  |
| `Base.similar(T::Union{Type,Function}, inds)`   | `T(Base.to_shape(inds))`                 | `inds`で指定したインデックスの`T`と同様な可変配列を返す (下記参照)          |


```@raw html
<!--
If a type is defined as a subtype of `AbstractArray`, it inherits a very large set of rich behaviors
including iteration and multidimensional indexing built on top of single-element access.  See
the [arrays manual page](@ref man-multi-dim-arrays) and the [Julia Base section](@ref lib-arrays) for more supported methods.
-->
```
`AbstractArray`のサブタイプとして定義された型は、多様な動作を数多く継承していて、反復処理や、1要素のアクセスから構築された多次元インデックスなどが利用できます。
その他の利用可能なメソッドについては、[配列のマニュアルページ]（@ ref man-multi-dim-arrays）と[JuliaのBaseライブラリのセクション]（@ ref lib-arrays）を参照してください。


```@raw html
<!--
A key part in defining an `AbstractArray` subtype is [`IndexStyle`](@ref). Since indexing is
such an important part of an array and often occurs in hot loops, it's important to make both
indexing and indexed assignment as efficient as possible.  Array data structures are typically
defined in one of two ways: either it most efficiently accesses its elements using just one index
(linear indexing) or it intrinsically accesses the elements with indices specified for every dimension.
 These two modalities are identified by Julia as `IndexLinear()` and `IndexCartesian()`.
 Converting a linear index to multiple indexing subscripts is typically very expensive, so this
provides a traits-based mechanism to enable efficient generic code for all array types.
-->
```
`AbstractArray`のサブタイプの定義で重要な部分は[`IndexStyle`](@ref) です。
インデックスは配列の重要な部分であり、頻繁にループで使わわれるため、インデックスによる参照と代入をできる限り効率的に行うことは重要です。
配列のデータ構造の定義には、通常、２つの手法のいずれかが採用されます。
一方は、インデックス（線形インデックス）をただ一つ使用して要素にアクセスする最も効率のよい手法で、もう一方は、本質的にはすべての次元に対してインデックスを指定して要素にアクセスする手法です。
これらの2つのモードは、Juliaでは`IndexLinear()`と`IndexCartesian()`として表わされます。
線形インデックスを多重インデックスの添字に変換するのは、通常非常にコストがかかるので、トレイトに基づいたしくみによって、すべての配列の型に汎化した効率的なコードが可能になっています。


```@raw html
<!--
This distinction determines which scalar indexing methods the type must define. `IndexLinear()`
arrays are simple: just define `getindex(A::ArrayType, i::Int)`.  When the array is subsequently
indexed with a multidimensional set of indices, the fallback `getindex(A::AbstractArray, I...)()`
efficiently converts the indices into one linear index and then calls the above method. `IndexCartesian()`
arrays, on the other hand, require methods to be defined for each supported dimensionality with
`ndims(A)` `Int` indices. For example, [`SparseMatrixCSC`](@ref) from the `SparseArrays` standard
library module, only supports two dimensions, so it just defines
`getindex(A::SparseMatrixCSC, i::Int, j::Int)`. The same holds for [`setindex!`](@ref).

Returning to the sequence of squares from above, we could instead define it as a subtype of an
`AbstractArray{Int, 1}`:
-->
```
この違いによって、どのスカラーインデックスのメソッドを型に対して定義しなければならないかが決まります。
`IndexLinear()`の配列は単純で、`getindex(A::ArrayType, i::Int)`を定義するだけです。
配列が多次元で複数のインデックスによってインデックス付けされている場合、補足的な`getindex(A::AbstractArray, I...)()` はインデックスを線形インデックスに効率的に変換し、前述のメソッドを呼び出します。
一方、`IndexCartesian()` の配列は、`ndims(A)`、`Int`の指定によって利用可能となる次元すべてに対して、メソッドを定義する必要があります。
たとえば、組込みの[`SparseMatrixCSC`](@ref)型は2次元しか利用可能ではないため、`getindex(A::SparseMatrixCSC, i::Int, j::Int)()`だけを定義しています。[`setindex!`](@ref)に関しても同様です。

上記の二乗の数列に戻ると、代わりに`AbstractArray{Int, 1}`のサブタイプとして定義することができます。


```jldoctest squarevectype
julia> struct SquaresVector <: AbstractArray{Int, 1}
           count::Int
       end

julia> Base.size(S::SquaresVector) = (S.count,)

julia> Base.IndexStyle(::Type{<:SquaresVector}) = IndexLinear()

julia> Base.getindex(S::SquaresVector, i::Int) = i*i
```

```@raw html
<!--
Note that it's very important to specify the two parameters of the `AbstractArray`; the first
defines the [`eltype`](@ref), and the second defines the [`ndims`](@ref). That supertype and those three
methods are all it takes for `SquaresVector` to be an iterable, indexable, and completely functional
array:
-->
```
`AbstractArray`で指定する2つのパラメータは、非常に重要であることに注意してください。
1番目は[`eltype`](@ref) を定義し、2番目は[`ndims`](@ref)を定義します。
このスーパータイプと3つのメソッドのすべてによって、`SquaresVector`は反復とインデックスによるアクセスが可能になり、完全に機能する配列となります。


```jldoctest squarevectype
julia> s = SquaresVector(4)
4-element SquaresVector:
  1
  4
  9
 16

julia> s[s .> 8]
2-element Array{Int64,1}:
  9
 16

julia> s + s
4-element Array{Int64,1}:
  2
  8
 18
 32

julia> sin.(s)
4-element Array{Float64,1}:
  0.8414709848078965
 -0.7568024953079282
  0.4121184852417566
 -0.2879033166650653
```

```@raw html
<!--
As a more complicated example, let's define our own toy N-dimensional sparse-like array type built
on top of [`Dict`](@ref):
-->
```

より複雑な例として、N次元で疎なおもちゃのような配列型を[`Dict`](@ref)を使って定義しましょう。


```jldoctest squarevectype
julia> struct SparseArray{T,N} <: AbstractArray{T,N}
           data::Dict{NTuple{N,Int}, T}
           dims::NTuple{N,Int}
       end

julia> SparseArray(::Type{T}, dims::Int...) where {T} = SparseArray(T, dims);

julia> SparseArray(::Type{T}, dims::NTuple{N,Int}) where {T,N} = SparseArray{T,N}(Dict{NTuple{N,Int}, T}(), dims);

julia> Base.size(A::SparseArray) = A.dims

julia> Base.similar(A::SparseArray, ::Type{T}, dims::Dims) where {T} = SparseArray(T, dims)

julia> Base.getindex(A::SparseArray{T,N}, I::Vararg{Int,N}) where {T,N} = get(A.data, I, zero(T))

julia> Base.setindex!(A::SparseArray{T,N}, v, I::Vararg{Int,N}) where {T,N} = (A.data[I] = v)
```

```@raw html
<!--
Notice that this is an `IndexCartesian` array, so we must manually define [`getindex`](@ref) and [`setindex!`](@ref)
at the dimensionality of the array. Unlike the `SquaresVector`, we are able to define [`setindex!`](@ref),
and so we can mutate the array:
-->
```
これは`IndexCartesian`の配列なので、 [`getindex`](@ref) と[`setindex!`](@ref) を次元ごとに手動で定義する必要がある点に注意してください。
`SquaresVector`配列とは違って、[`setindex!`](@ref)を定義できるので、配列を更新することができます：



```jldoctest squarevectype
julia> A = SparseArray(Float64, 3, 3)
3×3 SparseArray{Float64,2}:
 0.0  0.0  0.0
 0.0  0.0  0.0
 0.0  0.0  0.0

julia> fill!(A, 2)
3×3 SparseArray{Float64,2}:
 2.0  2.0  2.0
 2.0  2.0  2.0
 2.0  2.0  2.0

julia> A[:] = 1:length(A); A
3×3 SparseArray{Float64,2}:
 1.0  4.0  7.0
 2.0  5.0  8.0
 3.0  6.0  9.0
```

```@raw html
<!--
The result of indexing an `AbstractArray` can itself be an array (for instance when indexing by
an `AbstractRange`). The `AbstractArray` fallback methods use [`similar`](@ref) to allocate an `Array`
of the appropriate size and element type, which is filled in using the basic indexing method described
above. However, when implementing an array wrapper you often want the result to be wrapped as
well:
-->
```
`AbstractArray`をインデックス参照した結果自体が配列になることもあります（たとえば、`AbstractRange`を使ってインデックス参照した場合など）。
`AbstractArray`の補足メソッドは、[`similar`](@ref) を使って妥当な要素の型とサイズの`配列`をメモリの割り当てを行い、上述した基本的なインデックスのメソッドを使ってを値を埋めていきます。
しかし、配列のラッパーが実装されているときには、当然、結果も同様にラップしたくなることはよくあるでしょう。



```jldoctest squarevectype
julia> A[1:2,:]
2×3 SparseArray{Float64,2}:
 1.0  4.0  7.0
 2.0  5.0  8.0
```

```@raw html
<!--
In this example it is accomplished by defining `Base.similar{T}(A::SparseArray, ::Type{T}, dims::Dims)`
to create the appropriate wrapped array. (Note that while `similar` supports 1- and 2-argument
forms, in most case you only need to specialize the 3-argument form.) For this to work it's important
that `SparseArray` is mutable (supports `setindex!`). Defining `similar`, `getindex` and
`setindex!` for `SparseArray` also makes it possible to [`copy`](@ref) the array:
-->
```
この例では`Base.similar{T}(A::SparseArray, ::Type{T}, dims::Dims)`を定義して、適切にラップされた配列を作成しています。
（`similar`は引数が1個や2個の場合も対応していますが、必要になるのは、ほとんどの場合、3個の場合に特化している点に注意してください。）
これが動作するには`SparseArray`が可変（`setindex!`を利用可能）であることが重要です。
`similar`、`getindex`、`setindex!`を`SparseArray`に定義すると、配列を [`copy`](@ref)することが可能になります。


```jldoctest squarevectype
julia> copy(A)
3×3 SparseArray{Float64,2}:
 1.0  4.0  7.0
 2.0  5.0  8.0
 3.0  6.0  9.0
```

```@raw html
<!--
In addition to all the iterable and indexable methods from above, these types can also interact
with each other and use most of the methods defined in Julia Base for `AbstractArrays`:
-->
```
上記のすべての反復可能・インデックス可能なメソッドのほかにも、これらの型は相互に利用することができ、Baseライブラリで定義されている`AbstractArrays`向けのメソッドをほとんど利用できます。


```jldoctest squarevectype
julia> A[SquaresVector(3)]
3-element SparseArray{Float64,1}:
 1.0
 4.0
 9.0

julia> sum(A)
45.0
```

```@raw html
<!--
If you are defining an array type that allows non-traditional indexing (indices that start at
something other than 1), you should specialize [`axes`](@ref). You should also specialize [`similar`](@ref)
so that the `dims` argument (ordinarily a `Dims` size-tuple) can accept `AbstractUnitRange` objects,
perhaps range-types `Ind` of your own design. For more information, see
[Arrays with custom indices](@ref man-custom-indices).
-->
```

通常ではない（1以外から始まる)インデックスを使うには、[`axes`](@ref)を特化させる必要があります。
また引数の`dims`（通常は`Dims`のサイズのタプル）が`AbstractUnitRange`オブジェクト、おそらく独自設計の範囲型である`Ind`を受けとれるようにするには、[`similar`](@ref)を特化する必要があります。
詳細については、[独自インデックスの配列](@ref man-custom-indices)を参照してください。

`[](## [Strided Arrays](@id man-interface-strided-arrays))
## [ストライド配列](@id man-interface-strided-arrays)

```@raw html
<!--
| Methods to implement                            |                                        | Brief description                                                                     |
|:----------------------------------------------- |:-------------------------------------- |:------------------------------------------------------------------------------------- |
| `strides(A)`                             |                                        | Return the distance in memory (in number of elements) between adjacent elements in each dimension as a tuple. If `A` is an `AbstractArray{T,0}`, this should return an empty tuple.    |
| `Base.unsafe_convert(::Type{Ptr{T}}, A)`        |                                        | Return the native address of an array.                                            |
| **Optional methods**                            | **Default definition**                 | **Brief description**                                                                 |
| `stride(A, i::Int)`                             |     `strides(A)[i]`                                   | Return the distance in memory (in number of elements) between adjacent elements in dimension k.    |

-->
```
| 実装すべきメソッド                           |                                        | 簡単な説明                                                                     |
|:----------------------------------------------- |:-------------------------------------- |:------------------------------------------------------------------------------------- |
| `strides(A)`                             |                                        | Return the distance in memory (in number of elements) between adjacent elements in each dimension as a tuple. If `A` is an `AbstractArray{T,0}`, this should return an empty tuple.    |
| `Base.unsafe_convert(::Type{Ptr{T}}, A)`        |                                        | Return the native address of an array.                                            |
| **省略可能なメソッド**                            | **デフォルトの定義**                 | **簡単な説明**                                                                 |
| `stride(A, i::Int)`                             |     `strides(A)[i]`                                   | Return the distance in memory (in number of elements) between adjacent elements in dimension k.    |


```@raw html
<!--
A strided array is a subtype of `AbstractArray` whose entries are stored in memory with fixed strides.
Provided the element type of the array is compatible with BLAS, a strided array can utilize BLAS and LAPACK routines
for more efficient linear algebra routines.  A typical example of a user-defined strided array is one
that wraps a standard `Array` with additional structure.

Warning: do not implement these methods if the underlying storage is not actually strided, as it
may lead to incorrect results or segmentation faults.

Here are some examples to demonstrate which type of arrays are strided and which are not:
-->
```
ストライド配列は `AbstractArray`のサブタイプで、各要素が、メモリ中で決まったストライドで格納されます。
配列の各要素の型がBLASと互換性がある場合、ストライド配列に、線形代数用の効率的なアルゴリズムを持つBLASやLAPACKのルーチンを利用できます。
ユーザー定義のストライド配列の典型例として、標準的な`Array`に付加的な構造を加えてラップする、というものがあります。

警告：基になる格納先が実際にはストライドではない場合、こういったメソッドを実装しないでください。
間違った結果やセグメントの障害につながるかもしれないからです。

配列でストライドのあるもの・ないものを示す例をいくつか挙げます。

```julia
1:5   # not strided (there is no storage associated with this array.)
Vector(1:5)  # is strided with strides (1,)
A = [1 5; 2 6; 3 7; 4 8]  # is strided with strides (1,4)
V = view(A, 1:2, :)   # is strided with strides (1,4)
V = view(A, 1:2:3, 1:2)   # is strided with strides (2,4)
V = view(A, [1,2,4], :)   # is not strided, as the spacing between rows is not fixed.
```





`[](## [Customizing broadcasting](@id man-interfaces-broadcasting))
## [独自のブロードキャスト](@id man-interfaces-broadcasting)

```@raw html
<!--
| Methods to implement | Brief description |
|:-------------------- |:----------------- |
| `Base.BroadcastStyle(::Type{SrcType}) = SrcStyle()` | Broadcasting behavior of `SrcType` |
| `Base.similar(bc::Broadcasted{DestStyle}, ::Type{ElType})` | Allocation of output container |
| **Optional methods** | | |
| `Base.BroadcastStyle(::Style1, ::Style2) = Style12()` | Precedence rules for mixing styles |
| `Base.broadcast_axes(x)` | Declaration of the indices of `x` for broadcasting purposes (defaults to [`axes(x)`](@ref)) |
| `Base.broadcastable(x)` | Convert `x` to an object that has `axes` and supports indexing |
| **Bypassing default machinery** | |
| `Base.copy(bc::Broadcasted{DestStyle})` | Custom implementation of `broadcast` |
| `Base.copyto!(dest, bc::Broadcasted{DestStyle})` | Custom implementation of `broadcast!`, specializing on `DestStyle` |
| `Base.copyto!(dest::DestType, bc::Broadcasted{Nothing})` | Custom implementation of `broadcast!`, specializing on `DestType` |
| `Base.Broadcast.broadcasted(f, args...)` | Override the default lazy behavior within a fused expression |
| `Base.Broadcast.instantiate(bc::Broadcasted{DestStyle})` | Override the computation of the lazy broadcast's axes |
-->
```

| 実装すべきメソッド | 簡単な説明 |
|:-------------------- |:----------------- |
| `Base.BroadcastStyle(::Type{SrcType}) = SrcStyle()` | Broadcasting behavior of `SrcType` |
| `Base.similar(bc::Broadcasted{DestStyle}, ::Type{ElType})` | Allocation of output container |
| **Optional methods** | | |
| `Base.BroadcastStyle(::Style1, ::Style2) = Style12()` | Precedence rules for mixing styles |
| `Base.broadcast_axes(x)` | Declaration of the indices of `x` for broadcasting purposes (defaults to [`axes(x)`](@ref)) |
| `Base.broadcastable(x)` | Convert `x` to an object that has `axes` and supports indexing |
| **Bypassing default machinery** | |
| `Base.copy(bc::Broadcasted{DestStyle})` | Custom implementation of `broadcast` |
| `Base.copyto!(dest, bc::Broadcasted{DestStyle})` | Custom implementation of `broadcast!`, specializing on `DestStyle` |
| `Base.copyto!(dest::DestType, bc::Broadcasted{Nothing})` | Custom implementation of `broadcast!`, specializing on `DestType` |
| `Base.Broadcast.broadcasted(f, args...)` | Override the default lazy behavior within a fused expression |
| `Base.Broadcast.instantiate(bc::Broadcasted{DestStyle})` | Override the computation of the lazy broadcast's axes |


```@raw html
<!--
[Broadcasting](@ref) is triggered by an explicit call to `broadcast` or `broadcast!`, or implicitly by
"dot" operations like `A .+ b` or `f.(x, y)`. Any object that has [`axes`](@ref) and supports
indexing can participate as an argument in broadcasting, and by default the result is stored
in an `Array`. This basic framework is extensible in three major ways:
-->
```
[ブロードキャスト]　(@ref)は明示的に`broadcast`や`broadcast!`を呼び出したり、暗黙的に`A .+ b` や`f.(x, y)`などのドット演算を
行ったときに、発動します。
 [`axes`](@ref)を持ち、インデックスに対応しているオブジェクトは、ブロードキャストに引数として参加することができます。
デフォルトでは結果は`配列`に格納されます。
この基本的なフレームワークは主に３つの主要な方法によって拡張可能です。



```@raw html
<!--
* Ensuring that all arguments support broadcast
* Selecting an appropriate output array for the given set of arguments
* Selecting an efficient implementation for the given set of arguments
-->
```

* すべての引数がブロードキャストに対応していることを保証する
* 与えられた引数の組に対して適切な出力の配列を選択する
* 与えられた引数の組に対して効率的な実装を選択する


```@raw html
<!--
Not all types support `axes` and indexing, but many are convenient to allow in broadcast.
The [`Base.broadcastable`](@ref) function is called on each argument to broadcast, allowing
it to return something different that supports `axes` and indexing. By
default, this is the identity function for all `AbstractArray`s and `Number`s — they already
support `axes` and indexing. For a handful of other types (including but not limited to
types themselves, functions, special singletons like [`missing`](@ref) and [`nothing`](@ref), and dates),
`Base.broadcastable` returns the argument wrapped in a `Ref` to act as a 0-dimensional
"scalar" for the purposes of broadcasting. Custom types can similarly specialize
`Base.broadcastable` to define their shape, but they should follow the convention that
`collect(Base.broadcastable(x)) == collect(x)`. A notable exception is `AbstractString`;
strings are special-cased to behave as scalars for the purposes of broadcast even though
they are iterable collections of their characters (see [Strings](@ref) for more).
-->
```
すべての型が`axes`やインデックスに対応しているわけではないですが、多くでブロードキャストに便利に使えます。
[`Base.broadcastable`](@ref)関数は各引数をブロードキャストのために呼び出した際に少し違っていても `axes`とインデックスに対応しているものを返します。デフォルトでは、すべての`AbstractArray`や`Number`の場合、恒等写像になります。これらはすでに`axes`とインデックスに対応しているからです。
少数の型（また型に限らず、関数、[`missing`](@ref)や[`nothing`](@ref)などの特殊なシングルトン、日付など)に対しては、
`Base.broadcastable`は引数を`Ref`によってラップし、ブロードキャストに対応できるように0次元の"scalar"として動作するようにします。
独自の型対しても同様に、その形に対して`Base.broadcastable`を特化させる定義ができますが、`collect(Base.broadcastable(x)) == collect(x)`
が成り立つよう、慣習に従うべきです。
主な例外として、`AbstractString`があります。
文字列は文字に対するイテラブルなコレクションですが、ブロードキャストの際にスカラーとしてふるまう特殊なケースです。
(詳細は[文字列](@ref)を参照のこと)


```@raw html
<!--
The next two steps (selecting the output array and implementation) are dependent upon
determining a single answer for a given set of arguments. Broadcast must take all the varied
types of its arguments and collapse them down to just one output array and one
implementation. Broadcast calls this single answer a "style." Every broadcastable object
each has its own preferred style, and a promotion-like system is used to combine these
styles into a single answer — the "destination style".
-->
```
次の2つの段階(出力する配列と実装の選択)は、引数から決まる単一の解によって変わります。
ブロードキャストは型の変更を受けた引数すべてをとり、ただ一つの出力配列と実装に落とし込みます。
ブロードキャストでは、この一つの解を"スタイル"と呼びます。
各ブロードキャスト可能なオブジェクトには好みのスタイルがあり、昇格のような仕組みで、これらを結合した解である"目的のスタイル"が出されます。


`[](### Broadcast Styles)
### ブロードキャスト　スタイル

```@raw html
<!--
`Base.BroadcastStyle` is the abstract type from which all broadcast styles are derived. When used as a
function it has two possible forms, unary (single-argument) and binary. The unary variant states
that you intend to implement specific broadcasting behavior and/or output type, and do not wish to
rely on the default fallback [`Broadcast.DefaultArrayStyle`](@ref).

To override these defaults, you can define a custom `BroadcastStyle` for your object:
-->
```
抽象型の`Base.BroadcastStyle`からすべてのブロードキャストスタイルが派生します。
関数としては、単項（1個の引数）と二項の2つの形式で利用できます。

```julia
struct MyStyle <: Broadcast.BroadcastStyle end
Base.BroadcastStyle(::Type{<:MyType}) = MyStyle()
```

```@raw html
<!--
In some cases it might be convenient not to have to define `MyStyle`, in which case you can
leverage one of the general broadcast wrappers:

  - `Base.BroadcastStyle(::Type{<:MyType}) = Broadcast.Style{MyType}()` can be
    used for arbitrary types.
  - `Base.BroadcastStyle(::Type{<:MyType}) = Broadcast.ArrayStyle{MyType}()` is preferred
    if `MyType` is an `AbstractArray`.
  - For `AbstractArrays` that only support a certain dimensionality, create a subtype of `Broadcast.AbstractArrayStyle{N}` (see below).
-->
```


When your broadcast operation involves several arguments, individual argument styles get
combined to determine a single `DestStyle` that controls the type of the output container.
For more details, see [below](@ref writing-binary-broadcasting-rules).

`[](### Selecting an appropriate output array)
### Selecting an appropriate output array

The broadcast style is computed for every broadcasting operation to allow for
dispatch and specialization. The actual allocation of the result array is
handled by `similar`, using the Broadcasted object as its first argument.

```julia
Base.similar(bc::Broadcasted{DestStyle}, ::Type{ElType})
```

The fallback definition is

```julia
similar(bc::Broadcasted{DefaultArrayStyle{N}}, ::Type{ElType}) where {N,ElType} =
    similar(Array{ElType}, axes(bc))
```

However, if needed you can specialize on any or all of these arguments. The final argument
`bc` is a lazy representation of a (potentially fused) broadcast operation, a `Broadcasted`
object.  For these purposes, the most important fields of the wrapper are
`f` and `args`, describing the function and argument list, respectively.  Note that the argument
list can — and often does — include other nested `Broadcasted` wrappers.

For a complete example, let's say you have created a type, `ArrayAndChar`, that stores an
array and a single character:

```jldoctest ArrayAndChar; output = false
struct ArrayAndChar{T,N} <: AbstractArray{T,N}
    data::Array{T,N}
    char::Char
end
Base.size(A::ArrayAndChar) = size(A.data)
Base.getindex(A::ArrayAndChar{T,N}, inds::Vararg{Int,N}) where {T,N} = A.data[inds...]
Base.setindex!(A::ArrayAndChar{T,N}, val, inds::Vararg{Int,N}) where {T,N} = A.data[inds...] = val
Base.showarg(io::IO, A::ArrayAndChar, toplevel) = print(io, typeof(A), " with char '", A.char, "'")
# output

```

You might want broadcasting to preserve the `char` "metadata." First we define

```jldoctest ArrayAndChar; output = false
Base.BroadcastStyle(::Type{<:ArrayAndChar}) = Broadcast.ArrayStyle{ArrayAndChar}()
# output

```

This means we must also define a corresponding `similar` method:
```jldoctest ArrayAndChar; output = false
function Base.similar(bc::Broadcast.Broadcasted{Broadcast.ArrayStyle{ArrayAndChar}}, ::Type{ElType}) where ElType
    # Scan the inputs for the ArrayAndChar:
    A = find_aac(bc)
    # Use the char field of A to create the output
    ArrayAndChar(similar(Array{ElType}, axes(bc)), A.char)
end

"`A = find_aac(As)` returns the first ArrayAndChar among the arguments."
find_aac(bc::Base.Broadcast.Broadcasted) = find_aac(bc.args)
find_aac(args::Tuple) = find_aac(find_aac(args[1]), Base.tail(args))
find_aac(x) = x
find_aac(a::ArrayAndChar, rest) = a
find_aac(::Any, rest) = find_aac(rest)
# output
find_aac (generic function with 5 methods)
```

From these definitions, one obtains the following behavior:
```jldoctest ArrayAndChar
julia> a = ArrayAndChar([1 2; 3 4], 'x')
2×2 ArrayAndChar{Int64,2} with char 'x':
 1  2
 3  4

julia> a .+ 1
2×2 ArrayAndChar{Int64,2} with char 'x':
 2  3
 4  5

julia> a .+ [5,10]
2×2 ArrayAndChar{Int64,2} with char 'x':
  6   7
 13  14
```

`[](### [Extending broadcast with custom implementations](@id extending-in-place-broadcast))
### [Extending broadcast with custom implementations](@id extending-in-place-broadcast)

In general, a broadcast operation is represented by a lazy `Broadcasted` container that holds onto
the function to be applied alongside its arguments. Those arguments may themselves be more nested
`Broadcasted` containers, forming a large expression tree to be evaluated. A nested tree of
`Broadcasted` containers is directly constructed by the implicit dot syntax; `5 .+ 2.*x` is
transiently represented by `Broadcasted(+, 5, Broadcasted(*, 2, x))`, for example. This is
invisible to users as it is immediately realized through a call to `copy`, but it is this container
that provides the basis for broadcast's extensibility for authors of custom types. The built-in
broadcast machinery will then determine the result type and size based upon the arguments, allocate
it, and then finally copy the realization of the `Broadcasted` object into it with a default
`copyto!(::AbstractArray, ::Broadcasted)` method. The built-in fallback `broadcast` and
`broadcast!` methods similarly construct a transient `Broadcasted` representation of the operation
so they can follow the same codepath. This allows custom array implementations to
provide their own `copyto!` specialization to customize and
optimize broadcasting. This is again determined by the computed broadcast style. This is such
an important part of the operation that it is stored as the first type parameter of the
`Broadcasted` type, allowing for dispatch and specialization.

For some types, the machinery to "fuse" operations across nested levels of broadcasting
is not available or could be done more efficiently incrementally. In such cases, you may
need or want to evaluate `x .* (x .+ 1)` as if it had been
written `broadcast(*, x, broadcast(+, x, 1))`, where the inner operation is evaluated before
tackling the outer operation. This sort of eager operation is directly supported by a bit
of indirection; instead of directly constructing `Broadcasted` objects, Julia lowers the
fused expression `x .* (x .+ 1)` to `Broadcast.broadcasted(*, x, Broadcast.broadcasted(+, x, 1))`. Now,
by default, `broadcasted` just calls the `Broadcasted` constructor to create the lazy representation
of the fused expression tree, but you can choose to override it for a particular combination
of function and arguments.

As an example, the builtin `AbstractRange` objects use this machinery to optimize pieces
of broadcasted expressions that can be eagerly evaluated purely in terms of the start,
step, and length (or stop) instead of computing every single element. Just like all the
other machinery, `broadcasted` also computes and exposes the combined broadcast style of its
arguments, so instead of specializing on `broadcasted(f, args...)`, you can specialize on
`broadcasted(::DestStyle, f, args...)` for any combination of style, function, and arguments.

For example, the following definition supports the negation of ranges:

```julia
broadcasted(::DefaultArrayStyle{1}, ::typeof(-), r::OrdinalRange) = range(-first(r), step=-step(r), length=length(r))
```

`[](### [Extending in-place broadcasting](@id extending-in-place-broadcast))
### [Extending in-place broadcasting](@id extending-in-place-broadcast)

In-place broadcasting can be supported by defining the appropriate `copyto!(dest, bc::Broadcasted)`
method. Because you might want to specialize either on `dest` or the specific subtype of `bc`,
to avoid ambiguities between packages we recommend the following convention.

If you wish to specialize on a particular style `DestStyle`, define a method for
```julia
copyto!(dest, bc::Broadcasted{DestStyle})
```
Optionally, with this form you can also specialize on the type of `dest`.

If instead you want to specialize on the destination type `DestType` without specializing
on `DestStyle`, then you should define a method with the following signature:

```julia
copyto!(dest::DestType, bc::Broadcasted{Nothing})
```

This leverages a fallback implementation of `copyto!` that converts the wrapper into a
`Broadcasted{Nothing}`. Consequently, specializing on `DestType` has lower precedence than
methods that specialize on `DestStyle`.

Similarly, you can completely override out-of-place broadcasting with a `copy(::Broadcasted)`
method.

`[](#### Working with `Broadcasted` objects)
#### Working with `Broadcasted` objects

In order to implement such a `copy` or `copyto!`, method, of course, you must
work with the `Broadcasted` wrapper to compute each element. There are two main
ways of doing so:

* `Broadcast.flatten` recomputes the potentially nested operation into a single
  function and flat list of arguments. You are responsible for implementing the
  broadcasting shape rules yourself, but this may be helpful in limited situations.
* Iterating over the `CartesianIndices` of the `axes(::Broadcasted)` and using
  indexing with the resulting `CartesianIndex` object to compute the result.

`[](### [Writing binary broadcasting rules](@id writing-binary-broadcasting-rules))
### [Writing binary broadcasting rules](@id writing-binary-broadcasting-rules)

The precedence rules are defined by binary `BroadcastStyle` calls:

```julia
Base.BroadcastStyle(::Style1, ::Style2) = Style12()
```

where `Style12` is the `BroadcastStyle` you want to choose for outputs involving
arguments of `Style1` and `Style2`. For example,

```julia
Base.BroadcastStyle(::Broadcast.Style{Tuple}, ::Broadcast.AbstractArrayStyle{0}) = Broadcast.Style{Tuple}()
```

indicates that `Tuple` "wins" over zero-dimensional arrays (the output container will be a tuple).
It is worth noting that you do not need to (and should not) define both argument orders
of this call; defining one is sufficient no matter what order the user supplies the arguments in.

For `AbstractArray` types, defining a `BroadcastStyle` supersedes the fallback choice,
[`Broadcast.DefaultArrayStyle`](@ref). `DefaultArrayStyle` and the abstract supertype, `AbstractArrayStyle`, store the dimensionality as a type parameter to support specialized
array types that have fixed dimensionality requirements.

`DefaultArrayStyle` "loses" to any other
`AbstractArrayStyle` that has been defined because of the following methods:

```julia
BroadcastStyle(a::AbstractArrayStyle{Any}, ::DefaultArrayStyle) = a
BroadcastStyle(a::AbstractArrayStyle{N}, ::DefaultArrayStyle{N}) where N = a
BroadcastStyle(a::AbstractArrayStyle{M}, ::DefaultArrayStyle{N}) where {M,N} =
    typeof(a)(_max(Val(M),Val(N)))
```

You do not need to write binary `BroadcastStyle`
rules unless you want to establish precedence for
two or more non-`DefaultArrayStyle` types.

If your array type does have fixed dimensionality requirements, then you should
subtype `AbstractArrayStyle`. For example, the sparse array code has the following definitions:

```julia
struct SparseVecStyle <: Broadcast.AbstractArrayStyle{1} end
struct SparseMatStyle <: Broadcast.AbstractArrayStyle{2} end
Base.BroadcastStyle(::Type{<:SparseVector}) = SparseVecStyle()
Base.BroadcastStyle(::Type{<:SparseMatrixCSC}) = SparseMatStyle()
```

Whenever you subtype `AbstractArrayStyle`, you also need to define rules for combining
dimensionalities, by creating a constructor for your style that takes a `Val(N)` argument.
For example:

```julia
SparseVecStyle(::Val{0}) = SparseVecStyle()
SparseVecStyle(::Val{1}) = SparseVecStyle()
SparseVecStyle(::Val{2}) = SparseMatStyle()
SparseVecStyle(::Val{N}) where N = Broadcast.DefaultArrayStyle{N}()
```

These rules indicate that the combination of a `SparseVecStyle` with 0- or 1-dimensional arrays
yields another `SparseVecStyle`, that its combination with a 2-dimensional array
yields a `SparseMatStyle`, and anything of higher dimensionality falls back to the dense arbitrary-dimensional framework.
These rules allow broadcasting to keep the sparse representation for operations that result
in one or two dimensional outputs, but produce an `Array` for any other dimensionality.
