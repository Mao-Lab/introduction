# Virtual Machineのセットアップ方法
## 必要なソフト
 - VirtualBox
 - Vagrnt
 - (for Windows) Putty or TeraTerm

## 作業手順
※以下の環境で動作確認を行った (2014/03/04現在)  
Mac OS X 10.9.2  
VirtualBox 4.3.8  
Vagrant 1.4.3

#### VagrantによるVirtual環境のセットアップ
Vagrantを利用して，課題等を行うためのVirtual環境を構築する．  
Windows環境の詳細は説明はリンク先を参照：[Windows7で Vagrant & VirtualBox](http://www.jnlp.org/okada/computer/vagrant-virtualbox)

※Proxy環境下にある場合は，以下のコマンドを実行してpluginをインストールする．  
以下のコマンドが実行できない場合は，手元の環境で正しくproxyが設定できているか確認する．  
Linux or Macの場合はexportで設定できる (e.g. export http_proxy="http://proxy.***:8080")

```
$ vagrant plugin install vagrant-proxyconf
```

まず課題用のフォルダを作成し，ターミナル(or コマンドプロンプト)を立ち上げてそこに移動する．  
移動先で以下のコマンドを実行：

```
$ vagrant add <box名> <boxのURL>
(e.g. vagrant add centos65 https://github.com/2creatives/vagrant-centos/releases/download/v6.5.1/centos65-x86_64-20131205.box)
$ vagrant init
$ ls
Vagrantfile (Vagrantfileが作成されていることを確認)
```

ここでboxとは，導入するVirtual環境のことである．  
リンク先で様々なboxを入手することが出来る：[Vagrantbox.es](http://www.vagrantbox.es/)  
今回はCentOS 6.5 64bitを利用するので，boxリストの中から`CentOS 6.5 x86_64`を探し，そのURLをコピーして導入する (指定したbox以外は動作確認していない)．  
`vagrant init`で無事にVagrantfileが作成したことが確認できたら，Vagrantfileを編集する．

 - box名を`base`から導入したbox名に変更(例の場合，centos65)
 - vm.providerの設定を追加し，Virtualboxに名前をつける(任意．設定例の場合nlp_envとなっているが，nlp-localでも何でも好きにどうぞ)
 - private networkの設定に関する記述をコメントアウト
 - (if proxy環境) vagrant-proxyconfを利用してproxy環境の設定を追加

設定例：https://gist.github.com/amacbee/7784823

ここまで完了したら，ターミナル上で以下のコマンドを実行して，Virtual環境を起動する．

```
$ vagrant up
```

無事に起動したらVirtual環境のセットアップは終了．

#### Virtual環境にログイン
*nix系のOSの場合，`vagrant ssh`でVirtual環境にログインできる．  
しかし，Windowsの場合はPuttyやTeraTermといったソフトを介する必要がある．  
コマンドプロンプト上で`vagrant ssh-config`と打つと，Virtual環境にログインするための設定が確認できる．  
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

#### Chefによる初期環境の構築 (proxy環境想定 ※非推奨)
[Chef](http://www.getchef.com/chef/)を用いて，Virtual環境上にNLP用の初期環境を構築する．  
まずChef本体を導入するために，リンク先からVirtual環境に適したインストール方法でChef本体を導入する：  
http://www.getchef.com/chef/install/  
※CentOSの場合は，Virtual環境にログインした後，Virtual環境上で以下のコマンドを打つことでインストール出来る：

```
$ curl -L http://www.opscode.com/chef/install.sh | sudo bash
$ which chef-solo
/usr/bin/chef-solo (こう返ってきたら成功！)
```

※以下，スーパーユーザー権限で実行する  
Chefを導入できたら，`yum`コマンドで`git`を導入する．

```
$ yum -y install git
```

proxy環境にある場合は以下のコマンドを実行し，`git`のproxy設定を行う

```
$ git config --global https.proxy http://*****:8080
$ git config --global http.proxy http://*****:8080
```

`git`コマンドを用いて，Opscode社の提供するベーシックなchef-repoを導入する．  

```
$ git config --global url."https://".insteadOf git://
$ git clone git://github.com/opscode/chef-repo.git
```

成功すると，`chef-repo`という名前のディレクトリが作成される．  
ディレクトリ構成は次の通り．

```
$ ls
certificates  chefignore  config  cookbooks  data_bags  environments  LICENSE  Rakefile  README.md  roles
```

`cookbooks`ディレクトリに移動して，必要なcookbookをGithubからcloneしてくる．  
本演習用の環境構築の場合，必要なリポジトリは以下の通り (`chef-***`は，頭についている`chef-`を外す)．

```
$ cd cookbooks
$ git clone git://github.com/amacbee/chef-env.git
$ mv -v chef-env env
chef-env -> env
$ git clone git://github.com/amacbee/chef-mecab.git
$ mv -v chef-mecab mecab
chef-mecab -> mecab
$ git clone git://github.com/amacbee/chef-cabocha.git
$ mv -v chef-cabocha cabocha
chef-cabocha -> cabocha
$ git clone git://github.com/amacbee/python.git
$ git clone git://github.com/opscode-cookbooks/yum.git
$ git clone git://github.com/opscode-cookbooks/build-essential.git
$ git clone git://github.com/opscode-cookbooks/yum-epel.git
```

必要なファイルの導入が終わったら，実際にシステムにインストールを行う．  
まず，`chef-repo`直下に`localhost.json`(名前は適当で良い)ファイルを作成し，インストールするリストを指定する．  
今回の場合はリンク先の通り：  
https://gist.github.com/amacbee/8296997

なお，ファイルの作成／編集にはviを利用する．  
viの基本的な使い方はリンク先を参照：  
http://net-newbie.com/linux/commands/vi.html

更に，`chef-repo`直下に`solo.rb`というファイルを作成し，以下の通り記述する．

```
file_cache_path "/tmp/chef-solo"
cookbook_path ["/home/vagrant/chef-repo/cookbooks"]
```

これで準備は終了である．  
最後に以下のコマンドを実行して，必要な環境を自動構築する．

```
$ chef-solo -c solo.rb -j ./localhost.json
```

以上で必要な環境構築は終了(2014.01.09現在)




