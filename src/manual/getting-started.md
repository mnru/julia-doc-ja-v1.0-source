`[](# [Getting Started](@id man-getting-started))
# [始めよう](@id man-getting-started)

```@raw html
<!--
Julia installation is straightforward, whether using precompiled binaries or compiling from source.
Download and install Julia by following the instructions at [https://julialang.org/downloads/](https://julialang.org/downloads/).

The easiest way to learn and experiment with Julia is by starting an interactive session (also
known as a read-eval-print loop or "REPL") by double-clicking the Julia executable or running
`julia` from the command line:

-->
```


コンパイル済みのバイナリを使う場合も、ソースからコンパイルする場合も、Juliaのインストールは簡単です。
[https://julialang.org/downloads/](https://julialang.org/downloads/)
の指示に従ってダウンロードとインストールを行いましょう。

Juliaを学習・体験する手っ取り早い方法は対話セッション(read-eval-print loop や "REPL"と呼ばれます)を使ってみることです。
Juliaの実行ファイルをダブルクリックするか、コマンドラインに`julia`と入力してください。

```@eval
io = IOBuffer()
Base.banner(io)
banner = String(take!(io))
import Markdown
Markdown.parse("```\n\$ julia\n\n$(banner)\njulia> 1 + 2\n3\n\njulia> ans\n3\n```")
```

```@raw html
<!--
To exit the interactive session, type `CTRL-D` (press the Control/`^` key together with the `d` key), or type
`exit()`. When run in interactive mode, `julia` displays a banner and prompts the user for input.
Once the user has entered a complete expression, such as `1 + 2`, and hits enter, the interactive
session evaluates the expression and shows its value. If an expression is entered into an interactive
session with a trailing semicolon, its value is not shown. The variable `ans` is bound to the
value of the last evaluated expression whether it is shown or not. The `ans` variable is only
bound in interactive sessions, not when Julia code is run in other ways.

To evaluate expressions written in a source file `file.jl`, write `include("file.jl")`.

To run code in a file non-interactively, you can give it as the first argument to the `julia`
command:

-->
```

対話セッションを終了するには、`CTRL-D` (コントロールキー`^`と`d`の同時押し)、または、`exit()`を入力してください。
対話モードの時は、`julia`のバナーや入力を促すプロンプトが表示されます。
ユーザーが`1 + 2`のように式全体を入力してからエンターを打つと、対話セッションでは、式が評価され、その値が表示されます。
対話セッションでは、式に続けてセミコロンを打つと、値は表示されません。
変数`ans`には、表示非表示にかかわらず、番最後に評価された式の値が、束縛されます。
変数`ans`が束縛されるのは対話セッション中のみで、他の方法で実行される場合は、束縛されません。

ソースファイル`file.jl`中に書かれた式をを評価するには、`include("file.jl")`と書きます。

ファイル中のコードを非対話的に実行するには、そのファイルをコマンド`julia`の第1引数として指定します。

```
$ julia script.jl arg1 arg2...
```

```@raw html
<!--
As the example implies, the following command-line arguments to `julia` are interpreted as
command-line arguments to the program `script.jl`, passed in the global constant `ARGS`. The
name of the script itself is passed in as the global `PROGRAM_FILE`. Note that `ARGS` is
also set when a Julia expression is given using the `-e` option on the command line (see the
`julia` help output below) but `PROGRAM_FILE` will be empty. For example, to just print the
arguments given to a script, you could do this:

-->
```

例からわかるように、``julia``に続くコマンドライン引数は、プログラム``script.jl``に対するコマンドライン引数として解釈され、
グローバル定数``ARGS``に渡されます。
スクリプトファイル自体の名前は、グローバル変数``PROGRAM_FILE``に渡されます。
コマンドラインでJuliaに`-e`オプションによって式を指定した場合も、`ARGS`は設定されますが、
`PROGRAM_FILE`は空になります(後述の`julia`のヘルプを参照)。
例を挙げると、単にスクリプトに与えられた引数を表示するだけなら、このようにできます。


```
$ julia -e 'println(PROGRAM_FILE); for x in ARGS; println(x); end' foo bar

foo
bar
```

```@raw html
<!--
Or you could put that code into a script and run it:
-->
```

こうしたコードをスクリプトに書き込んで実行することもできます。

```
$ echo 'println(PROGRAM_FILE); for x in ARGS; println(x); end' > script.jl
$ julia script.jl foo bar
script.jl
foo
bar
```

```@raw html
<!--
The `--` delimiter can be used to separate command-line arguments intended for the script file from arguments intended for Julia:
-->
```

区切り文字の`--`を使うと、スクリプトファイルに対するコマンドライン引数と、Juliaに対する引数を分けることができます。


```
$ julia --color=yes -O -- foo.jl arg1 arg2..
```

```@raw html
<!--
Julia can be started in parallel mode with either the `-p` or the `--machine-file` options. `-p n`
will launch an additional `n` worker processes, while `--machine-file file` will launch a worker
for each line in file `file`. The machines defined in `file` must be accessible via a password-less
`ssh` login, with Julia installed at the same location as the current host. Each machine definition
takes the form `[count*][user@]host[:port] [bind_addr[:port]]`. `user` defaults to current user,
`port` to the standard ssh port. `count` is the number of workers to spawn on the node, and defaults
to 1. The optional `bind-to bind_addr[:port]` specifies the IP address and port that other workers
should use to connect to this worker.

If you have code that you want executed whenever Julia is run, you can put it in
`~/.julia/config/startup.jl`:

-->
```

Juliaを並列モードで起動するには、`-p` や `--machine-file`などのオプションを使います。
`-p n`を指定すると、 `n`個のワーカープロセスが別途起動しますが、
`--machine-₊file file`を指定すると`file`というファイルの各行に対するワーカーが起動します。
`file`で定義されている各マシンは、パスワードなしの`ssh`でログイン可能で、
現在のホストと同じ場所にJuliaがインストールされている必要があります。
各マシンの定義は`[count*][user@]host[:port] [bind_addr[:port]]`という形で行われます。
`user`のデフォルトは現在のユーザー、`port`のデフォルトはsshの標準ポートです。
`count`各ノードで生成されるワーカーの数で、デフォルトは1です。
`bind-to bind_addr[:port]`は省略可能で、他のワーカーが当該ワーカーに接続する際に使わるIPアドレスとポートを指定します。

Juliaが実行される際、常に実行したいコードがある場合、`~/.julia/config/startup.jl`に書き込んで、指定できます。





```
$ echo 'println("Greetings! 你好! 안녕하세요?")' > ~/.julia/config/startup.jl
$ julia
Greetings! 你好! 안녕하세요?

...
```

```@raw html
<!--
There are various ways to run Julia code and provide options, similar to those available for the
`perl` and `ruby` programs:
-->
```

Juliaの実行時に指定できるオプションは様々なものがあり、`perl` や `ruby` で利用可能なものに似ています。


```
julia [switches] -- [programfile] [args...]
```

```@raw html
<!--
|Switch                                 |Description|
|:---                                   |:---|
|`-v`, `--version`                      |Display version information|
|`-h`, `--help`                         |Print this message|
|`-J`, `--sysimage <file>`              |Start up with the given system image file|
|`-H`, `--home <dir>`                   |Set location of `julia` executable|
|`--startup-file={yes\|no}`             |Load `~/.julia/config/startup.jl`|
|`--handle-signals={yes\|no}`           |Enable or disable Julia's default signal handlers|
|`--sysimage-native-code={yes\|no}`     |Use native code from system image if available|
|`--compiled-modules={yes\|no}`         |Enable or disable incremental precompilation of modules|
|`-e`, `--eval <expr>`                  |Evaluate `<expr>`|
|`-E`, `--print <expr>`                 |Evaluate `<expr>` and display the result|
|`-L`, `--load <file>`                  |Load `<file>` immediately on all processors|
|`-p`, `--procs {N\|auto`}              |Integer value N launches N additional local worker processes; `auto` launches as many workers as the number of local CPU threads (logical cores)|
|`--machine-file <file>`                |Run processes on hosts listed in `<file>`|
|`-i`                                   |Interactive mode; REPL runs and `isinteractive()` is true|
|`-q`, `--quiet`                        |Quiet startup: no banner, suppress REPL warnings|
|`--banner={yes\|no\|auto}`             |Enable or disable startup banner|
|`--color={yes\|no\|auto}`              |Enable or disable color text|
|`--history-file={yes\|no}`             |Load or save history|
|`--depwarn={yes\|no\|error}`           |Enable or disable syntax and method deprecation warnings (`error` turns warnings into errors)|
|`--warn-overwrite={yes\|no}`           |Enable or disable method overwrite warnings|
|`-C`, `--cpu-target <target>`          |Limit usage of cpu features up to <target>; set to `help` to see the available options|
|`-O`, `--optimize={0,1,2,3}`           |Set the optimization level (default level is 2 if unspecified or 3 if used without a level)|
|`-g`, `-g <level>`                     |Enable / Set the level of debug info generation (default level is 1 if unspecified or 2 if used without a level)|
|`--inline={yes\|no}`                   |Control whether inlining is permitted, including overriding `@inline` declarations|
|`--check-bounds={yes\|no}`             |Emit bounds checks always or never (ignoring declarations)|
|`--math-mode={ieee,fast}`              |Disallow or enable unsafe floating point optimizations (overrides @fastmath declaration)|
|`--code-coverage={none\|user\|all}`    |Count executions of source lines|
|`--code-coverage`                      |equivalent to `--code-coverage=user`|
|`--track-allocation={none\|user\|all}` |Count bytes allocated by each source line|
|`--track-allocation`                   |equivalent to `--track-allocation=user`|
-->
```

|スイッチ                                |説明|
|:---                                   |:---|
|`-v`, `--version`                      |バージョン情報を表示|
|`-h`, `--help`                         |このメッセージを表示|
|`-J`, `--sysimage <file>`              |指定したシステムイメージファイルを使って開始|
|`-H`, `--home <dir>`                   |`julia` の実行ファイルの位置を設定|
|`--startup-file={yes\|no}`             | `~/.julia/config/startup.jl`を読み込む|
|`--handle-signals={yes\|no}`           |デフォオルトのJulia'のシグナルハンドラーを有効/無効にする|
|`--sysimage-native-code={yes\|no}`     |可能ならシステムイメージのネイティブコードを利用する|
|`--compiled-modules={yes\|no}`         |モジュールの逐次プリコンパイルを有効/無効にする|
|`-e`, `--eval <expr>`                  |`<expr>`を評価する|
|`-E`, `--print <expr>`                 |`<expr>`を評価し、結果を表示する|
|`-L`, `--load <file>`                  |すべてのプロセスで `<file>`を直ちに読み込む|
|`-p`, `--procs {N\|auto`}              |整数値Nの時 、N個のローカルワーカープロセスを別途起動する; `auto`の時、ローカルのCPUスレッド(論理コア) の数だけワーカーを起動する|
|`--machine-file <file>`                |`<file>`で列挙されたホスト上で、プロセスを実行する|
|`-i`                                   |対話モード; REPLが動作し、 関数`isinteractive()`は真を返す|
|`-q`, `--quiet`                        |沈黙起動: バナーはなく、REPLの警告も抑制される|
|`--banner={yes\|no\|auto}`             |起動時のバナーを有効/無効にする|
|`--color={yes\|no\|auto}`              |テキストの色付けの有無|
|`--history-file={yes\|no}`             |履歴の読み込み・保存|
|`--depwarn={yes\|no\|error}`           |文法・メソッドに対して非推奨の警告を有効/無効にする (`error`にすると警告がエラーに変わる)|
|`--warn-overwrite={yes\|no}`           |メソッドの上書き警告を有効/無効にする|
|`-C`, `--cpu-target <target>`          |<target>に対して利用できるCPUの特徴を制限する記法。利用可能なオプションを見る場合は`help`に設定する|
|`-O`, `--optimize={0,1,2,3}`           |最適化のレベルを設定 (何も指定しない時のデフォルトはレベル2,数字なしで指定した時のデフォルトはレベル3 )|
|`-g`, `-g <level>`                     |デバッグ情報の生成を有効にし、レベルを設定する (何も指定しない時のデフォルトはレベル1,数字なしで指定した時のデフォルトはレベル2)|
|`--inline={yes\|no}`                   |`@inline`宣言を上書きするなど、インラインを許可するかどうかを制御する|
|`--check-bounds={yes\|no}`             |境界チェックを常に行う・全くしない(宣言は無視される)|
|`--math-mode={ieee,fast}`              |安全ではない浮動小数点数の最適化を禁止・許可する(@fastmath 宣言を上書きする)|
|`--code-coverage={none\|user\|all}`    |実行するソースの行数を数える|
|`--code-coverage`                      |`--code-coverage=user`と同等|
|`--track-allocation={none\|user\|all}` |ソースの各行に割り当てられたバイト数を数える|
|`--track-allocation`                   |`--track-allocation=user`と同等|

`[](## Resources)
## 資料

```@raw html
<!--
A curated list of useful learning resources to help new users get started can be found on the [learning](https://julialang.org/learning/) page of the main Julia web site.
-->
```

 新規ユーザーが初めて見るのに役立つ精選された学習資料のリストが、
 JuliaのWebサイトの[学習](https://julialang.org/learning/)ページにあります。