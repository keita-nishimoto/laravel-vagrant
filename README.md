# laravel-vagrant

## 概要

laravelを利用したローカル開発環境を作成する為のVagrantfileです。

現在のところ以下の環境に対応しています。

- [laravel-front-sample（フロントエンド）](https://github.com/keita-nishimoto/laravel-front-sample)
- [laravel-api-sample（バックエンド）](https://github.com/keita-nishimoto/laravel-api-sample)

## 利用の為の事前準備

以下のツールのインストールが必要です。

- Vagrant 1.8.4
- VirtualBox 5.0.26
- Ansible 2.1.0.0

現状MacOSのみ動作します。

Ansible、VirtualBox共にこれ以下のバージョンでは動作保証はしておりません。
※Ansibleは2系であれば動くと思いますが出来る限り最新版の利用をオススメします。

#### 1. Homebrewをインストールします。（インストール済の場合この手順は省略可です。）

[公式サイト](http://brew.sh/index_ja.html)にある通りに下記を実行するだけです。

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

#### 2. VirtualBoxのインストール

[公式サイト](https://www.virtualbox.org/wiki/Downloads)より「OS X hosts」をダウンロードして下さい。

2016-08-29 現在、最新版のVirtualBox5.1系だとVagrantが上手く動作しない不具合があるようです。

- http://qiita.com/unchemist/items/3a971b5539adffdd338e
- https://github.com/mitchellh/vagrant/issues/7411

下記のリンクより動作確認済のバージョン5.0.26をダウンロードして下さい。
https://www.virtualbox.org/wiki/Download_Old_Builds_5_0

#### 3. Vagrantのインストール

[公式サイト](https://www.vagrantup.com/downloads.html) よりダウンロード＆インストールを行って下さい。


#### 4. Vagrant pluginのインストール

Vagrantプラグインのインストールを行います。
今回必要なのは、vagrant-vbguestです。
以下のコマンドを実行しインストールを行います。

```
vagrant plugin install vagrant-vbguest
```

下記のコマンドを実行して、"vagrant-vbguest"が表示されていればOKです。

```
$ vagrant plugin list
sahara (0.0.17)
vagrant-global-status (0.1.4)
vagrant-share (1.1.5, system)
vagrant-vbguest (0.12.0)
vagrant-vbox-snapshot (0.0.10)
```

#### 5. Ansibleのインストール

Homebrewを最新状態にします。

```
brew update
```

brewコマンドでansibleを指定してインストールします。

```
brew install ansible
```

"ansible --version"を実行し以下のように表示されればインストールが正常終了しています。

```
$ ansible --version
ansible 2.1.0.0
  config file =
  configured module search path = Default w/o overrides
```

#### 6. 関連するgit repositoryをcloneしておく

```
$ git clone https://github.com/keita-nishimoto/laravel-ansible.git
$ git clone https://github.com/keita-nishimoto/laravel-front-sample.git
$ git clone https://github.com/keita-nishimoto/laravel-api-sample.git
```

※注意点として全てのgit repositoryは下記のように同階層に配置して下さい。

```
├── laravel-vagrant
├── laravel-ansible
├── laravel-front-sample
├── laravel-api-sample
```

ここまでの手順が事前準備となります。

## 利用方法

git cloneしたnsc-vagrantの直下に移動して以下のコマンドを実行して下さい。

```
$ vagrant up
```

これを実行すると、[laravel-ansible](https://github.com/keita-nishimoto/laravel-ansible) 内のplaybookが実行され必要なミドルウェアがインストールされます。

※初回はBoxイメージのダウンロードがあるので時間がかかります。
本環境の元になっているのは、CentOS公式の[CentOS7.0](https://atlas.hashicorp.com/centos/boxes/7) のBoxです。

環境構築完了後、以下のコマンドを打つと複数のVirtualBoxが立ち上がっていることが確認出来ます。

```
$ vagrant status
Current machine states:

frontend                  running (virtualbox)
backend                   running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

## SSHでの接続方法

基本的に"vagrant ssh"を利用しますが、複数のVirtualBoxを起ち上げている関係上、第二引数にHost名の指定が必要です。

- フロントエンドのVMにSSHする場合

```
$ vagrant ssh frontend
```

- バックエンドのVMにSSHする場合

```
$ vagrant ssh backend
```

## HTTPSでのアクセス方法

ホストOSのhostsに以下を追加して名前解決出来る状態にします。

```
192.168.34.20 dev.laravel-front.net
192.168.34.21 dev.laravel-api.net
```

- フロントエンド
    - https://dev.laravel-front.net

- バックエンド
    - https://dev.laravel-api.net

## 停止・起動方法

[ドキュメント](http://raqda.com/vagrant/cli/index.html) を参照して下さい。

なお、環境構築のplaybookが実行されるのは初回のみです。
二回目以降に環境の状態が更新されそれを適応するには"vagrant provision"を明示的に指定する必要があります。

基本的に一度構築して開発を一旦中断する場合はsuspendで停止させて再開時にresumeで再開させるのが楽だと思います。

```
# 一時停止
$ vagrant suspend

# 再開
$ vagrant resume
```
