# GitHubの使い方

文責：岡田

## このファイル（ページ）について

 * 研究室の勉強会に関するGitHubの使い方を説明します．
 * 手元の環境にGitが入っていることを前提とします．
    * 研究室PCにGitを入れたい場合，お近くの個人マシン管理者までご相談下さい．
 * 諸々の初期設定は済んでいるものとします．
    * 初期設定については，[岡田の外部ページ](http://www.jnlp.org/okada/computer/github "GitHub")あたりを参照してください．
 * CUIでのお話です．GUIはシラネ．

## 使い方

### はじめに（任意）

任意の場所（例えば ~ 直下）に勉強会用のディレクトリを作成しておく．
ディレクトリ名はMao-Labでいいんじゃないかと思う．

    % cd ~           # ホームディレクトリに移動
    % mkdir Mao-Lab  # ディレクトリ"Mao-Lab"を作成
    % cd Mao-Lab     # ディレクトリ"Mao-Lab"に移動

### 手元環境にclone

GitHub上で，行う内容のリポジトリに移動し右下の方にある"HTTPS clone URL"をコピーしておく．
以下のコマンド打つ．`<clone URL>`の部分には先程コピーしたURLを貼る．

    % git clone <clone URL>  # <clone URL>の内容を手元にclone

これにより，GitHub上のリポジトリと同じ内容が手元環境にも構築されるはず．

### branchを切る

まずは

    % cd <repo-name>  # ディレクトリ<repo-name>に移動

として，作られたディレクトリに移動．（`<repo-name>`には先程cloneしたレポジトリ名が入る）
以下のコマンドを打つことで，branchが切られる．
`<branch name>`は自分の名前とかでいいと思う，

    % git checkout -b <branch name>  # branchを切って，そのbranchに移動

branchが切られたことを確認するには次のようにする．
（切られているbranchの名前一覧が表示，現在居るbranchに\*が付く）

    % git branch  # branch一覧を表示

### 変更を加える

ディレクトリ内で作業を行い（課題用のプログラムを書く等），変更を加える．

### 変更をcommitする

以下のコマンドを入力して，ローカルリポジトリに変更をcommitする．
`<commit message>`はその変更の説明を記述する（日本語可）．
「100本ノック1本目のプログラムを作成」など．

    % git add .                         # カレントディレクトリ以下の変更ファイルをステージングエリアに上げる
    % git commit -m "<commit message>"  # ステージングエリアの内容をcommit

### remoteにpushする

次のコマンドを入力してローカルリポジトリの内容をremoteにpushする．

    % git push origin <branch name>  # branch "<branch name>"をremote "origin"にpushする

以上までが一連の流れとなる．
後は，ひたすら課題をこなしてどんどんpushして行きましょう！

## Gitについてもっと詳しく

上で書かれている用語について詳しく知りたい，Gitのより詳しい使い方を知りたい，という人は例えば[こちら](http://git-scm.com/book/ja "Git - Book")を参照．
