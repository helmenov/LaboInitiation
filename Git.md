# Git のすゝめ

git はそもそもバージョン管理に使われる．
バージョンをファイル名に追記したものを量産するのではなく，ファイルは１つ，ただしバージョン管理システムにそのいろんなバージョンを管理させる．git の管理物は .git フォルダに格納される．

すなわちバックアップをとるようなものだ．

どうせバックアップするなら別のストレージに置きたくなる．それをリモートリポジトリという．
バージョン管理するだけなら，リモートリポジトリは不要だ．
リモートリポジトリには，子分（クローンリポジトリ）と親分（所謂リモートリポジトリ）

# TL;DR

- ファイルの管理（バージョン管理）: init, add, rm, mv, commit, (restore, reset, revert)
  - 派生ファイルを作る場合 : branch, switch, merge, stash
- リモートリポジトリ: remote, fetch

# 0.リポジトリ(Repository)とは

編集歴を保存しているディレクトリのことである．
編集歴は`.git/`というディレクトリに保存されている．
`.git/`を削除したら普通のディレクトリである．

# 1.Git の始め方（リモートリポジトリ不要）

おもむろに，今，編集歴を記録していきたいディレクトリがあるとする．
そのディレクトリに行き，

```{bash}
% git init
```

`git status`を打つと，現在の状況が表示される．

```{bash}
% git status
Untracked files: (use "git add <file>..." to include in what will be committed)
file.txt
file2.txt
```

## 1.1. staging(tracking): 管理対象を決める．

- 管理したい変更（ファイル追加，ファイル内容変更）を記録する

```{bash}
% git add <FileName>
```

```
% git status
Changes to be committed: (use "git rm --cached <file>..." to unstage)
newfile: file.txt

Untracked files: (use "git add <file>..." to include in what will be committed)
file2.txt
```

- 管理したい変更（ファイル削除）を記録(untracking)

```
git rm <FileName>
```

- 管理したい変更（ファイル名変更）を記録

```
git mv <FileName> <NewFileName>
```

### track しているファイルを記録前に戻す

```{bash}
git reset <FileName>
```

## 1.2. commit：編集歴をコメント入りで追記する

- 編集歴追加の準備

編集歴(commit log)には，

- 編集者の名前とメアド
- コメント
- 編集内容

が記録される．したがって，まず名前とメアドを git に教えておく

```
git config user.name="名前"
git config user.email="メアド"
```

- バージョンアップロード（コミットする）

```{bash}
git commit
```

### commit した変更をなくす(という commit を作る)

track しているファイルはその commit の前の commit 後状態に戻される

```{bash}
git revert <commit>
```

# 2. バックアップ

## 2.1. バックアップを作る

こんがらがるので，現在 A というリポジトリがあるときに，B というリポジトリを新しく作るとする．

- クローンリポジトリ（バックアップ）になりたい側 B で:

```{bash}
git clone <PathToクローン元リポジトリA>
```

リモート A をクローンした場合，ローカル B のブランチの状況は，

- origin/master
- master

の２つのブランチができる．`origin/`がリモートレポジトリ A のコピーである．
何もないほうが，ローカルレポジトリ B である．クローン直後は A と B の master は同じである．

- 構成ファイルが見えなくてもいいならベア(裸)で

B 側で，

```{bash}
git clone --bare <PathToクローン元リポジトリA>
```

## 2.2. バックアップからコピーする．

clone すればいい．

- リモートリポジトリと全く同じだと親子関係が不明なので，リモート A を master(本店)，自分 B はブランチ（支店）になり，自分のほうの master を消す．

バックアップと言っているが，実際はバックアップ元とバックアップ先の区別がなくなってしまう．remote ブランチを持たないほうが原本かもしれない．

気持ち悪い場合は，リモート（クラウド）に master，PC（ローカル）には master を置かないという手がある．

```
git branch <NewBranchName>
git switch <NewBranchName>
git branch -D master
```

## 2.3. リポジトリ間の同期

リポジトリが A と B の 2 つあり，

```
A - a
  - b

B - a
  - c
```

というディレクトリ構造だとする．

バックアップの基本は，「すべてをバックアップに取り込む」である．つまり元は無かった要素を追加する．自分の何も消さない．

集合でいえば，A がバックアップ先だとすると，$A\cap B$を作る．
今の場合，A に c が加わる．b はバックアップ元 B にないが，消されない．

```
A - a*
  - b
  - c

B - a
  - c
```

a は，A にも B にも存在するので，3 つ選択肢がある．

