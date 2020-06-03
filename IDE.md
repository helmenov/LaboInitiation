# 開発環境

## 前置き

アプリケーション開発には，次の3つの種類のソフトウェアが必要である．

1. コンパイラ：Compiler，ソースコードのアプリケーション化
2. デバッガ：Debugger，アプリケーションのテスト実行，エラー・ワーニング・バグの発見
3. エディタ：Editor，ソースコードの打ち込み

プログラミング演習などでは例えば，

1. コンパイラとして，gcc(GNU Compiler Collection)
2. デバッガとして，gdb(GNU Debugger)
3. エディタとして，emacs / Visual Studio Code

を採用している．

現在では，以上の3つに加えて，チーム開発（他人との開発共有）のため，

4. リポジトリ管理（開発プロジェクトの調整）：git

も必須になりつつあるかもしれない．

開発環境（IDE：）とは，これら4種のソフトウェアを連携させるソフトウェアであり，
有名どころでは，Visual Studio，X-Code，Eclipseは，所謂コンパイラ言語（コンパイラが実行しない言語．）のIDEである．

一方で，最近では，Matlab，Python，R，juliaなど，所謂インタプリタ言語（コンパイラが実行する言語）に特化したIDEとして，jupyter, Spyder, R-studioなどがある．

# 研究室でのプログラミング言語と推奨構成

## python言語

選択肢が2つある．pythonでは numpyやmatplotlibなどのモジュールの導入が必要だが，それを導入する方法で，分かれ道である．

1. pipでモジュール導入
2. condaでモジュール導入

pipはpythonに付属するモジュール管理ソフトであり，python公式リポジトリに登録されたモジュールを扱える．

condaはAnaconda社の作ったpython製のパッケージ管理・兼 仮想環境構築ソフトである．仮想環境ごとにパッケージを管理する．python製なのでpython自体も入り，pythonのプログラム開発もしやすいが，**python自体もcondaの中の一つの仮想環境であり**，Rや別の言語も管理できる．

研究室では，conda使いを推奨する．

### condaによるpython環境構築

1. miniconda　をインストール
   1. [ここ](https://docs.conda.io/en/latest/miniconda.html)から適宜ダウンロードして，インストール
   2. Anaconda Promptを起動する．初期環境は`base`と呼ばれる
   3. パッケージインストール元を追加する．
      1. `conda config --append channels conda-forge`
      2. Rを入れるなら，`conda config --append channels R`
2. プログラミング言語，言語のバージョンなどにより仮想環境を作成する．
   1. Anaconda Promptを起動し，名前付きで仮想環境を作成する．
   2. `conda create -n <env> <python=x.x>` 
      1. `<env>`には分かりやすい名前をつける．
      2. `python=x.x`：pythonのバージョン指定がある場合にxに適宜数字を入れて指定．
3. 仮想環境を立ち上げる．
   1. Anaconda Promptを起動し，仮想環境を立ち上げる
      1. `conda activate <env>`
   2. IDEを導入する．
      1. `conda install jupyter notebook ipykernel`

         `jupyter`はWebブラウザ上のIDEだが，これが嫌なら`spyder`かも
         - `conda install spyder`
   3. IDEを立ち上げる
      1. `jupyter notebook` とか `spyder3`　とか 
   4. モジュールの導入
      1. `conda install <module>`
      2. **pipは使わない‼**　pipで導入したモジュールはcondaの管理外になります．
4. condaのその他
   1. パッケージアップデート
      1. moduleのアップデート：`conda update <module>`
      2. すべてのmoduleのアップデート：`conda update --all`
   2. 環境のリスト
      1. `conda info`
   3. パッケージのリスト
      1. `conda list`
   4. パッケージの検索
      1. `conda search <module>`
   5. 環境のエクスポート（移管）
      1. `<env>.yml`に環境を保存：`conda env export -n <env> > <env>.yml`
      2. `<env>.yml`から環境を再構築：`conda env create -f <env>.yml`

気をつけることは，**condaを使うならpipを使わない．pipを使うならcondaは使わない**

とはいえ，どうしてもcondaには無い場合があります．そのときにpipを使います．

