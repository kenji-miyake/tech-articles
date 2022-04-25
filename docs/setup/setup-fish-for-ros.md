# ROS ユーザー向け fish shell 設定と自作プラグインの紹介

## 概要

こちらの記事に触発されたので僕も書いてみます。

<https://zenn.dev/eduidl/articles/fc6f29f73ebb34>

fish の基本的な使い方や利点/欠点は、他の解説記事にお任せします。
このあたりが分かりやすくて良いと思いました。

<https://qiita.com/hennin/items/33758226a0de8c963ddf>
<https://qiita.com/hiraike32/items/8458a69cd98f8ac23e7a>

fish の良さを一言で言うなら、「設定をがんばらなくても、いい感じに動く」です。
設定疲れしたあなたやデフォルト教のあなた、ぜひ使ってみて下さい！
（**POSIX 非互換**に嫌悪感を持つ人が多いですが、バージョン 3 からは`&&`や`||`も使えるようになり、そんなに違和感なく使えます。強いて言うなら、 `$`や`*`の扱いが特殊なので、たまに困るくらいですかね？）

### 環境

- Ubuntu 20.04
- ROS1/ROS2

### 対象読者

- Ubuntu で ROS を使っている方
- fish と仲良くなりたい方

## .bashrc の設定

「fish なのに何で bash の設定？」と思う方もいらっしゃるかもしれませんが、

- fish をデフォルトシェルにするとハマりがち
- セットアップ用スクリプトは bash で実行した方が、コマンドを書き換えなくて良いので楽なことが多い
- ROS1 だと補完スクリプトが独自実装で、fish だと動かないものも多い（特に rosbag 周り）
  - ROS2 だと argcomplete を使うので、言語間の差異は無くなっているはず？
  - （argcomplete になったせいで fish の良さが一部消えてしまったりもするんですが…）

等の理由で bash も併用した方が良いので、そのための設定を紹介します。
併用するとは言え、設定を二重管理するのは避けたいので、基本は`.bashrc`だけに書くようにしています。

まず僕の `.bashrc` を書いてしまって、その後ブロックごとに解説していきます。

### .bashrc 全体

```bash
# If not running interactively, don't do anything
case $- in
    *i*) ;;
    *) return;;
esac

# Original .bashrc
source /etc/skel/.bashrc

# Add user's private bin to PATH
export PATH="$HOME/.local/bin:$PATH"

# ROS
if [ -d "/opt/ros" ]; then
    test "$ROS_DISTRO" = "" && export ROS_DISTRO=foxy
    if [ "$ROS_DISTRO" = "rolling" ]; then
        source /opt/ros/rolling/setup.bash
    elif [ "$ROS_DISTRO" = "foxy" ]; then
        source /opt/ros/foxy/setup.bash
    elif [ "$ROS_DISTRO" = "noetic" ]; then
        source /opt/ros/noetic/setup.bash
    else
        echo "Invalid ROS_DISTRO `$ROS_DISTRO` was given."
    fi

    source /usr/share/colcon_argcomplete/hook/colcon-argcomplete.bash

    if [ "$ROS_VERSION" = "1" ]; then
        export ROSCONSOLE_FORMAT='[${severity}] [${time}] [${node}]: ${message}'
    elif [ "$ROS_VERSION" = "2" ]; then
        export RCUTILS_CONSOLE_OUTPUT_FORMAT="[{severity} {time}] [{name}]: {message}"
    fi
fi

# Linuxbrew
test -d /home/linuxbrew/.linuxbrew && eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)

# Fish Shell
if [ -z "$FISH_VERSION" ]; then
    command -v fish > /dev/null 2>&1 && exec fish
fi
```

### .bashrc を短く書くための設定

```bash
# If not running interactively, don't do anything
case $- in
    *i*) ;;
    *) return;;
esac

# Original .bashrc
source /etc/skel/.bashrc
```

fish とは関係ないんですが、オススメなので紹介します。
`.bashrc` って、デフォルトで色々書いてあって長いですよね…下に ROS 用の設定を追記していくと思うんですが、どこからが自分が書いた設定か、たまに分からなくなりませんか？
このように書いておくと、デフォルト設定を短く書けるので見通しが良くなります。

### ~/.local/bin を PATH に追加

```bash
# Add user's private bin to PATH
export PATH="$HOME/.local/bin:$PATH"
```

argcomplete 等、`pip3 install --user` でインストールしたものはここに置かれると思うので、PATH に追加しておきましょう。
`~/.profile` にも一応書いてあるんですが、bash 起動時に毎回読み込まれるわけではなく、後述するプラグインを使う時にちょっと困るので、`.bashrc` でも設定しています。

### ROS 用の設定

