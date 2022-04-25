# VSCode セットアップ

## 概要

先日、新しい PC を購入して、[Ubuntu 20.04 をセットアップ](https://qiita.com/kenji-miyake/items/06b8c3807bef0ba5c451)しました。
いつもは[Settings Sync](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)を使って終わらせる VSCode のセットアップですが、ちょうど良い機会なので、一度整理してみます。

基本的に言語非依存の設定を行いますが、一部[ROS](http://wiki.ros.org/)関連が強いのものも含みます。

### 環境

- Ubuntu 20.04
- VSCode 1.44.2

OS によって若干ショートカットキーが異なると思いますが、いい感じに読み替えて下さい。

### 対象読者

- 新人エンジニアの方
- VSCode の使い方がいまいちわからない方
- ROS 開発をされている方

## VSCode をインストールする

[公式](https://code.visualstudio.com/download)から`.deb`をダウンロードしてインストールして下さい。
`snap`でもインストールできますが、日本語入力できない問題があると見かけたので、僕は試していないです。

## 基本操作方法を確認する

馴染みのない方のために、簡単に紹介してみます。

VSCode を起動後、1 つのタブも開かれていない状態にする（Welcome ページも閉じる）と、以下のような画面が表示されると思います。
これらのコマンドは、全部とても便利なので覚えましょう。

- `Ctrl + Shift + P`: 困ったらこれ
- `Ctrl + P`: 大規模プロジェクトでファイル移動する時に、いちいちエクスプローラーのツリーから辿らなくて良いので便利です
- `Ctrl + Shift + F`: 高速にファイル内検索してくれます
- `F5`: 事前に設定が必要ですが、設定していない場合は拡張機能の設定をベースにして、`launch.json`を作ってくれます
- `Ctrl + Shift + @`: 統合ターミナルに出る Path を`Ctrl + クリック`すると VSCode で開いてくれるのとか、めっちゃ便利です

![vscode](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/45d5e0c3-dd00-8e1f-56d5-e2eed68886f6.png)

これ以外は、`Ctrl + Shift + P`で検索すれば大体何とかなります。
例えば、ファイルにフォーマットをかけたい時は、`Format`と検索すれば出てきます。
ショートカットコマンドもここに書いてあるので、何度も実行するなと感じてきたら覚えれば大丈夫です。

![find format](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/23279f06-33dd-3023-aee6-e6abcc86bd39.png)

ちなみに、フォーマットを実行したのにフォーマッタが入っていない場合は、コマンドを実行すると右下にポップアップが出てくるので、クリックしてそれっぽい拡張機能を入れると良いでしょう。

![no formatter found](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/d4ffe806-ed66-fdca-eeff-b31931cd0c50.png)

更に便利な機能を知りたい方は、`Ctrl + Shift + P`で Welcome ページを開き、`対話型プレイグランド`で学んでみると良さそうですね。
（そう言いつつ自分はやったことないので、後でやってみようと思います。）

![find welcome](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/69243862-91ed-f9ad-a08e-d1bf9d9ae433.png)

![welcome](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/e2ff1ee2-b0a5-47fd-9164-e5a926c18981.png)

最初に載っているマルチカーソルなんかは、使いこなせると便利＆気持ちいいので、ぜひ習得してみて下さい。

![playground](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/a92f96f5-d61d-e265-1466-96af5f9e9eb7.png)

[公式の更新情報](https://code.visualstudio.com/updates)にも目を通しておくと、最新機能にいち早く気付けるかと。
バージョンアップされた際、VSCode の起動後に表示されるページですね。

## 拡張機能をインストールする

`VSCode 拡張機能 おすすめ`等で検索すると、様々な拡張機能を丁寧に解説した記事がたくさん見つかるので、
ここでは僕がインストールしているものを簡単に紹介するだけにします。決して面倒なわけではないです、決して。
他にも便利な拡張機能を知りたい方は、[awesome-vscode](https://github.com/viatsko/awesome-vscode)とかで調べてみると良いと思います。

少し大変にはなりますが、一気に全てインストールするのではなく、**1 つ 1 つ挙動を確認しながらインストールしていくのがオススメ**です。

また余談ですが、1 個 1 個 Markdown 形式のリンクに直すのはだるいので、以下のようなブックマークレットを書いて使うと良いですね。
拡張機能の Web ページを開くのは、VSCode 上で拡張機能名のところをクリックすれば良いです。

- リンク取得用

```js
javascript: name = document.querySelector(".ux-item-name").textContent;
url = document.URL;
text = `[${name}](${url})`;
area = document.createElement("textarea");
area.textContent = text;
document.getElementsByTagName("body")[0].appendChild(area);
area.select();
document.execCommand("copy");
```

- 画像取得用（ブックマークレット実行後に画像をクリックして下さい）

```js
javascript: document.querySelectorAll("img").forEach((element) => {
  element.onclick = () => {
    name = document.querySelector(".ux-item-name").textContent;
    url = element.src;
    text = `![${name}](${url})`;
    area = document.createElement("textarea");
    area.textContent = text;
    document.getElementsByTagName("body")[0].appendChild(area);
    area.select();
    document.execCommand("copy");
  };
});
```

![how to open extension's web page](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/8a63e7b4-6f53-c005-0cea-868c1300b8a7.png)

### ツール系

#### [Settings Sync](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)

Gist に VSCode の設定を保存して、複数マシンで共有できるようになります。
何度もセットアップしなくて良くなるので、複数台マシンをお持ちの方は、必ず使った方が良いです。

#### [Git Graph](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph)

Source Tree くらい使いやすい（IDE 連携が強力なのでそれ以上かも？）Git クライアントです。
これがあるから VSCode 使っている、まであります。
![Git Graph](https://github.com/mhutchie/vscode-git-graph/raw/master/resources/demo.gif)

#### [GitLens — Git supercharged](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)

行ごとの変更履歴をエディタ画面に出してくれる機能が好きです。
僕は Git Graph をメインで使っているので、こちらはそこまで使い込んでいませんが、入れておくといざという時に便利です。
![GitLens — Git supercharged](https://raw.githubusercontent.com/eamodio/vscode-gitlens/master/images/docs/current-line-blame.png)

#### [Remote - Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

`VSCode拡張入りのコンテナ`で VSCode のサーバーを起動して、ホスト側の VSCode と連携できるようにする感じのものだと認識しています。
詳細は[このへん](https://code.visualstudio.com/docs/remote/remote-overview)見て下さい。[Codespaces](https://code.visualstudio.com/docs/remote/codespaces)というのも新しく出たようなので、`devcontainer.json`の設定を覚えると役に立つかもしれません。

#### [Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)

コンテナやイメージを一覧表示してくれます。
一括選択してコンテナ削除もできるので、`--rm`をつけてコンテナを起動しないタイプの人は、ゴミ掃除の時に便利かもしれません。
![Docker](https://github.com/microsoft/vscode-docker/raw/master/resources/readme/docker-view-context-menu.gif)

#### [Sort lines](https://marketplace.visualstudio.com/items?itemName=Tyriar.sort-lines)

複数行をアルファベット順にソートしてくれます。
インクルードや依存パッケージを並べ替える時に便利です。
![Sort lines](https://github.com/Tyriar/vscode-sort-lines/raw/master/images/usage-animation.gif)

#### [Todo Tree](https://marketplace.visualstudio.com/items?itemName=Gruntfuggly.todo-tree)

TODO コメントを見つけて一覧表示してくれます。
![Todo Tree](https://raw.githubusercontent.com/Gruntfuggly/todo-tree/master/resources/screenshot.png)

#### [Bookmarks](https://marketplace.visualstudio.com/items?itemName=alefragnani.Bookmarks)

コードにブックマークを付けられます。
（便利っぽいと思って入れてみたは良いんですが、全く活用できていないので、上手く使えている方はぜひ使い方を教えて下さい。）
![Bookmarks](https://github.com/alefragnani/vscode-bookmarks/raw/master/images/printscreen-select-lines.gif)

### UI 系

#### [Japanese Language Pack for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=MS-CEINTL.vscode-language-pack-ja)

日本語にしたい方はインストールしましょう。

#### [vscode-icons](https://marketplace.visualstudio.com/items?itemName=vscode-icons-team.vscode-icons)

アイコンをいい感じにしてくれます。
他にも似たような拡張機能があるので、好みで選べば良いと思います。
![vscode-icons](https://raw.githubusercontent.com/vscode-icons/vscode-icons/master/images/screenshot.gif)

#### [indent-rainbow](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)

インデントを可視化してくれます。
表示は EditorConfig の設定と連携してくれます。
![indent-rainbow](https://oderwat.gallerycdn.vsassets.io/extensions/oderwat/indent-rainbow/7.4.0/1552964364054/Microsoft.VisualStudio.Services.Icons.Default)

#### [Bracket Pair Colorizer 2](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2)

名前の通り、カッコのペアを可視化してくれます。1 と 2 がありますが、違いはよく知りません。
![Bracket Pair Colorizer 2](https://github.com/CoenraadS/Bracket-Pair-Colorizer-2/raw/master/images/forceUniqueOpeningColorEnabled.png)

#### [Rainbow CSV](https://marketplace.visualstudio.com/items?itemName=mechatroner.rainbow-csv)

CSV のカラム毎に色分けしてくれます。
![Rainbow CSV](https://i.imgur.com/PRFKVIN.png)

#### [Better Comments](https://marketplace.visualstudio.com/items?itemName=aaron-bond.better-comments)

コメントを色分けしてくれます。
![Better Comments](https://github.com/aaron-bond/better-comments/raw/master/images/better-comments.PNG)

### エディタ系

#### [EditorConfig for VS Code](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig)

`.editorconfig`を読み込んで、タブ幅やファイル末尾の改行を設定してくれます。

#### [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker)

名前の通り、スペルチェックをしてくれます。
プロジェクト固有の単語は当然自分で登録していかないと行けないので、警告を見つけては頑張って右クリックをして登録しています。
登録した単語は`settings.json`に保存されるので、`Settings Sync`でマシン間で共有できますね。
![Code Spell Checker](https://raw.githubusercontent.com/streetsidesoftware/vscode-spell-checker/master/packages/client/images/suggestions.gif)

#### [Path Intellisense](https://marketplace.visualstudio.com/items?itemName=christian-kohler.path-intellisense)

Path を補完してくれます。
![Path Intellisense](http://i.giphy.com/iaHeUiDeTUZuo.gif)

#### [Trailing Spaces](https://marketplace.visualstudio.com/items?itemName=shardulm94.trailing-spaces)

末尾空白を可視化してくれます。
![Trailing Spaces](https://shardulm94.gallerycdn.vsassets.io/extensions/shardulm94/trailing-spaces/0.3.1/1554790489790/Microsoft.VisualStudio.Services.Icons.Default)

#### [Highlight Bad Chars](https://marketplace.visualstudio.com/items?itemName=wengerk.highlight-bad-chars)

ゼロ幅スペース等の無効文字を可視化してくれます。
![Highlight Bad Chars](https://wengerk.gallerycdn.vsassets.io/extensions/wengerk/highlight-bad-chars/0.0.6/1635837048321/Microsoft.VisualStudio.Services.Icons.Default)

### 言語系

#### [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

Web 関連の様々な形式のファイルを、いい感じにフォーマットしてくれます。
以下のファイル形式に対応しているようです。

> JavaScript · TypeScript · Flow · JSX · JSON
> CSS · SCSS · Less
> HTML · Vue · Angular
> GraphQL · Markdown · YAML

#### [Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one)

Table の整形（これは`Prettier`でもやってくれる）や Table Of Contents 生成から HTML 化まで、全部やってくれます。
詳しくは拡張機能のページに色々書いてあるので見てみて下さい。

#### [markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)

Markdown 用の Linter です。ここには空行を入れなさい、とか結構厳しいです。
`Prettier`でフォーマットすると、これに合わせて修正してくれている気がします。

#### [Markdown Preview Mermaid Support](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-mermaid)

標準のプレビュー機能で、[mermaid](https://github.com/mermaid-js/mermaid)をプレビューしてくれます。

![Markdown Preview Mermaid Support](https://github.com/mjbvz/vscode-markdown-mermaid/raw/master/docs/example.png)

#### [PlantUML](https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml)

PlantUML のプレビューをしてくれます。
![PlantUML](https://github.com/qjebbs/vscode-plantuml/raw/master/images/auto_update_demo.gif)

#### [XML Tools](https://marketplace.visualstudio.com/items?itemName=DotJoshJohnson.xml)

XML のフォーマットをしてくれます。

#### [C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

インクルードファイルや関数定義へのジャンプ、clang-format による自動フォーマット、等全部やってくれます。

ちなみに、どの拡張がどういう機能を持っているかは、設定画面で検索をすると何となくわかります。
`{extension_name}.{setting_name}`という記述になっているので、以下を見ると`C_Cpp(=C/C++)`で`clang-format`できそうだなぁ、というのがわかります。

![find clang-format](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/8e7955ec-f61b-5ab1-176f-2b2fbef50231.png)

#### [cpplint](https://marketplace.visualstudio.com/items?itemName=mine.cpplint)

`cpplint`を呼んで、エラーがあればエディタ上に可視化してくれます。

ちゃんと使うには、`settings.json`に、以下のような設定が必要です。
lineLength や filters は、clang-format 等の設定と合わせて下さい。

```json
  "cpplint.cpplintPath": "{full_path_to_cpp_lint}",
  "cpplint.lineLength": 100,
  "cpplint.filters": [
    "-build/c++11",
  ]
```

#### ![cpplint](https://github.com/secularbird/cpplint-extension/raw/master/feature.png)

#### [CMake](https://marketplace.visualstudio.com/items?itemName=twxs.cmake)

CMake の補完をしてくれます。
![CMake](https://github.com/twxs/vs.language.cmake/raw/master/images/cmake1.gif)

Microsoft の[CMake Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cmake-tools)というのもありますが、ROS では不要な機能が多いのかな、と思ってこちらを使っています。

#### [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

`ms-vscode.cpptools`と同じように、大体全部やってくれます。
ちなみに僕は、フォーマッタは[Black](https://github.com/psf/black)が好きです。（何も考えなくていいので）

#### [Visual Studio IntelliCode](https://marketplace.visualstudio.com/items?itemName=VisualStudioExptTeam.vscodeintellicode)

`Python, TypeScript/JavaScript, Java`の補完で、関連度の高いものを上に持ってきてくれるようになります。
![Visual Studio IntelliCode](https://go.microsoft.com/fwlink/?linkid=2006041)

#### [ROS](https://marketplace.visualstudio.com/items?itemName=ms-iot.vscode-ros)

これを入れると、VSCode 上で ROS ノードのデバッグができるようになって無限に効率が上がるので、ROS 開発者は全員インストールした方が良いと思います。
大規模な launch だと設定が少し必要ですが、[同僚~~に教えて記事を書かせた~~が記事を書いてくれている](https://qiita.com/taka_horibe/items/50e4659e88d1ee3cd04a)ので、参考にしてみて下さい。

## 設定を変更する

僕が VSCode でデフォルト設定値を変更しているものを紹介します。
`settings.json`を直接書き換えても良いですし、UI から設定することもできます（一部 JSON のみの項目もあります）。

### ファイル保存時にフォーマットする

今どき自動フォーマットをかけない人なんて居ませんよね？

残念ながらプロジェクト内でフォーマットをかかっていないファイルがあって、フォーマットによる diff を出したくない場合は、`Ctrl + K` -> (Ctrl を離して)`S`で、フォーマットをかけずに保存することができます。

```jsonnet

"editor.formatOnSave": true
```

### 既定のフォーマッタを設定する

フォーマット機能を備えた拡張を複数インストールしている場合、競合する可能性があります。例えば、`Markdown All in One`と`Prettier`は、どちらも`.md`をフォーマットできますが、フォーマットコマンドを実行した際に使用されるのは、どちらか一方です。

既定のフォーマッタを選択するには、`Ctrl + Shift + P`で`format`と検索し、`ドキュメントをフォーマット`を実行します。
![find format](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/c89c0be9-9473-75d3-a1e2-9b597b18576d.png)

現在のファイルタイプに対応した拡張が表示されるので、既定にしたい方を選択します。
![select default formatter](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/e874bd26-aa46-0eb6-1759-f15f046bee91.png)

これをすると、`settings.json`に以下のように追加されます。

```jsonnet
"[markdown]": {
  "editor.defaultFormatter": "esbenp.prettier-vscode"
}
```

### ファイル末尾に改行を挿入する

ファイル末尾に改行がない場合に追加します。
git で余計な diff が出る原因になるので、共同開発している人は設定してほしいですね。
フォーマッタ側の設定でやるべきだとは思いますが、`clang-format`では残念ながら対応していないみたいです。

EditorConfig で設定する場合は、`insert_final_newline = true`です。

```jsonnet
"files.insertFinalNewline": true
```

### ファイル末尾の不要な改行を削除する

2 行以上の余計な改行は削除します。

EditorConfig で設定する場合は、`trim_trailing_whitespace = true`です。

```jsonnet
"files.trimFinalNewlines": true
```

### 自動 git fetch する

裏で更新してくれるので楽です。

```jsonnet
"git.autofetch": true
```

### git commit 時に常に Sign Off する

OSS 開発ではプロジェクトによっては求められるので、毎回 Sign Off するのが面倒な方は付けておくと良いでしょう。
（ここは主に同僚向けに書いています。）

```jsonnet
"git.alwaysSignOff": true
```

### git fetch 時に prune する

remote で削除されたブランチを local でも削除してくれます。

```jsonnet
"git-graph.fetchAndPrune": true
```

### ソース管理プロバイダーを常に表示する

そんなに邪魔になりませんし、今の branch が見えて便利なので、僕は表示させています。
1 つのワークスペースに複数リポジトリが含まれる場合は、このオプションを有効にしていなくても、表示されると思います。

![always show prociders](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/31dfebe7-9ec6-c00a-dac4-fe2545c973f1.png)

```jsonnet
"scm.alwaysShowProviders": true
```

こちらの設定、一度消えて、`scm.alwaysShowRepositories`になったようです。
<https://github.com/microsoft/vscode/issues/102080>

### ソース管理パネルで常にアクションを表示する

好みの問題かもしれませんんが、慣れないうちは出しておいた方がわかりやすいかもしれません。

ON
![on](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/e4ecdea3-3ed5-78ff-1646-52891426ee20.png)

OFF
![off](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/7f1d1a2b-21c1-bee7-315c-6626f8d22e9f.png)

```jsonnet
"scm.alwaysShowActions": true
```

似たものに、`alwaysShowHeaderActions`があります。こちらもお好みで。

ON
![on](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/a41267da-062b-71ab-0fd2-a53f01a9e93a.png)

OFF
![off](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/2b543667-b8ad-f8f2-0636-7ca725c7285d.png)

```jsonnet
"workbench.view.alwaysShowHeaderActions": true
```

### 統合ターミナルのスクロールバック上限を増やす

ログ出力で流れてしまわないよう、大きめに設定しておくのが良いです。
無限には設定できないようなので、適当に大きい値を入れています。

```jsonnet
"terminal.integrated.scrollback": 100000
```

## キーボードショートカットを変更する

あまりカスタマイズはせずデフォルトで使う派なんですが、どうしてもやりづらいところもあるので、若干変えています。
昔、邪魔なので無効化したもののキーバインドが今見たら変わっているような気がするので、定期的に見直したほうが良いかもしれません。

`Ctrl + K` -> `Ctrl + S`でショートカット一覧を開けます。コマンド名やキーバインドで検索ができます。
ショートカットの起動条件も指定できるため、`あるパネルにフォーカスしている時だけ実行する`ことも可能です。

![find shortcut](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/1a21e325-cd3c-d46e-f9c6-009ab92d246e.png)

`settings.json`と同じように、右上から JSON 形式で開くこともできます。
何を変えたか見たい時は、こちらの方がわかりやすいですね。
`command`に`-`があるものは既定に対して削除、なければ追加されたものです。

![open keybindings as json](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/0e9be15e-89df-d49c-0520-ca78c5f18825.png)

### 統合ターミナルで`Ctrl + F`が使えるようにする

fish shell を使っていると、`Ctrl + F`が使えないのはとってもストレスです。
他にも、`Ctrl + E`や`Ctrl + K`が競合していて、普通のターミナルのようには使えないと思います。

`Ctrl + F`で検索をかけてデフォルト設定を見ると、ターミナルフォーカス時の`Ctrl + F`は、`検索ウィジェットにフォーカスする`となっているので、適当なものに変えてしまいましょう。僕は`Ctrl + Shift + F`にしています。
ファイル内検索のショートカットとかぶっていますが、きちんと When 式を設定すれば競合しません。

### `Ctrl + (Shift) + Tab`ですぐにタブ移動する

デフォルトだと、表示は現在のタブのまま移動先を選ぶので、なんだか僕には合いません。
こんな感じに設定しています。

```jsonnet
{
  "key": "ctrl+tab",
  "command": "workbench.action.nextEditor"
},
{
  "key": "ctrl+shift+tab",
  "command": "workbench.action.previousEditor"
}
```

### 前に戻る/次へ進むを使いやすくする

これ、結構使う機能だと思うんですが、（例えば、`F12`で定義に飛んだ後、元の場所に戻る時）デフォルトは以下のようになっていて、覚えるのが大変です。

![navigate back and forward](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/3e30be48-77a1-088d-598a-7319b781e824.png)

`前に戻る`は、不要な`Alt`を無くして`Ctrl + -`と使いやすくして、
`次に進む`は`Ctrl + Shift + -`で、`Shift`の有無で反対の意味になるようにしています。

```jsonnet
{
  "key": "ctrl+[Minus]",
  "command": "workbench.action.navigateBack"
},
{
  "key": "ctrl+shift+[Minus]",
  "command": "workbench.action.navigateForward"
}
```

## まとめ

自分の VSCode のセットアップ方法を整理してみました。もし誰かのお役に立てば幸いです。
