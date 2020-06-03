# 開発環境

## 前置き

アプリケーション開発には，次の 3 つの種類のソフトウェアが必要である．

1. コンパイラ：Compiler，ソースコードのアプリケーション化
2. デバッガ：Debugger，アプリケーションのテスト実行，エラー・ワーニング・バグの発見
3. エディタ：Editor，ソースコードの打ち込み

プログラミング演習などでは例えば，

1. コンパイラとして，gcc(GNU Compiler Collection)
2. デバッガとして，gdb(GNU Debugger)
3. エディタとして，emacs / Visual Studio Code

を採用している．

現在では，以上の 3 つに加えて，チーム開発（他人との開発共有）のため，

4. リポジトリ管理（開発プロジェクトの調整）：git

も必須になりつつあるかもしれない．

開発環境（IDE：）とは，これら 4 種のソフトウェアを連携させるソフトウェアであり，
有名どころでは，Visual Studio，X-Code，Eclipse は，所謂コンパイラ言語（コンパイラが実行しない言語．）の IDE である．

一方で，最近では，Matlab，Python，R，julia など，所謂インタプリタ言語（コンパイラが実行する言語）に特化した IDE として，jupyter, Spyder, R-studio などがある．

# 研究室でのプログラミング言語と推奨構成

## python 言語

選択肢が 2 つある．python では numpy や matplotlib などのモジュールの導入が必要だが，それを導入する方法で，分かれ道である．

1. pip でモジュール導入
2. conda でモジュール導入

pip は python に付属するモジュール管理ソフトであり，python 公式リポジトリに登録されたモジュールを扱える．

conda は Anaconda 社の作った python 製のパッケージ管理・兼 仮想環境構築ソフトである．仮想環境ごとにパッケージを管理する．python 製なので python 自体も入り，python のプログラム開発もしやすいが，**python 自体も conda の中の一つの仮想環境であり**，R や別の言語も管理できる．

研究室では，conda 使いを推奨する．

### conda による python 環境構築

1. miniconda 　をインストール
   1. [ここ](https://docs.conda.io/en/latest/miniconda.html)から適宜ダウンロードして，インストール
   2. Anaconda Prompt を起動する．初期環境は`base`と呼ばれる
   3. パッケージインストール元を追加する．
      1. `conda config --append channels conda-forge`
      2. R を入れるなら，`conda config --append channels R`
2. プログラミング言語，言語のバージョンなどにより仮想環境を作成する．
   1. Anaconda Prompt を起動し，名前付きで仮想環境を作成する．
   2. `conda create -n <env> <python=x.x>`
      1. `<env>`には分かりやすい名前をつける．
      2. `python=x.x`：python のバージョン指定がある場合に x に適宜数字を入れて指定．
3. 仮想環境を立ち上げる．

   1. Anaconda Prompt を起動し，仮想環境を立ち上げる
      1. `conda activate <env>`
   2. IDE を導入する．

      1. `conda install jupyter notebook ipykernel`

         `jupyter`は Web ブラウザ上の IDE だが，これが嫌なら`spyder`かも

         - `conda install spyder`

   3. IDE を立ち上げる
      1. `jupyter notebook` とか `spyder3`　とか
   4. モジュールの導入
      1. `conda install <module>`
      2. **pip は使わない‼**　 pip で導入したモジュールは conda の管理外になります．

4. conda のその他
   1. パッケージアップデート
      1. module のアップデート：`conda update <module>`
      2. すべての module のアップデート：`conda update --all`
   2. 環境のリスト
      1. `conda info`
   3. パッケージのリスト
      1. `conda list`
   4. パッケージの検索
      1. `conda search <module>`
   5. 環境のエクスポート（移管）
      1. `<env>.yml`に環境を保存：`conda env export -n <env> > <env>.yml`
      2. `<env>.yml`から環境を再構築：`conda env create -f <env>.yml`

気をつけることは，**conda を使うなら pip を使わない．pip を使うなら conda は使わない**

とはいえ，どうしても conda には無い場合があります．そのときに pip を使います．