```bash
# ROS
if [ -d "/opt/ros" ]; then
    test "$ROS_DISTRO" = "" && export ROS_DISTRO=foxy
    if [ "$ROS_DISTRO" = "rolling" ]; then
        source /opt/ros/rolling/setup.bash
    elif [ "$ROS_DISTRO" = "foxy" ]; then
        source /opt/ros/foxy/setup.bash
    elif [ "$ROS_DISTRO" = "noetic" ]; then
        source /opt/ros/noetic/setup.bash
    else
        echo "Invalid ROS_DISTRO `$ROS_DISTRO` was given."
    fi

    source /usr/share/colcon_argcomplete/hook/colcon-argcomplete.bash

    if [ "$ROS_VERSION" = "1" ]; then
        export ROSCONSOLE_FORMAT='[${severity}] [${time}] [${node}]: ${message}'
    elif [ "$ROS_VERSION" = "2" ]; then
        export RCUTILS_CONSOLE_OUTPUT_FORMAT="[{severity} {time}] [{name}]: {message}"
    fi
fi
```

`setup.bash`を読んで、バージョン毎の設定を少ししているだけです。
後述するプラグインのために、if 文で読み込むバージョンを変えています。
1 つのバージョンしか使わないよ、という方はシンプルに 1 つだけ読む設定を書けば良いと思います。

### Linuxbrew の設定

```bash
# Linuxbrew
test -d /home/linuxbrew/.linuxbrew && eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)
```

ghq/fd/fzf 等の、 fish プラグインの依存が apt で入らないことがあるので、そのために入れています。

brew はとても便利なんですが、注意点もあります。
brew で様々なパッケージをインストールしていると、意図しないものまで勝手に brew link してしまい、そのせいでビルドが通らなくなったりします。
例えば、Python 関連のパッケージを入れると、gcc/python/pkg-config あたりが壊れがちな印象です…

### bash から fish を起動する設定

```bash
# Fish Shell
if [ -z "$FISH_VERSION" ]; then
    command -v fish > /dev/null 2>&1 && exec fish
fi
```

ここで、以下の挙動を実現しています。

- 通常時は fish を起動する
- fish から `bash`と起動した時は bash を起動する
  - 厳密には、`FISH_VERSION="$FISH_VERSION" bash`ですが、プラグインでラップしています

bash から fish を起動することで、bash で定義した環境変数を fish でも利用することができて便利です。

## ~/.config/fish/config.fish の設定

これが fish 用の設定ファイルです。基本的にはプラグインを作って外に出しているので、あまり設定を書いていません。

僕はこのように設定しています。

```fish
alias colcon='__colcon_find_workspace_dir > /dev/null && cd (__colcon_find_workspace_dir); command colcon'
alias roscd="ccd -o"

if [ "$ROS_VERSION" = "1" ]
    source /opt/ros/noetic/share/rosbash/rosfish
else if [ "$ROS_VERSION" = "2" ]
    register-python-argcomplete --shell fish ros2 | source
end
```

ポイントは以下の通りです。

- colcon のエイリアスを設定しておくとミスが少なくなる
  - 例えば `~/workspace/src/foo` で `colcon build` した時、 `cd ~/workspace` してからビルドしてくれる
    - `colcon build` は、ビルドしたフォルダに `build/` `install/` `log/` ができてしまうため不便
    - `catkin build` のように、カレントディレクトリに関わらずワークスペースルートに対してビルドしてくれる
- 自作プラグインを使うと、ROS2 でも `roscd` っぽいことができる
  - 公式で `colcon_cd` というのもあるが、自分には合わなかったし、そもそも bash しか使えないっぽい…
- ROS1 は rosfish、ROS2 は argcomplete で ROS コマンドを Tab 補完できるようになる

## おすすめ fish プラグイン

インストール方法は最後に載せるので、ここでは機能概要だけ紹介します。

### [jorgebucaran/fisher](https://github.com/jorgebucaran/fisher)

プラグイン管理用のプラグインで、これを使っておけば間違いないです。

### [rafaelrinaldi/pure](https://github.com/rafaelrinaldi/pure)

シンプルなテーマで、ミニマリストにオススメです。

### [jethrokuan/fzf](https://github.com/jethrokuan/fzf)

`Ctrl-R`で履歴検索ができます。
`PatrickF1/fzf.fish`もありますが、僕はこちらの方が好きです。

### [edc/bass](https://github.com/edc/bass)

fish から bash スクリプトを呼べるようにしてくれるスグレモノです。ROS では必須ですね！
ソースをチラ見したところ、bash を実行して環境変数の差分を反映する、みたいな実装だった気がします。

### [decors/fish-ghq](https://github.com/decors/fish-ghq)

`Ctrl-g`で`ghq get`したリポジトリ一覧を検索して移動できます。
「あの ROS パッケージのソースどうなってるんだろう？」となった時に気軽に clone して実装を追えるので便利です。
home 直下や`~/workspace`等に clone していっても良いんですが、リポジトリが増えると管理が大変なので、ghq に任せるのがオススメです。

