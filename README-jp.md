# docker-laravel-standardとは

Dockerを使ったLaravelウェブアプリのサンプル実装で、Windows, Mac, Linuxユーザーが同じ環境で開発できるようにすることを目的としています。

## プロジェクトの構成

プロジェクト全体がDockerを使って構築されています。
本プロジェクトはフルスタック型ウェブシステムです。
以下のDockerコンテナから構成されます。

## Dockerの構成

`docker-compose.yml` を参照。

- app
  - ウェブサービス本体
  - php-8.1
  - Laravel 9
  - composer内蔵。composer, artisanを実行するときはコンテナ内で実行すること。
- db
  - RDBサーバー(mysql, MariaDB)
- nginx
  - ウェブサーバー(nginx)

## Windows初心者向けの環境構築

Windowsの場合、他のOSと比べてフォルダ構成の自由度が高いため、作業PCの開発環境を構築する際にどうしたらいいかわからないという意見がありましたので、標準的なディレクトリ構成を書いておきます。

推奨はしますがこの通りにしなければ開発ができないというわけではありません。各自のスキルやポリシーに合う形で開発を進めてください。

### フォルダ構成(Windows)

- `C:¥Home` フォルダをExplorerで作る
- `Home`フォルダのアイコンを緑の木に変更する
  - フォルダの[プロパティ]->[カスタマイズ]->[フォルダーアイコン]
- `C:¥Home¥src` と `C:¥Home¥local` フォルダを作る
  - `C:¥Home¥src` 以下に `laravel-docker-standard`などの開発対象のプロジェクトをclone/checkoutする
  - `C:¥Home¥local` 以下に MSys2、Git、cmake、WinMerge等の開発系ツールをインストールする

### 環境変数とは

OSがアプリケーション等のプロセスを起動したときにOSから渡される文字列設定の束です。

例えばコンソールからコマンドの名前を書いて実行しようとした場合、コンソールのアプリケーションの`PATH`環境変数に書かれたディレクトリの順番にコマンドのプログラムが探され、最初に見つかったプログラムが実行されます。

その他、 docker-compose.ymlの各Dockerコンテナには`environment`設定で環境変数が設定されていて、各コンテナが起動したときにその環境変数が使われます。例えば`db`コンテナでは環境変数を使ってMySQLの接続情報が定義されます。


### 環境変数の設定(Windows)

Windowsの場合、環境変数の設定はアプリを使います。
Rapid Environment Editorなどが便利でしょう。
https://www.rapidee.com/ja/about

- インストール先はデフォルトでOK
- 左側がシステム全体、右側が現在のユーザー(左側が優先)
- 左側の編集を有効にするにはアプリ自体を特権レベルで実行するか、Run as Administoratorボタンを押す
- システム側(左側)のPATHを操作することでプログラム実行時の検索順を変更できます
  - [+] ボタンを押すことで箇条書きでリストアップされます(上が優先度高)
  - ツールバーの追加ボタン（２つあるが右側）を押すことで、選択したpathの次の行に新しいpathを挿入できます。
  - pathのコピーはExplorerから行うのが簡単です
    - パス表示欄の右側の白い部分をクリックすることでtextboxに変化し、pathのコピーが可能になる

### 初期設定までのセットアップ

- Dockerのインストール
  - https://www.docker.com/products/docker-desktop/
- Visual Studio Codeのインストール(本プロジェクトの標準エディタ)
- Windows
  - Windows Terminalのインストール(Microsoft Storeアプリから)
  - MSYS2のインストール
  - https://www.msys2.org/
  - Git for Windowsのインストール(MSYS2のgitでもよい)
  - https://gitforwindows.org/
  - "Checkout as-is, comment Unix-style line endings"を選択
  - https://qiita.com/uggds/items/00a1974ec4f115616580
  - docker-composeのインストール
- Mac
  - brewのインストール
  - https://brew.sh/index_ja
  - gitのインストール
  - docker-composeのインストール

## docker-composeの注意点

Docker Desktopにバンドルされるdocker-composeがv1系である場合、
brew等を使って2系をインストールして差し替える必要があります。

### docker-compose の更新(Mac)

以下、brewの場合。(おそらく初期状態ではdocker-compose v1系が実行される状態)

```bash
$ brew install docker-compose
$ brew unlink docker-compose
$ brew link --force --overwrite docker-compose
```

### Windowsの注意点(git)

```
$ git config -l
```
を実行し、 `core.autocrlf=true`になっている場合、正常に運用できないため
以下を実行する。

```bash
$ git config --global core.autocrlf input
```
に変更しておくこと。

git configは system/global/local の3段階の設定となっている。

- local
  - そのリポジトリ内のみで有効(最優先)
- global
  - 現在のログインユーザー全てのリポジトリで有効(localの次)
- system
  - そのホスト全てのユーザー共通の設定(優先度低)
  - Windowsの場合、特権レベルでgit configを実行した場合に変更できる


local含めて設定値が上書きされていないか確認すること。

この作業はリポジトリをクローンする前に実施する必要があります。

### 初期設定

- 本プロジェクトのリポジトリをクローンする
- .env のセットアップ
- Dockerコンテナのセットアップ
- composerのセットアップ

