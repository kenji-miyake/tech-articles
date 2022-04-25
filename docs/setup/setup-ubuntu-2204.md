# Ubuntu 22.04 セットアップ

備忘録です。
細かい部分は省略しているので、[Ubuntu 20.04 版](https://qiita.com/kenji-miyake/items/06b8c3807bef0ba5c451)で適宜補完して下さい。

## OS のインストール

- <https://releases.ubuntu.com/22.04/> からイメージをダウンロードする
- Startup Disk Creator を使って USB メモリに書き込む
- USB メモリを挿して PC を起動し、手順通りにインストールする

## NVIDIA Driver のインストール

今回は、以下のコマンドでインストールしました。

```bash
sudo add-apt-repository ppa:graphics-drivers/ppa # Optional
sudo apt update
sudo ubuntu-drivers autoinstall
```

再起動後、`nvidia-smi`で動作確認します。

```bash
$ nvidia-smi
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 510.60.02    Driver Version: 510.60.02    CUDA Version: 11.6     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ...  Off  | 00000000:01:00.0  On |                  N/A |
|  0%   36C    P8    19W / 290W |    639MiB /  8192MiB |      4%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A      1768      G   /usr/lib/xorg/Xorg                326MiB |
|    0   N/A  N/A      1935    C+G   ...ome-remote-desktop-daemon      158MiB |
|    0   N/A  N/A      1983      G   /usr/bin/gnome-shell               61MiB |
|    0   N/A  N/A      2937    C+G   ...276472197050392319,131072       88MiB |
+-----------------------------------------------------------------------------+
```

## SSH keys の生成と公開鍵の登録

詳細は[About SSH - GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/about-ssh)を見ると良いです。

まずは鍵を作成します。`-C`を省略すると`user@hostname`が自動的に付与されます。
パスフレーズは、2 回目以降は`ssh-agent`で入力を自動化もできるため、面倒くさがらずに入力するのが安全です。

```bash
ssh-keygen -t ed25519
cat ~/.ssh/id_ed25519.pub
```

表示された公開鍵をコピペして登録します。タイトルを省略すると、コメント部が使用されます。

- [GitHub](https://github.com/settings/keys)
- [GitLab](https://gitlab.com/-/profile/keys)

接続テストをします。

```sh-session
$ ssh -T git@github.com
Hi kenji-miyake! You've successfully authenticated, but GitHub does not provide shell access.
```

## 日本語入力の設定

`Manage Installed Languages`をクリックすると、「不完全な言語サポートの修正」的な表示が出てくるので、それをインストールして再起動します。

![Region & Language](https://storage.googleapis.com/zenn-user-upload/6d8a158305d6-20220422.png)

`Keyboard`から`Japanese (Mozc)`を追加します。JIS キーボードが US キーボードと誤判定されてしまうため、既存の`Japanese`も残しておきます。

![Keyboard](https://storage.googleapis.com/zenn-user-upload/29d0a6e3dfae-20220422.png)

この誤判定の問題は、`~/.config/mozc/ibus_config.textproto`を以下のように編集して再起動すれば直るかなと思ったんですが、残念ながら効果がありませんでした。

```diff
-  layout : "default"
-  layout : "jp"
```

## ソフトウェアのインストール

## GUI でインストールするもの

以下のリンクからパッケージをダウンロードしてインストールします。

- [Chrome](https://www.google.com/chrome/)
- [Visual Studio Code](https://code.visualstudio.com/Download)
- [Slack](https://slack.com/intl/en-gb/downloads/linux)
- [Discord](https://discord.com/download)
- [Zoom](https://zoom.us/download?os=linux)

仕事とプライベートでプロファイルを変えたい人には、[Chrome Beta](https://www.google.com/chrome/beta/)もインストールするのがオススメです。
通常の Chrome に別ユーザーとして追加しても良いんですが、ブラウザ自体を変える方が個人的には好きです。

`apt update`で警告が出る場合は、重複した`sources.list`を削除します。

```bash
sudo rm /etc/apt/sources.list.d/google-chrome-beta.list
```

## CUI でインストールするもの

### 事前準備

セットアップに必須のツールをインストールします。

```bash
sudo apt install curl git
```

また、`~/.profile`を見ると、`$HOME/.local/bin`が存在しないと PATH に追加してくれないことが分かります。

```bash
# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/.local/bin" ] ; then
    PATH="$HOME/.local/bin:$PATH"
fi
```

ディレクトリを作成後、再起動して PATH に反映します。

```bash
mkdir -p $HOME/.local/bin
sudo reboot
```

### [VLC media player](https://www.videolan.org/vlc/download-ubuntu.html)

```bash
sudo snap install vlc
```

### [pip](https://pip.pypa.io/en/stable/installation/)

```bash
sudo apt install python3-pip
```

### [marcosnils/bin](https://github.com/marcosnils/bin)

APT で配布されていない、Rust や Go 製の CLI ツールをインストールするのに便利です。

```bash
wget https://github.com/marcosnils/bin/releases/download/v0.13.1/bin_0.13.1_Linux_x86_64
chmod +x bin_0.13.1_Linux_x86_64
./bin_0.13.1_Linux_x86_64 install github.com/marcosnils/bin
```

### [Rust](https://www.rust-lang.org/)

`cargo`でインストールすると楽なツールもあるので、インストールします。

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
echo "source $HOME/.cargo/env" >> ~/.bashrc
sudo apt install build-essential
```

### [Vim](https://github.com/vim/vim)

```bash
sudo apt install vim-gtk3
```

### [fish](https://github.com/fish-shell/fish-shell)

```bash
sudo apt install fish
```

### [jorgebucaran/fisher](https://github.com/jorgebucaran/fisher)

```bash
fish
curl -sL https://git.io/fisher | source && fisher install jorgebucaran/fisher
```

### locate

```bash
sudo apt install plocate
```

### [BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep)

```bash
sudo apt install ripgrep
```

### [sharkdp/fd](https://github.com/sharkdp/fd)

```bash
sudo apt install fd-find
ln -s $(which fdfind) ~/.local/bin/fd
```

### [junegunn/fzf](https://github.com/junegunn/fzf)

```bash
sudo apt install fzf
```

### [koalaman/shellcheck](https://github.com/koalaman/shellcheck)

```bash
sudo apt  install shellcheck
```

### [stedolan/jq](https://github.com/stedolan/jq)

```bash
sudo apt install jq
```

### [mikefarah/yq](https://github.com/mikefarah/yq)

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys CC86BB64
sudo add-apt-repository ppa:rmescandon/yq
sudo apt update
sudo apt install yq -y
```

### [GitHub CLI](https://cli.github.com/)

```bash
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install gh
```

### [kislyuk/argcomplete](https://github.com/kislyuk/argcomplete)

```bash
pip3 install argcomplete
```

### [x-motemen/ghq](https://github.com/x-motemen/ghq)

```bash
bin install https://github.com/x-motemen/ghq
```

### [hadolint](https://github.com/hadolint/hadolint)

```bash
bin install https://github.com/hadolint/hadolint
```

### [joehillen/sysz](https://github.com/joehillen/sysz)

```bash
bin install https://github.com/joehillen/sysz
```

### [chmln/sd](https://github.com/chmln/sd)

bin だと若干面倒なので、cargo でインストールしました。

```bash
cargo install sd
```

### [Alacritty](https://github.com/alacritty/alacritty)

Linux 用バイナリは無いようなので、cargo でインストールしました。

```bash
sudo apt install cmake pkg-config libfreetype6-dev libfontconfig1-dev libxcb-xfixes0-dev libxkbcommon-dev python3
cargo install alacritty
sudo update-alternatives --install /usr/bin/x-terminal-emulator x-terminal-emulator (which alacritty) 50
```

手動で`update-alternatives`を設定する場合は以下を実行します。

```bash
sudo update-alternatives --config x-terminal-emulator
```

### [Zellij](https://github.com/zellij-org/zellij)

```bash
cargo install zellij
```

### [Docker Engine](https://docs.docker.com/engine/install/ubuntu/)

面倒なので、今回はこれでインストールしました。

```bash
curl -fsSL https://get.docker.com | sh
sudo groupadd docker
sudo usermod -aG docker $USER
```

Rootless にしたい場合は[Docker Documentation](https://docs.docker.com/engine/security/rootless/)を参考にセットアップして下さい。

## カスタマイズ

### dotfiles のインストール

各自のやり方に置き換えて下さい。`.gitconfig`等、ユーザー名が入っている部分以外は、使っていただいても大丈夫です。

```bash
ghq get git@github.com:kenji-miyake/dotfiles.git
cd ~/ghq/github.com/kenji-miyake/dotfiles/
./install.fish
fisher update
```

### ホームディレクトリのフォルダ名を英語にする

今回は英語でインストールしたので必要ありませんでしたが、一応載せておきます。

```bash
LANG=C xdg-user-dirs-gtk-update
```

### CapsLock を Ctrl にする

```bash
gsettings set org.gnome.desktop.input-sources xkb-options "['ctrl:nocaps', 'caps:ctrl_modifier']"
```

### スワップファイルを拡張する

```bash
# Optional: Check swapfile
free -h

# Remove current swapfile
sudo swapoff /swapfile
sudo rm /swapfile

# Create new swapfile
sudo fallocate -l 32G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Optional: Check swapfile again
free -h
```

### 便利な GNOME Shell Extensions をインストールする

```bash
sudo apt install chrome-gnome-shell
google-chrome https://extensions.gnome.org
# Install Chrome extension from the web browser
```

以下をインストールしています。

- [Dash to Panel](https://extensions.gnome.org/extension/1160/dash-to-panel/)
- [WinTile](https://extensions.gnome.org/extension/1723/wintile-windows-10-window-tiling-for-gnome/) （まだ使えないみたいです）