### [oh-my-fish/plugin-pbcopy](https://github.com/oh-my-fish/plugin-pbcopy)

`xsel --clipboard --input`をラップした`pbcopy`という関数を定義してくれます。…それだけですが、自分でエイリアスを定義するよりはプラグインを入れる方が楽です。

## 自作プラグインの紹介

ROS ユーザーにとって恐らく便利であろうプラグインをいくつか作っているので、良かったら使ってみて下さい！
リポジトリの README にも色々書いているので、詳細はそちらをご覧下さい。

### [kenji-miyake/vcd.fish](https://github.com/kenji-miyake/vcd.fish)

ROS 開発では、`.repos`ファイルを使ってワークスペースを構築することが多いと思います。
でもその時、 `cd ~/ros2_ws/src/foo/bar/baz`と毎回 cd するのは面倒ですよね。

そんな時のために、2 つコマンドをご用意しております。

- `vcd`: リポジトリに移動
- `ccd`: パッケージに移動

使い方は以下の Gif を見ていただければ分かると思います。

![vcd.gif](https://github.com/kenji-miyake/vcd.fish/raw/main/images/vcd.gif)

![ccd.gif](https://github.com/kenji-miyake/vcd.fish/raw/main/images/ccd.gif)

### [kenji-miyake/reload.fish](https://github.com/kenji-miyake/reload.fish)

環境変数を初期化して、シェルを再起動してくれます。
やっていることはシンプルなんですが、結構便利だなぁって個人的には思っています。
colcon を使っていると意図しないワークスペースのオーバーレイが発生しがちで、無駄なトラブルを防ぐために、こまめに環境変数をリセットするよう心がけています。

例えば、こういうことができます。さっきの`.bashrc`の設定との合わせ技ですね！
エディタで設定を書き換えてターミナル起動し直し…のような面倒なことはもうしなくても良いです。

```sh
$ echo $PYTHONPATH
/opt/ros/foxy/lib/python3.8/site-packages

$ reload -e ROS_DISTRO=noetic

$ echo $PYTHONPATH
/opt/ros/noetic/lib/python3/dist-packages
```

### [kenji-miyake/auto-source-setup-bash.fish](https://github.com/kenji-miyake/auto-source-setup-bash.fish)

その名の通り、ROS ワークスペースに移動した時に、自動的に`setup.bash`を source してくれます。
前回読んだものを記憶しておいて、次回以降は home に移動した時（＝ターミナルを起動した時）に読み込んでくれます。
別のワークスペースを読み込みたい時は、ワークスペースに移動して `reload` を実行すれば良いです。

たまに邪魔になる時もあるので、そういう時は以下を試してみて下さい。

- `auto_source_clear_cache`でキャッシュを消す
- `auto_source_disable`で無効化する
  - `auto_source_enable`で再度有効化する

### [kenji-miyake/colcon-clean.fish](https://github.com/kenji-miyake/colcon-clean.fish)

colcon でも`catkin clean`相当のことができるようになります。
実装は単に `rm -rf build/{package_name} install/{package_name}`をしているだけです。

### [kenji-miyake/colcon-abbr.fish](https://github.com/kenji-miyake/colcon-abbr.fish)

colcon のビルドコマンドを爆速で打てるようにしてくれます。
ユーザー側で自由にオプションを足すこともできます。
`abbr` は `alias` とは違って、後からコマンド検索しやすいのが良いですね。

```sh
# Input
$ cbrp<Space>

# Expanded Result
$ colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release --packages-up-to
```

## セットアップ用スクリプト

上で紹介した環境をセットアップするためのスクリプトです。
インストールしてみると良さが分かると思うので、まずは気軽に試してみて下さい！

```sh
# fish-shell
sudo apt-get update
sudo apt-get install fish

# dependent libraries
sudo apt-get install fzf fd-find jq
ln -s $(which fdfind) ~/.local/bin/fd

# Fisher
fish -c "curl -sL https://git.io/fisher | source && fisher install jorgebucaran/fisher"

# recommended fish plugins
fish -c "fisher install rafaelrinaldi/pure oh-my-fish/plugin-pbcopy edc/bass jethrokuan/fzf decors/fish-ghq"

# kenji-miyake's fish plugins
fish -c "fisher install kenji-miyake/reload.fish kenji-miyake/vcd.fish kenji-miyake/auto-source-setup-bash.fish kenji-miyake/colcon-clean.fish kenji-miyake/colcon-abbr.fish"

# argcomplete
pip3 install --user argcomplete
echo 'register-python-argcomplete --shell fish ros2 | source' >> ~/.config/fish/config.fish
```

ghq だけはこのままだと動かないので、別途 brew 等で入れて下さい。
Ubuntu 以外の方はいい感じに apt 周りを変更して下さい。