```bash
$ git clone https://github.com/kanryu/laravel-docker-standard.git
$ cd laravel-docker-standard
$ cd laravel-app
$ cp .env.local .env
$ cd ..
$ docker-compose up -d --build
$ docker-compose exec app composer install
$ docker-compose exec app npm install
$ docker-compose exec app npm run build
```

ウェブブラウザで http://localhost:8086 に接続し、開発中のappサービスが表示されれば初期設定完了。

### Dockerコンテナによるネットワーク構成

Dockerコンテナを使ってウェブ開発を行う場合、開発PCから接続する場合に localhostに対してアクセスします。例えば `http://localhost:8086` とアクセスすれば `nginx`コンテナに繋がりますし、 127.0.0.1:33306 にアクセスすれば`db`コンテナに繋がります。

ところが app, db, nginx の各Dockerコンテナは開発PCで直接起動されているわけではなく、別のネットワークに存在します。

そして、開発PC自体がそのネットワークへのルーターとして動作していて、例えば nginx:80 が localhost:8086 としてマウントされています。これをPort forwardingといい、NAT(Network Address Translation)の一種です。

Dockerコンテナはデフォルトではnoneのネットワークにあり、開発PCからはPort fowardingされたポート以外では接続できません。Dockerコンテナを通常のホストと同じように自由にネットワーク通信を行いたい場合は `docker-compose.yml` 上で `network` の項目を追加し、そのnetworkとDockerコンテナを関連付けます。

なお、app, db, nginxは同じネットワーク内にあるため、互いに自由に通信できます。例えば appコンテナからdbコンテナのMySQLサーバーには db:3306 で接続できます。

### MySQLへの接続方法

DBクライアントはWindows、Macともに多数存在するため、各自好みのクライアントを使用して構いません。
何を使っていいかわからない場合は DBeaver を使いましょう。 https://dbeaver.io/

`docker-compose.yml`の設定により、 MySQLのDockerコンテナは `db` という名前のホストとなっており、`bcp_creator_db`というコンテナ名になっています。

DB接続情報は`docker-compose.yml`に環境変数として設定されています。

作業PCからdb内のMySQLサーバーに接続する場合、以下のように設定します。

- ホスト
  - 127.0.0.1
- port
  - 33306
- database
  - laravel_app
- user
  - db-user
- password
  - db-pass (接続のたびに入力する設定でもよい)

### composer

composer.json は `/laravel-app` 以下で構築されており、Laravel 9アプリでもある。
`app` コンテナ内で実行すること。

例： 新しいパッケージをインストールする

実施方法1: ホスト側から間接的にdocker内のcomposerを実行する

```bash
$ docker-compose exec app composer require laravelcollective/html
```

実施方法2: appコンテナ内でcomposerを実行する

```bash
$ docker-compose exec app bash
# composer require laravelcollective/html
```

### Node.js(npm)

CSS, JavaScriptはNode.js(npm)でビルドすることになる。
package.json は `/laravel-app` 以下で構築されている。
`app` コンテナ内で実行すること。

例： 新しいパッケージをインストールする

実施方法1: ホスト側から間接的にdocker内のnpmを実行する

```bash
$ docker-compose exec app npm run build
```

実施方法2: appコンテナ内でnpmを実行する

```bash
$ docker-compose exec app bash
# npm run build
```

### artisan

artisanは dockerと同じく `/laravel-app` ディレクトリ以下で実行できる。
`app` コンテナ内で実行すること。

例: ウェブアプリのルート設定の確認

実施方法1: ホスト側から間接的にartisanを実行する

```bash
$ docker-compose exec app php artisan route:list
```

実施方法2: appコンテナ内でartisanを実行する

```bash
$ docker-compose exec app bash
# php artisan route:list
```

## debug方法

Xdebug3でデバッグできる。

vscodeの場合、`.vscode/launch.json`内に以下のように記載する。
(必要な拡張は各自インストールすること)

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for Xdebug",
            "type": "php",
            "request": "launch",
            "port": 9003,
            "pathMappings": {
                "/var/www": "${workspaceRoot}/creator"
             }
        },
    ]
}
```

app コンテナ内でPHPプログラムを実行する。
またはappのウェブアプリを実行する際にvscode等でデバッグが可能となります。

vscode等でブレークポイントを設定した状態でデバッガを起動して待機し、
ウェブアプリを実行したときにブレークポイントの行までプログラムが実行されると
プログラムはそこで動作を停止し、変数等の情報を得られるようになります。

デフォルトでは全てのPHPプログラムがxdebugをONにしており、デバッガを設定していない場合、

```
Xdebug: [Step Debug] Could not connect to debugging client. Tried: host.docker.internal:9003 (through xdebug.client_host/xdebug.client_port) :-(
```

というワーニングが表示されます。不快な場合は `php-xdebug.ini` のxdebug.start_with_requestをコメントアウトしてください。

## DEBUGBARについて

開発中のウェブページでブラウザ下部に表示される赤いバーはLaravelのDEBUGBARです。

laravel-app/.env

において

```
APP_DEBUG=true
```
または
```
APP_DEBUG=false
DEBUGBAR_ENABLED=true
```
の場合に表示されます。
