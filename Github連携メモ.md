# This is sample article!
Template of git and article

## 今回目指すこと
- VSCodeに慣れたい
- Githubを使いこなしたい
- 作成した記事をバージョン管理したい
    - ソースコードや画像の管理も同時に簡単にしたい

まずは参考にしたURLを以下においておく
* Markdown記法について
https://qiita.com/Thermidor/items/b684cfabe0f2fde0c67b
* Visual Studio Code の git 連携機能と git コマンドについて 
https://qiita.com/satokaz/items/4660ce57ca8eb456a096

## 改行コードを実際に投げる

* 参考URL
https://qiita.com/haifuri/items/5439586ae69c88d95539

このままでは、実際のMarkdownまで改行が反映されないので、改行コードについての設定を変更する必要がある。

実際に、extension.jsの位置は
/Applications/Visual Studio Code.app/Contents/Resources/app/extensions/markdown/out
にあった。

これを、以下のURLを参考に修正する。
https://qiita.com/rma/items/75f502e784b7164b8813


## VSCodeからGithubに投げるまで

最初に参考にするのはこれ
http://www.atmarkit.co.jp/ait/articles/1507/21/news017_2.html
https://git-scm.com/book/ja/v2/Git-の基本-Git-リポジトリの取得

1. Githubをインストールする

2. VSCodeからローカルレポジトリを作成する

> 統合ターミナル を Ctrl + @ で 表示します.

これによって編集中のディレクトリから、CLIのコマンドの入力が出来るようになる。


3. ターミナルから、必要なファイルを管理対象にする
https://git-scm.com/book/ja/v2/Git-の基本-Git-リポジトリの取得

ここを参照する。今回は既に作成してあるファイルをアップロードすることに注意する。また、tmpファイルを含むすべてを管理対象にする。

git add *

4.  状態を変更したときの出力を覚えておく

git statusすると、なにもない場合には

```
On branch master
nothing to commit, working tree clean
```

とでるが、どれかのファイルを編集すると、以下のようになる

```
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   SecHack365Summary_sample.md
```

5. ステージング（記録するファイル）の指定

ああらしく作成したファイルをgit addすると、そのファイルが管理対象となる。このときcommitすると、その状態が記録される。一度コミットすると、次回編集時には再度ステージング（git add）してやらないといけない。

```
  (use "git reset HEAD <file>..." to unstage)

        new file:   README.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   SecHack365Summary_sample.md
```
上記のようにnre fileは新しく作成され、ステージングされたファイル、modifiedで編集されたファイルを示している。

6. コミットを行う

変更のコミットを行う。5.でステージングエリアの準備ができたのえあれば、その変更内容をコミットすることが出来る。

先に、確認としてgit statusで変更を記録したいファイルがステージングされているかを確認する。

ただし、全てをコミットを選択すると全て一度にコミットされる。これはgit commit -aとおなじ。"ステージングエリアの省略”を参照する。

ターミナルから、もしくはVSCodeからコミットを行う。

このとき適当なコミットメッセージを書いておく。
もしターミナルから実行した場合にはvimなどのエディタが開き、そのコミット内容を記述する。

git commit -vするとdiffを表示してくれるため便利である。

ここで定期的にgit statusで状態を確認する

7. 不必要なファイルを管理対象から外す

参考URLの”ファイルの削除”を参考にする

8. オンラインgithubとgitを連携する

以下の記事を参考にする。
https://qiita.com/fuji-shin/items/06963c7a1b3219347348

基本的にいままでとやることは変わらない。githubで必要な手続きを行い、ssh鍵などを発行すれば同期される。

まずSSHの鍵などの設定を行う。（github連携にはHTTPSとSSHがあるみたい。今回はSSHで行う）

https://qiita.com/colorrabbit/items/6c3e96c394bb0f753ea0

なお、最初おかしくなったときの原因は勝手に適用にBranchを切ってしまったからで、pull, mergeがよくわからなくなってしまったため。

なお、Githubでレポジトリを作成すると、最初の画面には以下のような説明文があるので、基本的にコレに従えば普通にできる。

何か合ったときは.gitを削除してやり直せばいい（最初だけね）

```
or create a new repository on the command line

echo "# blog_article" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:himitu23/blog_article.git
git push -u origin master

or push an existing repository from the command line

git remote add origin git@github.com:himitu23/blog_article.git
git push -u origin master
```

基本的にこれで、管理はできるようになった。

気をつけないといけないことは

- ファイルの移動、名前の変更
- ファイルの削除

である。これらについてはgitで追跡の処理を行わないといけないので注意すること。

## 用語について

- ステージ

ソースコードなど、管理したい対象を指定すること

- コミット 

作業した内容を記録すること

- フォーク

メインの作業があるとして（masterブランチとか）、そこから自分用等に分岐させること。

- ブランチ

作業している流れのようなもの。もともとの意味は枝。

- クローン

githubなどにあるレポジトリから手元のローカル環境に落としてくること。

- マージ

自身で変更した内容などを他のブランチにくっつけること。

- プッシュ 

ローカルで変更した内容などをリモートレポジトリにアップロードすること。