1. B の a を全面的に採用する．(A の a は消える）
2. A の a を全面的に維持する．(B の a はバックアップされない)
3. 同期するときに手作業でいいようにする．

バックアップ先を A とするバックアップ元が B だけであれば，B の a は A の a から一方的に編集されたものなので，「1.B の a を全面的に採用する」で全く問題ない．git レポジトリは編集歴を保持しているため，2 つのレポジトリの編集歴からこの判断ができるとき，自動で B の a を全面的に採用する．

問題が起きるのはバックアップ元が B 以外に C があり，B がバックアップした後に C がバックアップしたことで，「B の a は A の a から一方的に編集されたもの」とは言えないときである．このとき conflict（衝突）となり，3 つの選択肢を手動で選ぶ必要がある．

自動であれ手動であれ，同期は内容が変わった側のレポジトリでは 1 つのコミットとなる．

- A に B を，反映させる：A を$A\cap B$にしたい

B から

```
git push
```

A 側に conflict が起きる場合がありそうだが，実は絶対に起きない．なぜなら，編集歴が一方的でない場合は，そもそも push しようとしてもできないからである．push する前に pull せよと言われる．

- A を B に，反映させる：B を$A\cap B$にしたい．

B から

```
git fetch A
git merge A/master
```

conflict しない場合は，自動で同期コミットされる．

conflict するかもしれないが，上記の 3 つの選択肢をしっかり選んで解消し，解消するために行った編集を新しく commit する．（手動で同期コミット）

# 3. ブランチ

# 3.1. レポジトリとブランチ

レポジトリが銀行だとすると，ブランチは支店である（そもそも支店の英訳は branch）．ふつうは本店があって master というブランチである．

ラーメン屋ののれん分けのアナロジーでいえば，各のれん店がブランチである．
元祖の店が master である．

銀行にしても，のれんにしても，必ず店がある．私たちが実際に触れるのはどこかの店である．すなわちレポジトリには必ずブランチがあり，私たちはブランチに触れる．前節まで言っていたレポジトリは実際は，ブランチのことであった．

ただ，店名を指定しない場合は，代表店 master を指す決まりである．

# 3.2. ブランチの使い方

つまり，ディレクトリの交流(pull,push,merge)は，

1. 同一レポジトリのブランチ間：A/a -- A/b
2. 異なるレポジトリのブランチ間：A/a -- B/c

1 は必ず同一のサーバの中であるが，2 では各レポジトリは異なるサーバにあると考えるのでレポジトリは URL 表記の別名を

```
git remote add 名前 URL
```

で登録する．

バックアップ用途で新しいブランチでなく，新しいレポジトリを用意するのは，異なるサーバにバックアップを置きたいからだとか，どこからでも見に行けるクラウドに置きたいからである．

# 3.3. ブランチの使いどころ

レポジトリを分けるのではなく，同一レポジトリ内でブランチを分ける動機は，master を書き換えるのは畏れ多いので，ブランチを作って実際はそこを普段使いするってところだと思う．ブランチなくても，いつでもある時点の master に戻ることはできるが，度重なる編集歴にその「ある時点」が埋もれてしまうのが嫌なので，開発終了したら不要となるような編集歴をまとめて別に管理したいがために，ブランチを分けるのかもしれない．ブランチはトカゲの尻尾切りのように本体からまとめて削ることが簡単なのである．基本的に普段使いのブランチは短命である．

バックアップ先と元の話でいえば，バックアップ先が master で元が develop ブランチかと．master に取り込んでもらうには相当のそれなりの承認が必要だ的な．

なので，チーム開発するときは下っ端は master を触らない．いじるのは自分だけの feature ブランチを作ってイジリ，それを直属の上司に dev ブランチに集めてもらう．dev ブランチは，もっと上の上司に master に集めてもらう．

いずれにせよ，master という絶対的な名前のブランチではない，別のブランチを普段使いする癖をつけていたほうがいいかもしれない．master は神であり唯一なのである．

# 4. commit の名指し(ポインタ)

コミットを行うとハッシュ値が割り振られる．

このハッシュ値は，そのコミットを行った直後の状態を表す．

復元する場合にこのハッシュ値が必要であるが，長いので最初の数桁だけを指定してもよい．また，ハッシュ値の別名となるようなポインタがある．

- ブランチ名：ブランチは上記の 3 で説明したが，こんがらがるかもしれないが，「ブランチ**名**」は「そのブランチの最新のコミット直後の状態」へのポインタである．
- HEAD：現在の「ブランチ**名**（ポインタ）」を指すポインタ

ポインタ演算

- p~ ：ポインタがあるコミットを指すが，そのコミットの 1 つ前のコミットを指す．
- p~~ ：さらに 1 つ前
- p~n ：n 個前のコミットを指す．

# 5. github

# 5.1. Fork

github 内での clone である．pull や merge ではないので conflict がない．

pull したい場合は，pull request から compare across fork で，
方向（「自分 <- fork 元」）を間違えないようにして，pull request を自分から自分に送って自分で merge する．
conflict があるかもしれない