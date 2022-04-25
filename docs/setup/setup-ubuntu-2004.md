# Ubuntu 20.04 セットアップ

## 概要

先日、Ubuntu 20.04 がリリースされましたね！[リリースノート](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes)によると、ついに Python のデフォルトバージョンが`3.8`になりました。
大変おめでたいので、開発用 PC に Ubuntu 20.04 をセットアップした時のメモを、備忘録として残してみます。

### 対象読者

- Ubuntu をソフトウェア開発に使う方
- Ubuntu 初〜中級者

## Ubuntu 20.04 のインストール

### 環境

Ubuntu だと相性問題も色々とあるかもしれないので、一応主要な情報を記載しておきます。
こちらの構成で、特に問題なくインストールできました。

- OS: Ubuntu 20.04 Desktop
- CPU: Ryzen 9 3900X
- GPU: RTX 2070
- MB: X570 Taichi

### ISO ファイルのダウンロード

[こちら](https://ubuntu.com/download/desktop)からダウンロードします。

様々な [Ubuntu フレーバー](https://ubuntu.com/download/flavours)も存在するので、お好きなものを入れても良いと思います。
個人的には`Ubuntu Budgie`は、便利な機能がたくさんありそうなので気になりました。

### インストール USB の作成

Windows だと[Rufus](https://rufus.ie/)、Ubuntu だと`Super -> "usb"で検索`して`Startup Disk Creator`が使えます。
どちらも Ubuntu 公式にドキュメントがあります。

- [Windows](https://ubuntu.com/tutorials/tutorial-create-a-usb-stick-on-windows#1-overview)
- [Ubuntu](https://ubuntu.com/tutorials/tutorial-create-a-usb-stick-on-ubuntu#1-overview)

### USB から起動してインストール

事前に、BIOS で起動順を設定しておくか、手動で USB から起動して下さい。
デュアルブートにする場合はパーティションの設定が必要ですが、それ以外は特にハマらないと思うので、ここの説明は省略します。

## NVIDIA Driver のインストール

ここはドライバのインストール以前に OS を起動する段階でハマる可能性が高いです。
Ubuntu 18.04 では、インストール直後に GUI がバグっているため、CUI ログインをして作業が必要だったり、色々と苦しめられました…

今回はすんなり起動してくれましたが、もしハマった人は、[古いバージョン](http://old-releases.ubuntu.com/releases/)を入れてみると良いかもしれません。
それでもダメな場合は、`Ubuntu NVIDIA ドライバ インストール`等でググると色々出てくるので、頑張って解決しましょう。

さて、ドライバのインストールですが、いくつか方法があります。

1. `ubuntu-drivers`コマンドでインストールする
2. `Software & Updates` の `Additional Drivers`からインストールする
3. NVIDIA 公式からドライバをダウンロードしてインストールする

詳細は、[こちら](https://linuxconfig.org/how-to-install-the-nvidia-drivers-on-ubuntu-18-04-bionic-beaver-linux)に詳しく書いてあるので（Ubuntu 18.04 ですが）、ここでは 1 についてのみ説明します。

### (Optional) PPA を追加する

```sh
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
```

### (Optional) 推奨ドライバを確認する

```sh
$ ubuntu-drivers devices
== /sys/devices/pci0000:00/0000:00:03.1/0000:0d:00.0 ==
modalias : pci:v000010DEd00001F07sv00001043sd00008670bc03sc00i00
vendor   : NVIDIA Corporation
model    : TU106 [GeForce RTX 2070 Rev. A]
driver   : nvidia-driver-440 - distro non-free recommended
driver   : nvidia-driver-435 - distro non-free
driver   : xserver-xorg-video-nouveau - distro free builtin
```

### ドライバをインストールする

- 自動で推奨ドライバをインストールする場合

```sh
sudo ubuntu-drivers autoinstall
```

- 手動でバージョン指定をしてインストールする場合

```sh
sudo apt install nvidia-driver-440
```

はい、これで完了です…とは残念ながらなりません。
ドライバのインストール後に再起動したら、ログイン画面でループしてしまう可能性もあるため、再起動してログインできるまでが NVIDIA ドライバインストールです。

ログインループにハマってしまった場合、違うバージョンのドライバを入れたり、
`Ubuntu NVIDIA ドライバー` ＋ `blacklist nouveau / grub nomodeset / セキュアブート`あたりのワードで検索してみると、解決策が見つかるかもしれません。

今回はこちらも、特にトラブルは発生しませんでした。平和な時代になったものです。

最終的に、`nvidia-smi`を実行して GPU の情報が表示されれば完了です。

```sh
$ nvidia-smi
Thu Apr 30 02:57:11 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 440.64       Driver Version: 440.64       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce RTX 2070    Off  | 00000000:0D:00.0  On |                  N/A |
| 26%   31C    P8     9W / 215W |   1210MiB /  7979MiB |      3%      Default |
+-------------------------------+----------------------+----------------------+
```

## SSH 公開鍵の生成と登録

```sh
ssh-keygen -t rsa -b 4096
cat ~/.ssh/id_rsa.pub
```

表示された公開鍵をコピペして登録して下さい。

- [GitHub](https://github.com/settings/keys)
- [GitLab](https://gitlab.com/profile/keys)

## 日本語入力の設定

何通りか方法があります。
[日本語 Remix](https://www.ubuntulinux.jp/download/ja-remix)をインストールした場合は、特に設定不要のはずです。

### Option1. インプットメソッドフレームワークの選択

1. `iBus`
1. `Fcitx`
1. その他

個人的には、JIS キーボードなら `iBus` で良いかと思います。
US キーボードの場合は、`Fcitx` だと Alt の空打ちで 日本語入力 ON/OFF の切り替えができるそうなので、便利かもしれません。
`Fcitx` の設定については、[こちら](https://kledgeb.blogspot.com/2013/12/ubuntu-fcitx-10-fcitx.html)が分かりやすかったです。

### Option2. 日本語入力 ON/OFF の切り替え方法

1. インプットメソッドフレームワークで切り替える
1. IME (`Mozc`) 単体で切り替える

1 は、`インプットメソッドフレームワーク側でキーボードを2種類用意しておき、Mozcは常に日本語入力ON、もう一方は常にOFFで切り替える`というイメージです。
僕は 2 を使っています。Mac 風にしたい方は、以下のような設定をしておくと便利です。

|![mozc settings](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/4ad4f0bf-cf38-c3fe-b2ff-a7bd51569f4f.png)
|:-:|

`Mozc` で JIS キーボードなのに US キーボードとして認識されてしまう場合は、`/usr/share/ibus/component/mozc.xml`を編集すれば治ります。

```diff
- <layout>default</layout>
+ <layout>jp</layout>
```

[Terminator](#terminator)の項にも書いてありますが、`ibus-setup` -> Emoji で`Ctrl-Shift-E`を`Ctrl-Shift-Alt-E`あたりに変更しておくと良いと思います。

## ソフトウェアのインストール

### GUI アプリケーション

特に解説はしませんが、リンクだけ書いておきます。

- [Chrome](https://www.google.com/intl/ja_jp/chrome/)
- [Slack](https://slack.com/intl/ja-jp/downloads/linux)
- [VSCode](https://code.visualstudio.com/download)
- [VLC](https://www.videolan.org/vlc/download-ubuntu.html)

`apt`と`snap`どちらでもインストールができるものが多いですが、`snap`版だと日本語入力ができない不具合もあるそうなので、僕は`snap`推奨のもの以外は`apt`で入れています。

仕事用と個人用でブラウザのプロファイルを分けたい方は、Chrome の他に、[Chrome Beta](https://www.google.com/intl/ja/chrome/beta/) もインストールするのがオススメです。
Ubuntu 版 の Chrome 単体では、Windows 版 のように上手くプロファイル管理をしてくれないと感じたためです。

参考までに僕の使っているものを載せてみます。必要なものだけインストールしてください。
最初に `sudo apt install git curl` くらいはしておくと、色々とスムーズにインストールできると思います。

### fish shell

機能豊富で設定も少なくて済む、ミニマリストにピッタリの Shell です。
プラグイン管理の [Fisher](https://github.com/jorgebucaran/fisher) と、便利なプラグインもいくつか一緒に入れると良いです。

```sh
# Fish Shell
sudo apt install fish

# Fisher
curl -sL git.io/fisher | source && fisher install jorgebucaran/fisher

# Plugins
fish -c "fisher install rafaelrinaldi/pure oh-my-fish/plugin-pbcopy edc/bass jethrokuan/z jethrokuan/fzf decors/fish-ghq"
sudo apt install fzf
```

`chsh` でログインシェルを変更しても良いですが、色々とハマりポイントもあるのと、`Bash` との併用のために環境変数を共通化したいので、僕は以下のように `.bashrc` で `fish` を呼び出しています。

（[ROS](http://wiki.ros.org)を使っていると、ツール周りのサポートの関係で、`基本はfishを使いつつ、必要な時はBashも使いたい`ことが結構あるんですよね。）

```sh
# ~/.bashrc

# If not running interactively, don't do anything
case $- in
    *i*) ;;
      *) return;;
esac

# Original .bashrc
source /etc/skel/.bashrc

# Fish Shell
exec fish
```

> 参考
>
> `source /etc/skel/.bashrc` の部分は、元々の `.bashrc` が長くて見通しが悪いので置き換えています。スッキリするのでオススメです。
> 先頭の `# If not running interactively, don't do anything` は消すと問題が起きるので残しましょう。

上記は簡単にするために省略して書きましたが、このままだと問題があります。

#### 問題 1. `fish` がインストールされていないとターミナルが起動しなくなる

```sh
# ~/.bashrc

command -v fish > /dev/null 2>&1 && exec fish
```

#### 問題 2. `fish` 上で `bash` と実行しても `Bash` に切り替わらない

以下のようなハックで回避できます。

```sh
# ~/.config/fish/config.fish

alias bash='env FISH_VERSION=$FISH_VERSION bash'
```

```sh
# ~/.bashrc

if [ -z "$FISH_VERSION" ]; then
  command -v fish > /dev/null 2>&1 && exec fish
fi
```

### Vim

クリップボード連携機能が必要な場合は `vim-gtk3` をインストールして、`~/.vimrc` に `set clipboard=unnamedplus` を追加します。
以前は `vim-gnome` もありましたが、廃止されたようですね。

```sh
sudo apt install vim-gtk3
```

### Terminator

分割できるターミナルです。デフォルトで使い勝手が良いので使っています。
`Ctrl-Shift-E / Ctrl-Shift-O / Ctrl-Shift-X` だけ覚えておけば何とかなります。

```sh
sudo apt install terminator
```

[Ctrl-Shift-E が絵文字にアサインされていたので](https://askubuntu.com/questions/1083913/what-does-ctrl-shift-e-do-while-typing-text)、違うものに変更した方が良さそうです。

### Linuxbrew

`apt` だと配布されていない or バージョンが古いソフトをインストールする場合に便利です。

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

```sh
# ~/.bashrc

test -d /home/linuxbrew/.linuxbrew && eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)
```

### Docker

[これも色々とインストール方法があってややこしい](https://stackoverflow.com/questions/45023363/what-is-docker-io-in-relation-to-docker-ce-and-docker-ee)のですが、今回は[こちらの方法](https://linuxconfig.org/how-to-install-docker-on-ubuntu-20-04-lts-focal-fossa)でインストールしました。

`curl -fsSL https://get.docker.com | sh`でも良いと思います。

GPU を使う場合は[ここ](https://github.com/NVIDIA/nvidia-docker/tree/master#quickstart)を見て`nvidia-container-toolkit`をインストールします。

### その他

その他インストールしたソフトの名前と一言コメントだけ書いておきます。
最初に入れておかなくても、必要になったらその場でインストールすれば良いです。

#### apt で入るもの

- `flameshot`: スクショを撮って画像を加工できます
- `peek`: GIF を撮るのに便利です
- `simplescreenrecorder`: GIF 以外はこっちを使っています
- `ffmpeg`: 撮影した動画をさくっと圧縮するのに便利です
- `silversearcher-ag`: `grep` はもう使っていられない…
- `mlocate`: `locate`が使えるようになります
- `bat`: いい感じにシンタックスハイライトして`cat`してくれます（`apt`版だとコマンドは`batcat`です）
- `fd-find`: `find` のオプションも僕には難しすぎるので…（`apt`版だとコマンドは`fdfind`です）
- `fzf`: これ単体で使うことは少ないですが、色んなツールが依存していたりします
- `tig`: TUI で Git ログを見れます
- `tree`: ディレクトリ構造を見れます
- `xsel`: 標準出力をクリップボードにコピーできます（`pbcopy` のエイリアスを作るのがオススメです）
- `htop`: これを表示させているだけで仕事している感が出るとか出ないとか
- `jq`: JSON ファイルから何かを抽出したい時（ほとんどの人は滅多になさそう）に使えます

#### brew で入るもの

- `ripgrep`: `ag` に飽きた方へ（あまり使ってないです）
- `sd`: `sed` の使い方イマイチわからん人でも使えると思います
- `ghq`: `git clone` 時にどこに置くか考えなくて良くなるので便利です
- `yq`: `jq` の YAML 版です

## カスタマイズ

### ホームディレクトリのフォルダ名を英語にする

これをしないと実質 `cd` できないので、必ず設定した方が良いです。

```sh
LANG=C xdg-user-dirs-gtk-update
```

### CapsLock を Ctrl にする

```sh
sudo apt install gnome-tweaks
gnome-tweaks
```

で `gnome-tweaks` を開き、以下の箇所で設定できます。念の為 2 つ設定しています。

- Keyboard & Mouse -> Additional Layout Options
  - Ctrl Position -> Caps Lock as Ctrl
  - Caps Lock behavior -> Caps Lock is also a Ctrl

または、以下のコマンドでも変更できます。

```sh
gsettings set org.gnome.desktop.input-sources xkb-options "['ctrl:nocaps', 'caps:ctrl_modifier']"
```

どうやら不具合でこの設定が無効化されてしまうことがあるようですが、`Alt+F2` -> `r` -> `Enter`をすれば直るようです。
<https://askubuntu.com/questions/1286997/caps-lock-remapping-stops-working>

### ターミナルで無限スクロールバックできるようにする

デフォルト値だと、ログメッセージが途切れてしまうことが多いので、設定した方が良いです。
`Terminator` だと以下のように設定できます。

|![infinite scrollback](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/e5fd6b33-e20c-103f-8ca4-e712054c2f01.png)
|:-:|

### スクリーンショットのショートカットを変更する

ご自分の用途に合わせて、好きなように設定すれば良いです。
僕は`選択範囲をクリップボードにコピーしてSlackに貼り付ける`ことが多いので、それに `PrintScreen` を割り当てています。

|![screenshot shortcut](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/89706539-2000-75d2-ac49-1066053da714.png)
|:-:|

[flameshot](https://github.com/lupoDharkael/flameshot)を使う方は、以下のように既存のショートカットを無効化し、`flameshot`のショートカットを追加すると良いでしょう。

|![disable default screenshot](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/82df0e04-416e-9a00-c617-9e3a698b0c94.png)
|:-:|

|![add flameshot shortcut](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/e4e3a63b-4517-6a28-0f19-24094d50facb.png)
|:-:|

### スワップファイルを拡張する

増やしておくと安心です。

```sh
# (Check swapfile)
free -h

# Remove current swapfile
sudo swapoff /swapfile
sudo rm /swapfile

# Create new swapfile
sudo fallocate -l 32G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# (Check swapfile again)
free -h
```

### Windows とのデュアルブートで時計がずれるのを直す

時計がずれると気になる人は設定してみて下さい。

```sh
sudo timedatectl set-local-rtc true
```

### 便利な GNOME Shell Extensions をインストールする

```sh
sudo apt install chrome-gnome-shell
```

をしてから、Firefox か Chrome で [ここ](https://extensions.gnome.org/)を開くと、便利な拡張機能がインストールできます。
`Click here to install browser extension.` と表示されていたらクリックして、ブラウザ拡張をインストールして下さい。
拡張機能のページで、以下のようにボタンを`ON`にするとインストールされます。

|![gnome shell extensions](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/3839638e-e965-ed39-1504-34b938b0be73.png)
|:-:|

設定用アプリは、`extensions` で検索すると出てきます。 `Tweaks` からでも設定できます。

|![search extensions](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/409956/06c2e44e-2d42-4ec0-a8c9-424d4838bd78.png)
|:-:|

僕は以下のものをインストールしています。

- [Dash to Panel](https://extensions.gnome.org/extension/1160/dash-to-panel/)
- [Coverflow Alt-Tab](https://extensions.gnome.org/extension/97/coverflow-alt-tab/)
- [WinTile](https://extensions.gnome.org/extension/1723/wintile-windows-10-window-tiling-for-gnome/)

[このへん](https://itsfoss.com/best-gnome-extensions/)を見て、自分に合うものを探してみて下さい。

## まとめ

自分の開発用 PC セットアップ方法を整理してみました。もし誰かのお役に立てば幸いです。
詳細までは書ききれなかったとこも多いので、気になる方はご自身で深く調べてみて下さい。

他にもオススメの設定があれば、コメント欄でぜひ教えて下さい！
