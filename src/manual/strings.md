`[](# [Strings](@id man-strings))
# [文字列](@id man-strings)

```@raw html
<!--
Strings are finite sequences of characters. Of course, the real trouble comes when one asks what
a character is. The characters that English speakers are familiar with are the letters `A`, `B`,
`C`, etc., together with numerals and common punctuation symbols. These characters are standardized
together with a mapping to integer values between 0 and 127 by the [ASCII](https://en.wikipedia.org/wiki/ASCII)
standard. There are, of course, many other characters used in non-English languages, including
variants of the ASCII characters with accents and other modifications, related scripts such as
Cyrillic and Greek, and scripts completely unrelated to ASCII and English, including Arabic, Chinese,
Hebrew, Hindi, Japanese, and Korean. The [Unicode](https://en.wikipedia.org/wiki/Unicode) standard
tackles the complexities of what exactly a character is, and is generally accepted as the definitive
standard addressing this problem. Depending on your needs, you can either ignore these complexities
entirely and just pretend that only ASCII characters exist, or you can write code that can handle
any of the characters or encodings that one may encounter when handling non-ASCII text. Julia
makes dealing with plain ASCII text simple and efficient, and handling Unicode is as simple and
efficient as possible. In particular, you can write C-style string code to process ASCII strings,
and they will work as expected, both in terms of performance and semantics. If such code encounters
non-ASCII text, it will gracefully fail with a clear error message, rather than silently introducing
corrupt results. When this happens, modifying the code to handle non-ASCII data is straightforward.
-->
```

文字列は、文字の有限列です。
しかし、文字とは何かと自問すると、なかなか厄介です。
英語圏で馴染みのある文字は、`A`、`B`、`C`などとか、数字とか、句読点などの普通の記号でしょう。
こうした文字はまとめて、0から127までの整数値を対応付ける[ASCII](https://en.wikipedia.org/wiki/ASCII)
規格として標準化されています。
当然、非英語圏には、多数の別の文字があります。
アクセントや修飾文字をつけた、ASCII文字の変種、近縁の言語であるキリル文字やギリシア文字、
ASCIIや英語に全く無縁の言語であるアラビア語、中国語、ヘブライ語、ヒンディ語、日本語、韓国語などです。
 [ユニコード](https://en.wikipedia.org/wiki/Unicode)規格は、文字とは正確には何なのかという複雑さに挑み、
 この問題を扱う最も信頼のおける規格として広く受け入れられています。
必要に応じて、複雑さを全く無視してASCII文字だけしか存在しないように振る舞うこともできますし、
非ASCIIの文書を扱う際に遭遇する、どんな文字やエンコードでも扱えるコードを書くこともできます。
Juliaでは、ASCII文書は単純で効率よく扱うこともできますし、Unicodeもできる限り単純で効率的に扱うこともできます。
特に、C言語流のASCII文字列を処理するコードを書くが可能で、パフォーマンス的にもセマンティック的にも期待通りに動作します。
こうしたコードは非ASCII文書に出くわすと潔く明快なエラーメッセージを出して失敗し、暗黙のまま壊れた結果を出すことはありません。
こうした場合に、非ASCIIデータを扱えるようにコードを変更するのは簡単です。


```@raw html
<!--
There are a few noteworthy high-level features about Julia's strings:
-->
```
Juliaの文字列には、顕著な高水準の特徴がいくつかあります。

```@raw html
<!--
  * The built-in concrete type used for strings (and string literals) in Julia is [`String`](@ref).
    This supports the full range of [Unicode](https://en.wikipedia.org/wiki/Unicode) characters via
    the [UTF-8](https://en.wikipedia.org/wiki/UTF-8) encoding. (A [`transcode`](@ref) function is
    provided to convert to/from other Unicode encodings.)
  * All string types are subtypes of the abstract type `AbstractString`, and external packages define
    additional `AbstractString` subtypes (e.g. for other encodings).  If you define a function expecting
    a string argument, you should declare the type as `AbstractString` in order to accept any string
    type.
  * Like C and Java, but unlike most dynamic languages, Julia has a first-class type for representing
    a single character, called [`AbstractChar`](@ref). The built-in [`Char`](@ref) subtype of `AbstractChar`
    is a 32-bit primitive type that can represent any Unicode character (and which is based
    on the UTF-8 encoding).
  * As in Java, strings are immutable: the value of an `AbstractString` object cannot be changed.
    To construct a different string value, you construct a new string from parts of other strings.
  * Conceptually, a string is a *partial function* from indices to characters: for some index values,
    no character value is returned, and instead an exception is thrown. This allows for efficient
    indexing into strings by the byte index of an encoded representation rather than by a character
    index, which cannot be implemented both efficiently and simply for variable-width encodings of
    Unicode strings.
-->
```

  * Juliaで文字列（や文字列リテラル）につかう組み込みの複合型が [`String`](@ref)です。
    [Unicode](https://en.wikipedia.org/wiki/Unicode) 全範囲の文字が[UTF-8](https://en.wikipedia.org/wiki/UTF-8) エンコーディングによって利用可能です。
    (関数 [`transcode`](@ref) が他のユニコードのエンコーディングとの双方向の変換に利用可能です。)   
  * すべての文字列型は抽象型`AbstractString`のサブタイプであり、外部パッケージで副次的な(別のエンコーディングなどの  `AbstractString`のサブタイプを定義します。
    引数として文字列を想定する関数を作るときは、どんな文字列でも受け入れられるように、型宣言は`AbstractString`にすべきでしょう。
  * C言語やJavaと同様に、しかしおおかたの動的言語とは違って、Juliaには、１字の文字に対して、 [`AbstractChar`](@ref)と呼ばれる、 
    第一級の型があります。
    32bitのプリミティブ型である組み込み型の [`Char`](@ref)は、`AbstractChar`のサブタイプで、
    任意のユニコードの文字を表現できます(UTF-8エンコードに基づいています)。
  * Javaと同様に文字列は不変です。`AbstractString`オブジェクトの値はかえられません。
    別の文字列を構成するには別の文字列の一部から構成します。
  * 概念的には、インデックスから文字への**部分関数**です。
    インデックスの一部は文字の値を返さず、かわりに例外を投げます。
    これによって、文字列のインデックスをエンコード表現からのバイトインデックスにして効率的にできます。
    文字のインデックスだとユニコードのエンコーディングの変数の幅が変わるので、効率性と単純さを両立できないのです。


`[](## [Characters](@id man-characters))
## [文字](@id man-characters)

```@raw html
<!--
A `Char` value represents a single character: it is just a 32-bit primitive type with a special literal
representation and appropriate arithmetic behaviors, and which can be converted
to a numeric value representing a
[Unicode code point](https://en.wikipedia.org/wiki/Code_point).  (Julia packages may define
other subtypes of `AbstractChar`, e.g. to optimize operations for other
[text encodings](https://en.wikipedia.org/wiki/Character_encoding).) Here is how `Char` values are
input and shown:
-->
```

`Char`の値は1文字を表しています。
これは32bitのプリミティブ型で特殊なリテラル表現をもち、適当な算術が可能です。
そして、[符号位置](https://en.wikipedia.org/wiki/Code_point)を表す数値に変換する事ができます。
 (Juliaのパッケージでは別の`AbstractChar`のサブタイプを定義することができます。
例えば、別の[文字エンコーディング](https://en.wikipedia.org/wiki/Character_encoding)の操作に最適化されたものなどです)
以下に`Char`の値の入力・表示の方法を示します。



```jldoctest
julia> 'x'
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)

julia> typeof(ans)
Char
```

You can easily convert a `Char` to its integer value, i.e. code point:

```jldoctest
julia> Int('x')
120

julia> typeof(ans)
Int64
```

```@raw html
<!--
On 32-bit architectures, [`typeof(ans)`](@ref) will be [`Int32`](@ref). You can convert an
integer value back to a `Char` just as easily:
-->
```
32bitアーキテクチャなら、[`typeof(ans)`](@ref)は[`Int32`](@ref)になるでしょう。
整数値を`Char`に戻すことも簡単にできます。


```jldoctest
julia> Char(120)
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)
```

```@raw html
<!--
Not all integer values are valid Unicode code points, but for performance, the `Char` conversion
does not check that every character value is valid. If you want to check that each converted value
is a valid code point, use the [`isvalid`](@ref) function:
-->
```

すべての整数値に有効なユニコードの符号位置が割り当てられているわけではありません。
しかしパフォーマンスをあげるため、`Char`の変換は文字の値が有効かどうか検査しません。
それぞれの変換された値が有効な符号位置かどうか調べるには、[`isvalid`](@ref)関数を使ってください。


```jldoctest
julia> Char(0x110000)
'\U110000': Unicode U+110000 (category In: Invalid, too high)

julia> isvalid(Char, 0x110000)
false
```

```@raw html
<!--
As of this writing, the valid Unicode code points are `U+00` through `U+d7ff` and `U+e000` through
`U+10ffff`. These have not all been assigned intelligible meanings yet, nor are they necessarily
interpretable by applications, but all of these values are considered to be valid Unicode characters.

You can input any Unicode character in single quotes using `\u` followed by up to four hexadecimal
digits or `\U` followed by up to eight hexadecimal digits (the longest valid value only requires
six):
-->
```

執筆時点では有効なユニコードの符号位置は、`U+00`から`U+d7ff`までと`U+e000`から`U+10ffff`までです。
この値は分かりやすい意味があるわけもなく、必ずしもアプリケーションが解釈できるわけではないですが、
すべて有効なユニコード文字だと考えられています。
ユニコードを入力するには、`\u`のあとに４桁までの16進数を続けたものか、`\U`のあとに8桁までのの16進数を続けたものを
一重引用符で囲みます。(最大の有効な桁数は6桁までなのですが)


```jldoctest
julia> '\u0'
'\0': ASCII/Unicode U+0000 (category Cc: Other, control)

julia> '\u78'
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)

julia> '\u2200'
'∀': Unicode U+2200 (category Sm: Symbol, math)

julia> '\U10ffff'
'\U10ffff': Unicode U+10ffff (category Cn: Other, not assigned)
```

```@raw html
<!--
Julia uses your system's locale and language settings to determine which characters can be printed
as-is and which must be output using the generic, escaped `\u` or `\U` input forms. In addition
to these Unicode escape forms, all of [C's traditional escaped input forms](https://en.wikipedia.org/wiki/C_syntax#Backslash_escapes)
can also be used:
-->
```
Juliaはシステムのロケールや言語設定に従って、どの文字をそのまま印字するか、
どの文字を`\u`や `\U`でエスケープする汎用的な入力形式で出力するかを判断します。 
さらに、これらユニコードのエスケープ形式の他に、すべての[C言語の従来のエスケープ入力形式](https://en.wikipedia.org/wiki/C_syntax#Backslash_escapes)も利用可能です。



```jldoctest
julia> Int('\0')
0

julia> Int('\t')
9

julia> Int('\n')
10

julia> Int('\e')
27

julia> Int('\x7f')
127

julia> Int('\177')
127
```

```@raw html
<!--
You can do comparisons and a limited amount of arithmetic with `Char` values:
-->
```
`Char`の値に対して、比較や、数は限られますが算術を行うことができます。

```jldoctest
julia> 'A' < 'a'
true

julia> 'A' <= 'a' <= 'Z'
false

julia> 'A' <= 'X' <= 'Z'
true

julia> 'x' - 'a'
23

julia> 'A' + 1
'B': ASCII/Unicode U+0042 (category Lu: Letter, uppercase)
```

`[](## String Basics)
## 文字列の基本

```@raw html
<!--
String literals are delimited by double quotes or triple double quotes:
-->
```

文字列リテラルは、二重引用符、または、３つ続けた二重引用符で区切ることができます。

```jldoctest helloworldstring
julia> str = "Hello, world.\n"
"Hello, world.\n"

julia> """Contains "quote" characters"""
"Contains \"quote\" characters"
```

```@raw html
<!--
If you want to extract a character from a string, you index into it:
-->
```

文字列から文字を抜き出したいときは、インデックスが使えます

```jldoctest helloworldstring
julia> str[1]
'H': ASCII/Unicode U+0048 (category Lu: Letter, uppercase)

julia> str[6]
',': ASCII/Unicode U+002c (category Po: Punctuation, other)

julia> str[end]
'\n': ASCII/Unicode U+000a (category Cc: Other, control)
```

```@raw html
<!--
Many Julia objects, including strings, can be indexed with integers. The index of the first
element is returned by [`firstindex(str)`](@ref), and the index of the last element
with [`lastindex(str)`](@ref). The keyword `end` can be used inside an indexing
operation as shorthand for the last index along the given dimension.
Most indexing in Julia is 1-based: the first element of many integer-indexed objects is found at
index 1. (As we will see below, this does not necessarily mean that the last element is found
at index `n`, where `n` is the length of the string.)

You can perform arithmetic and other operations with [`end`](@ref), just like
a normal value:
-->
```

多くのJuliaのオブジェクトは文字列も含めて、整数のインデックスづけが可能です。
先頭の要素のインデックスは[`firstindex(str)`](@ref)で、最後の要素のインデックスは[`lastindex(str)`](@ref)で得られます。
キーワード`end`は、インデックス操作時に該当次元の最後のインデックスを示す簡略記法として利用できます。
Juliaでは殆どのインデックスが1を基準としています。
整数でインデックス付けされたオブジェクトの多数で、最初の要素のインデックスは１です。
(だからといって、下記で見るように、文字列の長さ`n`が、そのまま最後の要素のインデックスとなるわけでは、必ずしもありません。)



```jldoctest helloworldstring
julia> str[end-1]
'.': ASCII/Unicode U+002e (category Po: Punctuation, other)

julia> str[end÷2]
' ': ASCII/Unicode U+0020 (category Zs: Separator, space)
```

```@raw html
<!--
Using an index less than 1 or greater than `end` raises an error:
-->
```

インデックスが1より小さい場合や`end`より大きい場合はエラーが生じます。



```jldoctest helloworldstring
julia> str[0]
ERROR: BoundsError: attempt to access "Hello, world.\n"
  at index [0]
[...]

julia> str[end+1]
ERROR: BoundsError: attempt to access "Hello, world.\n"
  at index [15]
Stacktrace:
[...]
```

```@raw html
<!--
You can also extract a substring using range indexing:
-->
```

部分文字列を範囲インデックスを使って抜き出すこともできます。

```jldoctest helloworldstring
julia> str[4:9]
"lo, wo"
```

```@raw html
<!--
Notice that the expressions `str[k]` and `str[k:k]` do not give the same result:
-->
```
`str[k]`と`str[k:k]`の式は同じ演算結果を返さないので注意してください。。


```jldoctest helloworldstring
julia> str[6]
',': ASCII/Unicode U+002c (category Po: Punctuation, other)

julia> str[6:6]
","
```

```@raw html
<!--
The former is a single character value of type `Char`, while the latter is a string value that
happens to contain only a single character. In Julia these are very different things.

Range indexing makes a copy of the selected part of the original string.
Alternatively, it is possible to create a view into a string using the type [`SubString`](@ref),
for example:
-->
```

前者は型`Char`の文字１字の値、他方後者はたまた1文字しか含まない文字列の値です
Juliaではこれらは全く別物です。

範囲インデックスでは、元の文字列に対して選択部分のコピーを作成します。
別の方法として、型 [`SubString`](@ref)を使って、文字列に対するビューを作成することも可能です。


```jldoctest
julia> str = "long string"
"long string"

julia> substr = SubString(str, 1, 4)
"long"

julia> typeof(substr)
SubString{String}
```

```@raw html
<!--
Several standard functions like [`chop`](@ref), [`chomp`](@ref) or [`strip`](@ref)
return a [`SubString`](@ref).
-->
```
いくつかの標準的な関数[`chop`](@ref)、[`chomp`](@ref) 、 [`strip`](@ref)などは、
 [`SubString`](@ref)の型を返します。

`[](## Unicode and UTF-8)
## ユニコードとUTF-8

```@raw html
<!--
Julia fully supports Unicode characters and strings. As [discussed above](@ref man-characters), in character
literals, Unicode code points can be represented using Unicode `\u` and `\U` escape sequences,
as well as all the standard C escape sequences. These can likewise be used to write string literals:
-->
```

Juliaはユニコードの文字と文字列に完全対応しています。
[上述](@ref man-characters)のように、ユニコードの`\u`や`\U`のエスケープシーケンスや、
すべての標準的なC言語のエスケープシーケンスなどの文字リテラルを使って、ユニコードの符号位置を表現することができます。
これらは、文字列リテラルにも利用できます。



```jldoctest unicodestring
julia> s = "\u2200 x \u2203 y"
"∀ x ∃ y"
```

```@raw html
<!--
Whether these Unicode characters are displayed as escapes or shown as special characters depends
on your terminal's locale settings and its support for Unicode. String literals are encoded using
the UTF-8 encoding. UTF-8 is a variable-width encoding, meaning that not all characters are encoded
in the same number of bytes. In UTF-8, ASCII characters -- i.e. those with code points less than
0x80 (128) -- are encoded as they are in ASCII, using a single byte, while code points 0x80 and
above are encoded using multiple bytes -- up to four per character. This means that not every
byte index into a UTF-8 string is necessarily a valid index for a character. If you index into
a string at such an invalid byte index, an error is thrown:
-->
```

ユニコードの文字がエスケープされて表示されるか、特殊な文字で表示されるかは、
ターミナルのロケールの設定や、ユニコードへの対応状況に依存します。
文字列リテラルは、UTF-8でエンコードされます。
UTF-8は可変長のエンコーディングです。つまり、すべての文字が同じバイト数にエンコードされるわけではありません。
UTF-8ではASCII文字、つまり符号位置が0x80 (128)未満の文字は、 ASCIIのまま１バイトでエンコードされます。
他方符号位置が0x80 (128)以上の文字は、４文字までの複数の文字でエンコードされます。
このため、UTF-8の文字列に対するバイトインデックスは、すべてに有効な文字が割り当てられているわけではないことになります。
もし文字列に対して、このような無効なインデックス呼び出しをすると、エラーが投げられます。



```jldoctest unicodestring
julia> s[1]
'∀': Unicode U+2200 (category Sm: Symbol, math)

julia> s[2]
ERROR: StringIndexError("∀ x ∃ y", 2)
[...]

julia> s[3]
ERROR: StringIndexError("∀ x ∃ y", 3)
Stacktrace:
[...]

julia> s[4]
' ': ASCII/Unicode U+0020 (category Zs: Separator, space)
```

```@raw html
<!--
In this case, the character `∀` is a three-byte character, so the indices 2 and 3 are invalid
and the next character's index is 4; this next valid index can be computed by [`nextind(s,1)`](@ref),
and the next index after that by `nextind(s,4)` and so on.

Extraction of a substring using range indexing also expects valid byte indices or an error is thrown:
-->
```

この場合、`∀`という文字は３バイトの文字なので、２番目と３番目のインデックスは無効で、次の文字のインデックスは４です。
次の有効なインデックスは[`nextind(s,1)`](@ref)で計算できて、その次は[`nextind(s,4)`](@ref)と続いていきます。

部分文字列を抜き出す範囲インデックスも有効なインデックスを想定していて、無効ならばエラーが生じます。



```jldoctest unicodestring
julia> s[1:1]
"∀"

julia> s[1:2]
ERROR: StringIndexError("∀ x ∃ y", 2)
Stacktrace:
[...]

julia> s[1:4]
"∀ "
```

```@raw html
<!--
Because of variable-length encodings, the number of characters in a string (given by [`length(s)`](@ref))
is not always the same as the last index. If you iterate through the indices 1 through [`lastindex(s)`](@ref)
and index into `s`, the sequence of characters returned when errors aren't thrown is the sequence
of characters comprising the string `s`. Thus we have the identity that `length(s) <= lastindex(s)`,
since each character in a string must have its own index. The following is an inefficient and
verbose way to iterate through the characters of `s`:
-->
```

エンコーディングが可変長であるため、文字列の文字数（[`length(s)`](@ref)で得られます）と最後尾のインデックスは必ずしも一致しません。
 1から[`lastindex(s)`](@ref)まで`s`へのインデックス呼び出しを繰り返すと、エラーがスローされない時に、`s`を構成する一連の文字が
順に返されます。
このように、バイトインデックスと文字列インデックスを同一視する写像`length(s) <= lastindex(s)`が得られます。
というのも、文字列中の文字は自身のインデックスを必ず持つからです。
下記は非効率で冗長な方法で、`s`に対して繰り返しを行っています。

```jldoctest unicodestring
julia> for i = firstindex(s):lastindex(s)
           try
               println(s[i])
           catch
               # ignore the index error
           end
       end
∀

x

∃

y
```

```@raw html
<!--
The blank lines actually have spaces on them. Fortunately, the above awkward idiom is unnecessary
for iterating through the characters in a string, since you can just use the string as an iterable
object, no exception handling required:
-->
```

上の空行には、実際には空白があります。
幸い、上記の不格好な書き方をしなくても、文字列中の文字に対する反復を行うことができます。
というのも、文字列はイテラブルなオブジェクトなので、例外処理を行う必要もありません。


```jldoctest unicodestring
julia> for c in s
           println(c)
       end
∀

x

∃

y
```

```@raw html
<!--
Strings in Julia can contain invalid UTF-8 code unit sequences. This convention allows to
treat any byte sequence as a `String`. In such situations a rule is that when parsing
a sequence of code units from left to right characters are formed by the longest sequence of
8-bit code units that matches the start of one of the following bit patterns
(each `x` can be `0` or `1`):
-->
```

Juliaでは、UTF-8の文字列としては妥当ではないバイト列を文字列に含むことができます。
このしくみによって、任意のバイト列を`文字列`として扱うことができます。
そんな条件のもと、左から右へバイト列を解析する規則は、
下記のビットパターンから始まる最長のバイト列を取り出すことです。
(各`x`は`0`でも`1`でもよい)



* `0xxxxxxx`;
* `110xxxxx` `10xxxxxx`;
* `1110xxxx` `10xxxxxx` `10xxxxxx`;
* `11110xxx` `10xxxxxx` `10xxxxxx` `10xxxxxx`;
* `10xxxxxx`;
* `11111xxx`.

```@raw html
<!--
In particular this implies that overlong and too high code unit sequences are accepted.
This rule is best explained by an example:
-->
```

この規則では、冗長だったり、値が大きすぎるコードも受け入れることになります。
実例で説明するほうがいいでしょう。


```julia-repl
julia> s = "\xc0\xa0\xe2\x88\xe2|"
"\xc0\xa0\xe2\x88\xe2|"

julia> foreach(display, s)
'\xc0\xa0': [overlong] ASCII/Unicode U+0020 (category Zs: Separator, space)
'\xe2\x88': Malformed UTF-8 (category Ma: Malformed, bad data)
'\xe2': Malformed UTF-8 (category Ma: Malformed, bad data)
'|': ASCII/Unicode U+007c (category Sm: Symbol, math)

julia> isvalid.(collect(s))
4-element BitArray{1}:
 false
 false
 false
  true

julia> s2 = "\xf7\xbf\xbf\xbf"
"\U1fffff"

julia> foreach(display, s2)
'\U1fffff': Unicode U+1fffff (category In: Invalid, too high)
```

```@raw html
<!--
We can see that the first two code units in the string `s` form an overlong encoding of
space character. It is invalid, but is accepted in a string as a single character.
The next two code units form a valid start of a three-byte UTF-8 sequence. However, the fifth
code unit `\xe2` is not its valid continuation. Therefore code units 3 and 4 are also
interpreted as malformed characters in this string. Similarly code unit 5 forms a malformed
character because `|` is not a valid continuation to it. Finally the string `s2` contains
one too high code point.
-->
```

`s`の始めの２符号単位、はスペースの冗長なエンコーディングです。
これは無効なものですが、１字の文字として、文字列として受け入れられます。
次の２符号単位は、3符号単位のUTF-8の始まりとして有効ですが、５番目の`\xe2`はその続きとして無効です。
よって３符号単位目と４符号単位目も文字列として不正な形式のものです。
同様に、５符号単位目も`|`が続きとして無効なので、不正な形式の文字です。
最後に文字列`s2`は符号位置が大きすぎます。



```@raw html
<!--
Julia uses the UTF-8 encoding by default, and support for new encodings can be added by packages.
For example, the [LegacyStrings.jl](https://github.com/JuliaArchive/LegacyStrings.jl) package
implements `UTF16String` and `UTF32String` types. Additional discussion of other encodings and
how to implement support for them is beyond the scope of this document for the time being. For
further discussion of UTF-8 encoding issues, see the section below on [byte array literals](@ref man-byte-array-literals).
The [`transcode`](@ref) function is provided to convert data between the various UTF-xx encodings,
primarily for working with external data and libraries.
-->
```

JuliaはデフォルトでUTF-8でエンコーディングを行いますが、パッケージを加えて新しいエンコーディングに対応することができます。
例えば、[LegacyStrings.jl](https://github.com/JuliaArchive/LegacyStrings.jl)パッケージは、`UTF16String`型や`UTF32String`型を実装しています。
別のエンコーディングや、それに対応するための実装方法に関する議論は、当面、この文書の範囲外です。
UTF-8エンコーディングの問題に関するさらなる議論は、下記セクションの[バイト列リテラル](@ref man-byte-array-literals)を参照してください。
[`transcode`](@ref)関数は、様々なUTF-xxエンコーディング間でデータを変換し、主に外部のデータやライブラリと一緒に動作させます。



`[](## Concatenation)
## 連結

```@raw html
<!--
One of the most common and useful string operations is concatenation:
-->
```

最も普通で役に立つ文字列操作の一つが、連結です。


```jldoctest stringconcat
julia> greet = "Hello"
"Hello"

julia> whom = "world"
"world"

julia> string(greet, ", ", whom, ".\n")
"Hello, world.\n"
```

```@raw html
<!--
It's important to be aware of potentially dangerous situations such as concatenation of invalid UTF-8 strings.
The resulting string may contain different characters than the input strings,
and its number of characters may be lower than sum of numbers of characters
of the concatenated strings, e.g.:
-->
```

無効な文字列を連結する場合に起こりうる潜在的な危険に注意を払うことは重要です。
連結した文字列は、入力した文字列と変わるかもしれないし、もとの文字列の文字数のの合計よりも、文字数が少なくなるかもしれません。
例えば、

```julia-repl
julia> a, b = "\xe2\x88", "\x80"
("\xe2\x88", "\x80")

julia> c = a*b
"∀"

julia> collect.([a, b, c])
3-element Array{Array{Char,1},1}:
 ['\xe2\x88']
 ['\x80']
 ['∀']

julia> length.([a, b, c])
3-element Array{Int64,1}:
 1
 1
 1
```

```@raw html
<!--
This situation can happen only for invalid UTF-8 strings. For valid UTF-8 strings
concatenation preserves all characters in strings and additivity of string lengths.

Julia also provides [`*`](@ref) for string concatenation:
-->
```

こういうことが起こりうるのは、無効な UTF-8の文字列のときだけです。
有効なUTF-8文字列を連結した時は、文字列の文字数の合計が保たれます。




```jldoctest stringconcat
julia> greet * ", " * whom * ".\n"
"Hello, world.\n"
```

```@raw html
<!--
While `*` may seem like a surprising choice to users of languages that provide `+` for string
concatenation, this use of `*` has precedent in mathematics, particularly in abstract algebra.

In mathematics, `+` usually denotes a *commutative* operation, where the order of the operands does
not matter. An example of this is matrix addition, where `A + B == B + A` for any matrices `A` and `B`
that have the same shape. In contrast, `*` typically denotes a *noncommutative* operation, where the
order of the operands *does* matter. An example of this is matrix multiplication, where in general
`A * B != B * A`. As with matrix multiplication, string concatenation is noncommutative:
`greet * whom != whom * greet`. As such, `*` is a more natural choice for an infix string concatenation
operator, consistent with common mathematical use.

More precisely, the set of all finite-length strings *S* together with the string concatenation operator
`*` forms a [free monoid](https://en.wikipedia.org/wiki/Free_monoid) (*S*, `*`). The identity element
of this set is the empty string, `""`. Whenever a free monoid is not commutative, the operation is
typically represented as `\cdot`, `*`, or a similar symbol, rather than `+`, which as stated usually
implies commutativity.
-->
```

文字列の連結に`+`を使う言語のユーザーは、`*`を使う選択にびっくりするかもしれませんが、
これには数学、特に抽象代数に先例があります。

数学では、`+`はたいてい **可換的** 操作を表し、演算対象の順序は影響しません。
例としては、行列の足し算があり、形の同じ任意の行列 `A`と`B`に対して、`A + B == B + A`が成り立ちます。
一方、`*`ふつうは **非可換的** 操作を表し、演算対象の順序は影響します。
例としては、行列の掛け算があり、一般には、行列 `A`と`B`に対して、`A * B != B * A`が成り立ちます。
行列の掛け算と同様に文字列の連結も非可換です。
`greet * whom != whom * greet`です。
よって、通常の数学での使い方との一貫性を考えると、文字列連結の中置演算には、`*`を選択するほうが、より自然なのです。

更に正確には、すべての有限長の文字列の集合 *S* と 文字列連結演算子 `*`は、　
[自由モノイド](https://en.wikipedia.org/wiki/Free_monoid) (*S*, `*`)を形成します。
恒等元は空の文字列`""`です。
自由モノイドが非可換の時は、演算子は通常、`\cdot`、`*`などの記号で表し、通常、可換性を示唆する`+`は使いません。



`[](## [Interpolation](@id string-interpolation))
## [文字列展開](@id string-interpolation)

```@raw html
<!--
Constructing strings using concatenation can become a bit cumbersome, however. To reduce the need for these
verbose calls to [`string`](@ref) or repeated multiplicaions, Julia allows interpolation into string literals
using `$`, as in Perl:
-->
```

しかし、文字列を連結で構成するが厄介な場合もあります。[`string`](@ref)の冗長な呼び出しや乗算記号の反復をへらすために、
Juliaでは、Perlのように`$`を使った、文字列リテラルの展開が利用できます。


```jldoctest stringconcat
julia> "$greet, $whom.\n"
"Hello, world.\n"
```

```@raw html
<!--
This is more readable and convenient and equivalent to the above string concatenation -- the system
rewrites this apparent single string literal into a concatenation of string literals with variables.

The shortest complete expression after the `$` is taken as the expression whose value is to be
interpolated into the string. Thus, you can interpolate any expression into a string using parentheses:
-->
```

これは、読みやすく、便利で、上記の文字列連結と同等ですが、
システムが、見た目は１つの文字列リテラルを、変数と文字列リテラルの連結に書き換えています。

`$`に続く最短の完全な式を、展開の対象とみなすので、任意の式を括弧を使って展開することができます。






```jldoctest
julia> "1 + 2 = $(1 + 2)"
"1 + 2 = 3"
```

```@raw html
<!--
Both concatenation and string interpolation call [`string`](@ref) to convert objects into string
form. Most non-`AbstractString` objects are converted to strings closely corresponding to how
they are entered as literal expressions:
-->
```

連結も文字列展開も[`string`](@ref)を呼び出して、オブジェクトを文字列に変換します。
たいていの`AbstractString`ではないオブジェクトが文字列に変換されるのは、どのようにリテラル式として入力されたのかに密接に関連します。


```jldoctest
julia> v = [1,2,3]
3-element Array{Int64,1}:
 1
 2
 3

julia> "v: $v"
"v: [1, 2, 3]"
```

```@raw html
<!--
[`string`](@ref) is the identity for `AbstractString` and `AbstractChar` values, so these are interpolated
into strings as themselves, unquoted and unescaped:
-->
```
[`string`](@ref) は `AbstractString` や`AbstractChar`の値と同等です。
そのため、そのままクオートやエスケープせずに、展開されます。



```jldoctest
julia> c = 'x'
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)

julia> "hi, $c"
"hi, x"
```

```@raw html
<!--
To include a literal `$` in a string literal, escape it with a backslash:
-->
```

リテラルの`$`を文字列リテラルに含めるには、バックスラッシュでエスケープします。

```jldoctest
julia> print("I have \$100 in my account.\n")
I have $100 in my account.
```

`[](## Triple-Quoted String Literals)
## 三連クオート文字列リテラル


```@raw html
<!--
When strings are created using triple-quotes (`"""..."""`) they have some special behavior that
can be useful for creating longer blocks of text.

First, triple-quoted strings are also dedented to the level of the least-indented line.
This is useful for defining strings within code that is indented. For example:
-->
```

３連クオート(`"""..."""`)を使って生成した文字列は、長い文章のブロックを作るのに役立つ特殊な挙動をします。

まず、３連クオート文字列、１番インデントの少ない行に合わせて、インデントを除去します。
これは、コード内にインデントのある文字列を定義するのに便利です。例えば、


```jldoctest
julia> str = """
           Hello,
           world.
         """
"  Hello,\n  world.\n"
```

```@raw html
<!--
In this case the final (empty) line before the closing `"""` sets the indentation level.

The dedentation level is determined as the longest common starting sequence of spaces or
tabs in all lines, excluding the line following the opening `"""` and lines containing
only spaces or tabs (the line containing the closing `"""` is always included).
Then for all lines, excluding the text following the opening `"""`, the common starting
sequence is removed (including lines containing only spaces and tabs if they start with
this sequence), e.g.:
-->
```

この場合は、終了の`"""`の前の最後の（空）行 をインデントの基準として設定します。

インデント除去の水準は、開始の`"""`の行や、空白かタブしか含まない行を除くすべての行が共通して
開始の空白やタブであるシーケンスの最も長いものとして決定されます。
(終了の`"""`の行は常に含まれます)。
するとすべての行(開始の`"""`はのぞいて)の開始までの共通のシーケンスは除去されます。
（空白やタブの行も開始までの部分があるものは除去されます）
例えば、


```jldoctest
julia> """    This
         is
           a test"""
"    This\nis\n  a test"
```

```@raw html
<!--
Next, if the opening `"""` is followed by a newline,
the newline is stripped from the resulting string.
-->
```

次に、開始の `"""`の改行が続く場合、その改行は結果として得られる文字列から除去されます。

```julia
"""hello"""
```

```@raw html
<!--
is equivalent to
-->
```

は下記と同等です。

```julia
"""
hello"""
```

```@raw html
<!--
but
-->
```

しかし、

```julia
"""

hello"""
```

```@raw html
<!--
will contain a literal newline at the beginning.

Stripping of the newline is performed after the dedentation. For example:
-->
```

は改行リテラルが最初に来るかもしれません。

改行の除去はインデントの除去のあとに行います。例えば、



```jldoctest
julia> """
         Hello,
         world."""
"Hello,\nworld."
```

```@raw html
<!--
Trailing whitespace is left unaltered.

Triple-quoted string literals can contain `"` symbols without escaping.

Note that line breaks in literal strings, whether single- or triple-quoted, result in a newline
(LF) character `\n` in the string, even if your editor uses a carriage return `\r` (CR) or CRLF
combination to end lines. To include a CR in a string, use an explicit escape `\r`; for example,
you can enter the literal string `"a CRLF line ending\r\n"`.
-->
```

続きの空白は変わらないままです。

３連クオートには、エスケープなしで`"`記号を入れることができます。

注意が必要なのは、文字列中の改行は、クオートが1連でも3連でも、ラインフィード(LF)文字の`\n`になります。
エディタの設定がキャリッジリターン(CR)`\r`やC組み合わせのCRLFであってもです。
CRを残したい場合は、明示的にエスケープして`\r`のようにいれてください。
例えば、`"a CRLF line ending\r\n"`のように文字列リテラルを入力できます。




`[](## Common Operations)
## よくある処理

```@raw html
<!--
You can lexicographically compare strings using the standard comparison operators:
-->
```
辞書順で比較を行うには、標準の比較演算子を利用できます。

```jldoctest
julia> "abracadabra" < "xylophone"
true

julia> "abracadabra" == "xylophone"
false

julia> "Hello, world." != "Goodbye, world."
true

julia> "1 + 2 = 3" == "1 + 2 = $(1 + 2)"
true
```

```@raw html
<!--
You can search for the index of a particular character using the [`findfirst`](@ref) function:
-->
```

特定の文字のインデックスを[`findfirst`](@ref)関数を使って検索できます。

```jldoctest
julia> findfirst(isequal('x'), "xylophone")
1

julia> findfirst(isequal('p'), "xylophone")
5

julia> findfirst(isequal('z'), "xylophone")
```

```@raw html
<!--
You can start the search for a character at a given offset by using [`findnext`](@ref)
with a third argument:
-->
```
 [`findnext`](@ref)は３番目の引数で指定した数だけ、検索の開始位置をずらすことができます。



```jldoctest
julia> findnext(isequal('o'), "xylophone", 1)
4

julia> findnext(isequal('o'), "xylophone", 5)
7

julia> findnext(isequal('o'), "xylophone", 8)
```

```@raw html
<!--
You can use the [`occursin`](@ref) function to check if a substring is found within a string:
-->
```
[`occursin`](@ref)関数を使うと、文字列の中に部分列があるかどうかを検査することができます。


```jldoctest
julia> occursin("world", "Hello, world.")
true

julia> occursin("o", "Xylophon")
true

julia> occursin("a", "Xylophon")
false

julia> occursin('o', "Xylophon")
true
```

```@raw html
<!--
The last example shows that [`occursin`](@ref) can also look for a character literal.

Two other handy string functions are [`repeat`](@ref) and [`join`](@ref):
-->
```
最後の例は、 [`occursin`](@ref)が、文字リテラルを探す時にも利用できることを示しています。

[`repeat`](@ref)や[`join`](@ref)はまた別の便利な文字列関数です。



```jldoctest
julia> repeat(".:Z:.", 10)
".:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:."

julia> join(["apples", "bananas", "pineapples"], ", ", " and ")
"apples, bananas and pineapples"
```



```@raw html
<!--
Some other useful functions include:

  * [`firstindex(str)`](@ref) gives the minimal (byte) index that can be used to index into `str` (always 1 for strings, not necessarily true for other containers).
  * [`lastindex(str)`](@ref) gives the maximal (byte) index that can be used to index into `str`.
  * [`length(str)`](@ref) the number of characters in `str`.
  * [`length(str, i, j)`](@ref) the number of valid character indices in `str` from `i` to `j`.
  * [`ncodeunits(str)`](@ref) number of [code units](https://en.wikipedia.org/wiki/Character_encoding#Terminology) in a string.
  * [`codeunit(str, i)`](@ref) gives the code unit value in the string `str` at index `i`.
  * [`thisind(str, i)`](@ref) given an arbitrary index into a string find the first index of the character into which the index points.
  * [`nextind(str, i, n=1)`](@ref) find the start of the `n`th character starting after index `i`.
  * [`prevind(str, i, n=1)`](@ref) find the start of the `n`th character starting before index `i`.

-->
```


他に、便利な関数を挙げていくと、

  * [`firstindex(str)`](@ref) では、`str`の最小の(バイト)インデックスが得られます。(文字列に対しては常に１になりますが、他のコンテナではそうとは限りません)。
  * [`lastindex(str)`](@ref) では、`str`の最大の(バイト)インデックスが得られます。
  * [`length(str)`](@ref) では、`str`の文字数が得られます。
  * [`length(str, i, j)`](@ref) では、 `str` の `i` から `j`までで有効なインデックスの数が得られます・
  * [`ncodeunits(str)`](@ref)では、 文字列中の[符号単位](https://en.wikipedia.org/wiki/Character_encoding#Terminology)の数が得られます。
  * [`codeunit(str, i)`](@ref)では、文字列`str`のインデックスが`i`符号単位の値が得られます。
  * [`thisind(str, i)`](@ref) では、任意のインデックスに対して、その指し示す文字に対する最初のインデックスが得られます。
  * [`nextind(str, i, n=1)`](@ref) インデックス `i`の文字の'`n`個後ろの文字の先頭のインデックスか得られます。
  * [`prevind(str, i, n=1)`](@ref) インデックス `i`の文字の'`n`個前の文字の先頭のインデックスか得られます。


`[](## [Non-Standard String Literals](@id non-standard-string-literals))
## [非標準文字列リテラル](@id non-standard-string-literals)

```@raw html
<!--
There are situations when you want to construct a string or use string semantics, but the behavior
of the standard string construct is not quite what is needed. For these kinds of situations, Julia
provides [non-standard string literals](@ref). A non-standard string literal looks like a regular
double-quoted string literal, but is immediately prefixed by an identifier, and doesn't behave
quite like a normal string literal.  Regular expressions, byte array literals and version number
literals, as described below, are some examples of non-standard string literals. Other examples
are given in the [Metaprogramming](@ref) section.
-->
```
文字列は使いたいけど、通常の使い方では、要求にそぐわないという状況もあるでしょう。
そんな場合のために、Juliaには、 [非標準文字列リテラル](@ref)が用意されています。
非標準文字列リテラルは、ふつうの二重引用符による文字列リテラルに似ていますが、識別用の文字が頭についていて、挙動が通常の文字とは異なります。
正規表現、バイト列リテラル、バージョン番号リテラルなど、非標準文字列リテラルの例をこれからいくつか見ていきます。
他の例は[メタプログラミング](@ref) セクションで扱います。



`[](## Regular Expressions)
## 正規表現

```@raw html
<!--
Julia has Perl-compatible regular expressions (regexes), as provided by the [PCRE](http://www.pcre.org/)
library. Regular expressions are related to strings in two ways: the obvious connection is that
regular expressions are used to find regular patterns in strings; the other connection is that
regular expressions are themselves input as strings, which are parsed into a state machine that
can be used to efficiently search for patterns in strings. In Julia, regular expressions are input
using non-standard string literals prefixed with various identifiers beginning with `r`. The most
basic regular expression literal without any options turned on just uses `r"..."`:

-->
```

JuliaではPerl互換の正規表現が利用可能で、[PCRE](http://www.pcre.org/)ライブラリを利用しています。
正規表現と文字列には２通りの関係があります。
一つは、文字列から正規パターンを見つけるために正規表現が使われること。
もう一つは、正規表現自体を文字列として入力して解析し、効率的に文字列内のパターンを探索できるステートマシンを構築することです。
Juliaでは、正規表現の入力には、`r`で始まる様々な識別子を頭につけた非標準文字列リテラルを利用します。
最も基本的な正規表現リテラルは、何もオプションを付けない`r"..."`です。


```jldoctest
julia> r"^\s*(?:#|$)"
r"^\s*(?:#|$)"

julia> typeof(ans)
Regex
```

```@raw html
<!--
To check if a regex matches a string, use [`occursin`](@ref):
-->
```
正規表現が文字列に一致するかどうかを調べるには、 [`occursin`](@ref)を使います。


```jldoctest
julia> occursin(r"^\s*(?:#|$)", "not a comment")
false

julia> occursin(r"^\s*(?:#|$)", "# a comment")
true
```

```@raw html
<!--
As one can see here, [`occursin`](@ref) simply returns true or false, indicating whether a
match for the given regex occurs in the string. Commonly, however, one wants to know not
just whether a string matched, but also *how* it matched. To capture this information about
a match, use the [`match`](@ref) function instead:
-->
```

見てのとおり、[`occursin`](@ref)は単に正規表現が文字列と一致するかどうか真偽値を返すだけです。
しかし、通常は、単に一致しているかどうかだけでなく、**どのように** 一致しているかを知りたいものです。
一致に関する情報を捕捉するには、 [`match`](@ref) 関数をかわりに使います。



```jldoctest
julia> match(r"^\s*(?:#|$)", "not a comment")

julia> match(r"^\s*(?:#|$)", "# a comment")
RegexMatch("#")
```

```@raw html
<!--
If the regular expression does not match the given string, [`match`](@ref) returns [`nothing`](@ref)
-- a special value that does not print anything at the interactive prompt. Other than not printing,
it is a completely normal value and you can test for it programmatically:
-->
```

正規表現が文字列と一致しない場合、[`match`](@ref) 関数は [`nothing`](@ref)を返します。
--これは、特殊な値で対話プロンプトでは何も表示されません。表示以外は全く普通の値なので、プログラムによって検査をすることもできます。


```julia
m = match(r"^\s*(?:#|$)", line)
if m === nothing
    println("not a comment")
else
    println("blank or comment")
end
```

```@raw html
<!--
If a regular expression does match, the value returned by [`match`](@ref) is a `RegexMatch`
object. These objects record how the expression matches, including the substring that the pattern
matches and any captured substrings, if there are any. This example only captures the portion
of the substring that matches, but perhaps we want to capture any non-blank text after the comment
character. We could do the following:
-->
```

正規表現が一致する場合、 [`match`](@ref)の返す値は、`RegexMatch`オブジェクトです。
このオブジェクトには、式の一致の状態が記録されていて、パターンと一致した部分文字列や、存在する場合には捕捉した文字列がわかります。
この例では、一致した部分文字列を捕捉しているだけですが、おそらく、コメントのあとの空白ではない文章も捕捉したいでしょう。
以下のようにすれば、可能です。

```jldoctest
julia> m = match(r"^\s*(?:#\s*(.*?)\s*$|$)", "# a comment ")
RegexMatch("# a comment ", 1="a comment")
```

```@raw html
<!--
When calling [`match`](@ref), you have the option to specify an index at which to start the
search. For example:
-->
```

[`match`](@ref)を呼び出す時は、必要に応じて、どのインデックスから検索を始めるか指定することができます。
例えば、

```jldoctest
julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",1)
RegexMatch("1")

julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",6)
RegexMatch("2")

julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",11)
RegexMatch("3")
```

```@raw html
<!--
You can extract the following info from a `RegexMatch` object:

  * the entire substring matched: `m.match`
  * the captured substrings as an array of strings: `m.captures`
  * the offset at which the whole match begins: `m.offset`
  * the offsets of the captured substrings as a vector: `m.offsets`

For when a capture doesn't match, instead of a substring, `m.captures` contains `nothing` in that
position, and `m.offsets` has a zero offset (recall that indices in Julia are 1-based, so a zero
offset into a string is invalid). Here is a pair of somewhat contrived examples:

-->
```
`RegexMatch`オブジェクトから以下のような情報を抽出することができます。

  * 一致した部分文字列全体: `m.match`
  * 捕捉した部分文字列を入れた文字列の配列: `m.captures`
  * 一致した部分全体の始まるオフセット: `m.offset`
  * 捕捉した部分文字列のオフセットからなるベクトル: `m.offsets`

捕捉が一致しない場合、`m.captures`には部分文字列ではなく`nothing`が該当する位置に入れます。
`m.offsets`のオフセットは0になります。(Juliaのインデックスは基準1なので、
文字列に対しては、０のインデックスは無効であることを思い出してください)
ちょっと不自然な例を２，３挙げます。


```jldoctest acdmatch
julia> m = match(r"(a|b)(c)?(d)", "acd")
RegexMatch("acd", 1="a", 2="c", 3="d")

julia> m.match
"acd"

julia> m.captures
3-element Array{Union{Nothing, SubString{String}},1}:
 "a"
 "c"
 "d"

julia> m.offset
1

julia> m.offsets
3-element Array{Int64,1}:
 1
 2
 3

julia> m = match(r"(a|b)(c)?(d)", "ad")
RegexMatch("ad", 1="a", 2=nothing, 3="d")

julia> m.match
"ad"

julia> m.captures
3-element Array{Union{Nothing, SubString{String}},1}:
 "a"
 nothing
 "d"

julia> m.offset
1

julia> m.offsets
3-element Array{Int64,1}:
 1
 0
 2
```

```@raw html
<!--
It is convenient to have captures returned as an array so that one can use destructuring syntax
to bind them to local variables:
-->
```

捕捉したものを配列として返すことができれば便利です。
そうすれば、分割構文によってローカル変数に束縛することができます。

```jldoctest acdmatch
julia> first, second, third = m.captures; first
"a"
```

```@raw html
<!--
Captures can also be accessed by indexing the `RegexMatch` object with the number or name of the
capture group:
-->
```

捕捉したものは、`RegexMatch`オブジェクトの捕捉したグループを数字や名前でインデックスづけしても、取り出すことができます。



```jldoctest
julia> m=match(r"(?<hour>\d+):(?<minute>\d+)","12:45")
RegexMatch("12:45", hour="12", minute="45")

julia> m[:minute]
"45"

julia> m[2]
"45"
```

```@raw html
<!--
Captures can be referenced in a substitution string when using [`replace`](@ref) by using `\n`
to refer to the nth capture group and prefixing the substitution string with `s`. Capture group
0 refers to the entire match object. Named capture groups can be referenced in the substitution
with `g<groupname>`. For example:

-->
```

[`replace`](@ref)を使った置換文字列に対する捕捉は参照可能です。
n番目の捕捉グループは`\n`で参照し置換文字列には頭に`s`をつけます。
0番目の捕捉グループは一致したオブジェクト全体を参照します。
名前をつけた捕捉グループは、`g<groupname>`という形で置換の中で参照できます。
例えば、



```jldoctest
julia> replace("first second", r"(\w+) (?<agroup>\w+)" => s"\g<agroup> \1")
"second first"
```

```@raw html
<!--
Numbered capture groups can also be referenced as `\g<n>` for disambiguation, as in:
-->
```

曖昧さを避けるために、番号のついた捕捉グループは、`\g<n>`のように参照することもできます。

```jldoctest
julia> replace("a", r"." => s"\g<0>1")
"a1"
```

```@raw html
<!--
You can modify the behavior of regular expressions by some combination of the flags `i`, `m`,
`s`, and `x` after the closing double quote mark. These flags have the same meaning as they do
in Perl, as explained in this excerpt from the [perlre manpage](http://perldoc.perl.org/perlre.html#Modifiers):
-->
```

正規表現の挙動を変えるには、`i`, `m`,`s`,`x`といったフラグの組み合わせを、閉じるほうの二重引用符の後ろにつけます。
フラグの意味はPerlと同じなので、説明を [perlre manpage](http://perldoc.perl.org/perlre.html#Modifiers)から抜粋します。



```@raw html
<!--
```
i   Do case-insensitive pattern matching.

    If locale matching rules are in effect, the case map is taken
    from the current locale for code points less than 255, and
    from Unicode rules for larger code points. However, matches
    that would cross the Unicode rules/non-Unicode rules boundary
    (ords 255/256) will not succeed.

m   Treat string as multiple lines.  That is, change "^" and "$"
    from matching the start or end of the string to matching the
    start or end of any line anywhere within the string.

s   Treat string as single line.  That is, change "." to match any
    character whatsoever, even a newline, which normally it would
    not match.

    Used together, as r""ms, they let the "." match any character
    whatsoever, while still allowing "^" and "$" to match,
    respectively, just after and just before newlines within the
    string.

x   Tells the regular expression parser to ignore most whitespace
    that is neither backslashed nor within a character class. You
    can use this to break up your regular expression into
    (slightly) more readable parts. The '#' character is also
    treated as a metacharacter introducing a comment, just as in
    ordinary code.
```
-->
```

```
i   パターンマッチで大文字小文字を区別しない

    ロケールマッチングルールが有効な場合、ケースマップは、符号位置が255より
    小さいものは現在のロケールに、大きいものはユニコードのルールに従う。
    しかし、ユニコード・非ユニコードの境界(ords 255/256)を超える場合は
    うまくいかないでしょう。

m   文字列を複数行として扱う。つまり、 "^" と "$"は、文字列の先頭と末尾の一致から
    文字列内の各行の先頭と末尾の一致に変更する。

s   文字列を単一行として扱う。つまり、"."をすべての文字と一致するように変更する。
    これには、通常は、一致の対象としない改行も含む。

    r""ms のように一緒に使うと、 "."がすべての文字と一致する一方で、
    "^"と"$"は文字列内の改行の直後と直前に一致します。

x   バックスラッシュがついていたり、文字クラスに含まれていたり
　　する場合を除く、ほとんどの空白を無視するように、正規表現パーサに、
  　伝えます。
    これを利用すると、正規表現が(少し)読みやすくなるように分割することができます。
 　　'#'の文字も、普通のコードのように、コメントの始まるメタ文字として
 　 扱われます。
```


```@raw html
<!--
For example, the following regex has all three flags turned on:
-->
```

例えば、以下の正規表現では３つのフラグすべてをオンにしています。

```jldoctest
julia> r"a+.*b+.*?d$"ism
r"a+.*b+.*?d$"ims

julia> match(r"a+.*b+.*?d$"ism, "Goodbye,\nOh, angry,\nBad world\n")
RegexMatch("angry,\nBad world")
```

```@raw html
<!--
The `r"..."` literal is constructed without interpolation and unescaping (except for
quotation mark `"` which still has to be escaped). Here is an example
showing the difference from standard string literals:
-->
```

`r"..."`リテラルでは、式展開やエスケープの処理をしていません。（エスケープ処理が必要な二重引用符`"`を除きます）
以下の例は、通常の文字リテラルとの違いを示しています。


```julia-repl
julia> x = 10
10

julia> r"$x"
r"$x"

julia> "$x"
"10"

julia> r"\x"
r"\x"

julia> "\x"
ERROR: syntax: invalid escape sequence
```

```@raw html
<!--
Triple-quoted regex strings, of the form `r"""..."""`, are also supported (and may be convenient
for regular expressions containing quotation marks or newlines).
-->
```
３連クオートの正規表現も、`r"""..."""`といった形で利用可能です。
（二重引用符や改行のある正規表現で便利かもしれません）


`[](## [Byte Array Literals](@id man-byte-array-literals))
## [バイト列リテラル](@id man-byte-array-literals)

```@raw html
<!--
Another useful non-standard string literal is the byte-array string literal: `b"..."`. This
form lets you use string notation to express read only literal byte arrays -- i.e. arrays of
[`UInt8`](@ref) values. The type of those objects is `CodeUnits{UInt8, String}`.
The rules for byte array literals are the following:
-->
```
もう一つの役立つ非標準の文字列リテラルが、`b"..."`といったバイト列文字列リテラルです。
この形式を使うと、読み取り専用のバイト列リテラル、つまり[`UInt8`](@ref)の値の配列を文字列表記で、
表現することができます。
このオブジェクトの型は、`CodeUnits{UInt8, String}`です。
バイト列リテラルの規則は以下のようになります。


```@raw html
<!--
  * ASCII characters and ASCII escapes produce a single byte.
  * `\x` and octal escape sequences produce the *byte* corresponding to the escape value.
  * Unicode escape sequences produce a sequence of bytes encoding that code point in UTF-8.
-->
```

  * ASCII文字や、エスケープしたASCII文字は、１バイトを生成する。
  * `\x` や8進エスケープした文字は、エスケープした値に対応する　**バイト**　を生成する。
  * ニコードのエスケープ列は、その符号位置をUTF-8でエンコーディングしたバイト列を生成する。


```@raw html
<!--
There is some overlap between these rules since the behavior of `\x` and octal escapes less than
0x80 (128) are covered by both of the first two rules, but here these rules agree. Together, these
rules allow one to easily use ASCII characters, arbitrary byte values, and UTF-8 sequences to
produce arrays of bytes. Here is an example using all three:
-->
```

 この規則には、重なりがあり、`x`と８進エスケープは0x80 (128)では始めの２つの規則があてはまるのですが、実は一致しています。
 この規則を使って、ASCII文字や、任意のバイト値や、UTF-8のシーケンスから、バイト列を生成できます。
 ３つすべてを使った例をあげます。


```jldoctest
julia> b"DATA\xff\u2200"
8-element Base.CodeUnits{UInt8,String}:
 0x44
 0x41
 0x54
 0x41
 0xff
 0xe2
 0x88
 0x80
```

```@raw html
<!--
The ASCII string "DATA" corresponds to the bytes 68, 65, 84, 65. `\xff` produces the single byte 255.
The Unicode escape `\u2200` is encoded in UTF-8 as the three bytes 226, 136, 128. Note that the
resulting byte array does not correspond to a valid UTF-8 string:
-->
```

ASCIIの文字列"DATA"に対応するバイトは 68, 65, 84, 65です。
 `\xff`からは、１バイト255が生成されます。
 `\u2200`はUTF-8エンコードの３バイト　226, 136, 128です。
 得られるバイト列は有効なUTF-8に対応していない点に注意してください。

```jldoctest
julia> isvalid("DATA\xff\u2200")
false
```

```@raw html
<!--
As it was mentioned `CodeUnits{UInt8,String}` type behaves like read only array of `UInt8` and
if you need a standard vector you can convert it using `Vector{UInt8}`:
-->
```

前述のように、`CodeUnits{UInt8,String}`型は`UInt8`の読み取り専用の配列として振る舞い、標準的なベクトルが必要なら
`Vector{UInt8}`を使って変換できます。



```jldoctest
julia> x = b"123"
3-element Base.CodeUnits{UInt8,String}:
 0x31
 0x32
 0x33

julia> x[1]
0x31

julia> x[1] = 0x32
ERROR: setindex! not defined for Base.CodeUnits{UInt8,String}
[...]

julia> Vector{UInt8}(x)
3-element Array{UInt8,1}:
 0x31
 0x32
 0x33
```

```@raw html
<!--
Also observe the significant distinction between `\xff` and `\uff`: the former escape sequence
encodes the *byte 255*, whereas the latter escape sequence represents the *code point 255*, which
is encoded as two bytes in UTF-8:
-->
```

また、`\xff`と`\uff`には重大な違いがあります。
前者のエスケープシーケンスは **バイト 255** にエンコードされ、後者はエスケープシーケンスは **符号位置 255**を表現し
UTF-8は２バイトにエンコードされます。



```jldoctest
julia> b"\xff"
1-element Base.CodeUnits{UInt8,String}:
 0xff

julia> b"\uff"
2-element Base.CodeUnits{UInt8,String}:
 0xc3
 0xbf
```

```@raw html
<!--
Character literals use the same behavior.

For code points less than `\u80`, it happens that the
UTF-8 encoding of each code point is just the single byte produced by the corresponding `\x` escape,
so the distinction can safely be ignored. For the escapes `\x80` through `\xff` as compared to
`\u80` through `\uff`, however, there is a major difference: the former escapes all encode single
bytes, which -- unless followed by very specific continuation bytes -- do not form valid UTF-8
data, whereas the latter escapes all represent Unicode code points with two-byte encodings.
-->
```
文字リテラルも同じような挙動をします。

符号位置が`\u80`より小さい時は、UTF-8 エンコーディングは１バイトで、対応する `\x`エスケープで生成されます。
そのため、この違いを無視しても安全です。
 しかし`\x80`から`\xff`までと、`\u80`から`\uff`までを比較すると、大きな違いがあります。
前者はすべて１バイトにエンコードされますが、ごく一部の連なりを除いて、有効なUTF-8のデータではありません。
一方、後者は、すべてユニコードの符号位置を表現し、２バイトにエンコードされます。




```@raw html
<!--
If this is all extremely confusing, try reading ["The Absolute Minimum Every
Software Developer Absolutely, Positively Must Know About Unicode and Character
Sets"](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/).
It's an excellent introduction to Unicode and UTF-8, and may help alleviate
some confusion regarding the matter.
-->
```

もし大混乱しているようなら、
 ["The Absolute Minimum Every
Software Developer Absolutely, Positively Must Know About Unicode and Character
Sets"](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)
を試しに読むといいでしょう。
UnicodeとUTF-8に関する優れた入門書なので、その混乱が軽減するかもしれません。




`[](## [Version Number Literals](@id man-version-number-literals))
## [バージョン番号リテラル](@id man-version-number-literals)

```@raw html
<!--
Version numbers can easily be expressed with non-standard string literals of the form [`v"..."`](@ref @v_str).
Version number literals create [`VersionNumber`](@ref) objects which follow the
specifications of [semantic versioning](http://semver.org),
and therefore are composed of major, minor and patch numeric values, followed by pre-release and
build alpha-numeric annotations. For example, `v"0.2.1-rc1+win64"` is broken into major version
`0`, minor version `2`, patch version `1`, pre-release `rc1` and build `win64`. When entering
a version literal, everything except the major version number is optional, therefore e.g.  `v"0.2"`
is equivalent to `v"0.2.0"` (with empty pre-release/build annotations), `v"2"` is equivalent to
`v"2.0.0"`, and so on.
-->
```
バージョン番号は [`v"..."`](@ref @v_str)という形の非標準文字列リテラルを使って、簡単に表現できます。
バージョン番号リテラルは、[セマンティックバージョニング](http://semver.org)の仕様に従う[`VersionNumber`](@ref)オブジェクトを生成します。
これは、メジャー・マイナー・パッチの数値から構成され、プレリリースやビルドのアルファベットや数値による注釈が続きます。
例えば、`v"0.2.1-rc1+win64"`を分解してみると、メジャーバージョンが`0`、マイナーバージョンが`2`、パッチバージョンが `1`、
プレリリースが `rc1`、ビルドが `win64`となります。
バージョンリテラルの入力の際には、メジャーバージョン以外は省略可能なので、例えば、 `v"0.2"`は`v"0.2.0"`（プレリリース/ビルドの注釈は空白）
と同等、 `v"2"`は`v"2.0.0"`と同等などとなります。

```@raw html
<!--
`VersionNumber` objects are mostly useful to easily and correctly compare two (or more) versions.
For example, the constant [`VERSION`](@ref) holds Julia version number as a `VersionNumber` object, and
therefore one can define some version-specific behavior using simple statements as:
-->
```

`VersionNumber`オブジェクトはたいていの場合、２つ（かそれ以上）のバージョンを簡単かつ正確に比較するのに役立ちます。
例えば、定数の[`VERSION`](@ref)にはJuliaのバージョン番号が`VersionNumber`オブジェクトとして保持され、
バージョン固有の挙動を、簡単な文で定義することができます。

```julia
if v"0.2" <= VERSION < v"0.3-"
    # do something specific to 0.2 release series
end
```

```@raw html
<!--
Note that in the above example the non-standard version number `v"0.3-"` is used, with a trailing
`-`: this notation is a Julia extension of the standard, and it's used to indicate a version which
is lower than any `0.3` release, including all of its pre-releases. So in the above example the
code would only run with stable `0.2` versions, and exclude such versions as `v"0.3.0-rc1"`. In
order to also allow for unstable (i.e. pre-release) `0.2` versions, the lower bound check should
be modified like this: `v"0.2-" <= VERSION`.
-->
```
上記の例では、標準的ではないバージョン番号`v"0.3-"`を使い、`-`で終わっている点に注意してください。
この表記はJuliaによる標準の拡張で、バージョンが`0.3`のリリースより低いことを示しており、すべてのプレリリースを含みます。
よって上記の例では、 安定版の`0.2`のみで実行可能で、`v"0.3.0-rc1"`のようなバージョンは対象外です。
不安定な（つまりプレリリースの） `0.2`のバージョンも許可するには、下限の検査を次のように変えます。
`v"0.2-" <= VERSION`。




```@raw html
<!--
Another non-standard version specification extension allows one to use a trailing `+` to express
an upper limit on build versions, e.g.  `VERSION > v"0.2-rc1+"` can be used to mean any version
above `0.2-rc1` and any of its builds: it will return `false` for version `v"0.2-rc1+win64"` and
`true` for `v"0.2-rc2"`.
-->
```

べつの非標準のバージョンの拡張では、末尾に`+` をつけて、ビルドバージョンの上限を表します。
例えば、`VERSION > v"0.2-rc1+"`は`0.2-rc1`以上の任意のバージョンを意味するために使います。
`v"0.2-rc1+win64"`に対しては`false`、`v"0.2-rc2"`に対しては`ture`を返します。


```@raw html
<!--
It is good practice to use such special versions in comparisons (particularly, the trailing `-`
should always be used on upper bounds unless there's a good reason not to), but they must not
be used as the actual version number of anything, as they are invalid in the semantic versioning
scheme.

Besides being used for the [`VERSION`](@ref) constant, `VersionNumber` objects are widely used
in the `Pkg` module, to specify packages versions and their dependencies.
-->
```
このような特殊なバージョンを使った比較は、優れて実践的です(特に末尾に`-`をつけた上限は、理由のない限り常に使うべきでしょう)。
しかしセマンティックバージョニングの方式としては無効なものなので、実際のバージョン番号にはいずれも使用できません。

[`VERSION`](@ref)定数に使う以外に、`VersionNumber`オブジェクトは`Pkg`モジュールで、
パッケージのバージョンとその依存関係を指定するために、広く利用されています。


`[](## [Raw String Literals](@id man-raw-string-literals))
## [生文字列リテラル](@id man-raw-string-literals)

```@raw html
<!--
Raw strings without interpolation or unescaping can be expressed with
non-standard string literals of the form `raw"..."`. Raw string literals create
ordinary `String` objects which contain the enclosed contents exactly as
entered with no interpolation or unescaping. This is useful for strings which
contain code or markup in other languages which use `$` or `\` as special
characters.
-->
```

式展開やエスケープの処理を行わない生文字列は、`raw"..."`という形の非標準の文字列リテラルとして表現可能です。
生文字列リテラルは、普通の`String`オブジェクトで、引用符の中身を、式展開やエスケープ処理をせず、そのまま含んでいます。
これは、`$`や `\`を特殊な文字として使う、コードやマークアップ言語を含む文字列に役立ちます。


```@raw html
<!--
The exception is that quotation marks still must be escaped, e.g. `raw"\""` is equivalent
to `"\""`.
To make it possible to express all strings, backslashes then also must be escaped, but
only when appearing right before a quote character:
-->
```

例外として、引用符はエスケープ処理が必要です。
つまり、`raw"\""`は`"\""`と同等です。
すべての文字列を表現するには、バックスラッシュにはエスケープ処理が必要ですが、引用符の直前にあるときだけです。



```jldoctest
julia> println(raw"\\ \\\"")
\\ \"
```

```@raw html
<!--
Notice that the first two backslashes appear verbatim in the output, since they do not
precede a quote character.
However, the next backslash character escapes the backslash that follows it, and the
last backslash escapes a quote, since these backslashes appear before a quote.
-->
```
始めの２つのバックスラッシュは、、引用符の直前にはないので、そのまま出力される点に注意してください。
しかし、次のバックスラッシュは直後のバックスラッシュをエスケープし、最後のバックスラッシュは引用符を
エスケープします。これらのバックスラッシュは引用符の直前に出現するからです。

