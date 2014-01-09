# Virtual Machineのセットアップ方法
## 必要なソフト
 - VirtualBox
 - Vagrnt
 - (for Windows) Putty or TeraTerm

## 作業手順
#### VagrantによるVirtual環境のセットアップ
Vagrantを利用して，課題等を行うためのVirtual環境を構築する．<br/>
Windows環境の詳細は説明はリンク先を参照：[Windows7で Vagrant & VirtualBox](http://www.jnlp.org/okada/computer/vagrant-virtualbox)

まず課題用のフォルダを作成し，ターミナル(or コマンドプロンプト)を立ち上げてそこに移動する．<br/>
移動先で以下のコマンドを実行：

```
$ vagrant add <box名> <boxのURL>
(e.g. vagrant add centos64 http://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.4-i386-v20130731.box)
$ vagrant init
$ ls
Vagrantfile (Vagrantfileが作成されていることを確認)
```

ここでboxとは，導入するVirtual環境のことである．<br/>
リンク先で様々なboxを入手することが出来る：[Vagrantbox.es](http://www.vagrantbox.es/)<br/>
今回はCentOSを利用するので，boxリストの中からCentOSを選択して導入する．<br/>
`vagrant init`で無事にVagrantfileが作成したことが確認できたら，Vagrantfileを編集する．

 - box名を`base`から導入したbox名に変更(例の場合，centos64)
 - vm.providerの設定を追加し，Virtualboxに名前をつける(任意．設定例の場合nlp_envとなっているが，nlp-localでも何でも好きにどうぞ)
 - private networkの設定に関する記述をコメントアウト

設定例：https://gist.github.com/amacbee/7784823

ここまで完了したら，ターミナル上で以下のコマンドを実行して，Virtual環境を起動する．

```
$ vagrant up
```

無事に起動したらVirtual環境のセットアップは終了．

#### Virtual環境にログイン
*nix系のOSの場合，`vagrant ssh`でVirtual環境にログインできる．<br/>
しかし，Windowsの場合はPuttyやTeraTermといったソフトを介する必要がある．<br/>
コマンドプロンプト上で`vagrant ssh-config`と打つと，Virtual環境にログインするための設定が確認できる．<br/>
ログインの例を以下に示す(for TeraTerm)．

 - TCP/IPによるログインを選択
  - ホスト：vagrant@127.0.0.1
  - TCPポート：2222
  -サービス：SSH(SSH2)
 - (※初回のみ) fingerprintが表示されるので，`続行`を選択
 - SSH認証
  - パスワード：vagrant(初期設定)
  - RSA/DSA/ECDSA鍵を使う
   - `秘密鍵`を選択
   - ファイルの種類は，`すべてのファイル`
   - `C:\Users\(ユーザ名)\.vagrant.d\insecure_private_key`を選択
 - OKを押すとログインできる

#### Chefによる初期環境の構築 (proxy環境想定)
[Chef](http://www.getchef.com/chef/)を用いて，Virtual環境上にNLP用の初期環境を構築する．<br/>
まずChef本体を導入するために，リンク先からVirtual環境に適したインストール方法でChef本体を導入する：<br/>
http://www.getchef.com/chef/install/<br/>
※CentOSの場合は，Virtual環境上で以下のコマンドを打つことでインストール出来る：

```
$ export http_proxy=http://*****:8080
$ curl -L http://www.opscode.com/chef/install.sh | sudo bash
$ which chef-solo
/usr/bin/chef-solo (こう返ってきたら成功！)
```

※以下，スーパーユーザー権限で実行する<br/>
Chefを導入できたら，`yum`コマンドで`git`を導入する．

```
$ export http_proxy=http://*****:8080
$ yum -y install git
```

`git`コマンドを用いて，Opscode社の提供するベーシックなchef-repoを導入する．

```
$ git config --global https.proxy https://*****:8080
$ git config --global http.proxy http://*****:8080
$ git config --global url."https://".insteadOf git://
$ git clone git://github.com/opscode/chef-repo.git
```

成功すると，`chef-repo`という名前のディレクトリが作成される．<br/>
ディレクトリ構成は次の通り．

```
$ ls
certificates  chefignore  config  cookbooks  data_bags  environments  LICENSE  Rakefile  README.md  roles
```

`cookbooks`ディレクトリに移動して，必要なcookbookをGithubからcloneしてくる．<br/>
本演習用の環境構築の場合，必要なリポジトリは以下の通り (`chef-***`は，頭についている`chef-`を外す)．

```
$ cd cookbooks
$ git clone git://github.com/amacbee/chef-env.git
$ mv -v chef-env env
chef-env -> env
$ git clone git://github.com/amacbee/chef-mecab.git
$ mv -v chef-mecab mecab
mecab -> mecab
$ git clone git://github.com/amacbee/chef-cabocha.git
$ mv -v chef-cabocha cabocha
cabocha -> cabocha
$ git clone git://github.com/opscode-cookbooks/yum.git
$ git clone git://github.com/opscode-cookbooks/build-essential.git
$ git clone git://github.com/opscode-cookbooks/yum-epel.git
$ git clone git://github.com/opscode-cookbooks/python.git
```

必要なファイルの導入が終わったら，実際にシステムにインストールを行う．<br/>
まず，`chef-repo`直下に`localhost.json`(名前は適当で良い)ファイルを作成し，インストールするリストを指定する．<br/>
今回の場合はリンク先の通り：<br/>
https://gist.github.com/amacbee/8296997

更に，`chef-repo`直下に`solo.rb`というファイルを作成し，以下の通り記述する．

```
file_cache_path "/tmp/chef-solo"
cookbook_path ["/home/vagrant/chef-repo/cookbooks"]
```

これで準備は終了である．<br/>
最後に以下のコマンドを実行して，必要な環境を自動構築する．

```
$ sudo chef-solo -c solo.rb -j ./localhost.json
```

以上で必要な環境構築は終了(2014.01.09現在)




