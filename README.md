# 説明

Dockerを使って、既存のレンタルサーバー上のWordpress開発環境をローカルに再構築するためのコードです。

非エンジニアの方でもgithubに登録してこのレポジトリをgit cloneして、（コピペでも可)
dockerDeskTopを入れたら、手順通りの操作をするだけで実現できると思います！

## 0.DockerForDesktopを入れてない人はまずインストールしておいてください
https://www.docker.com/products/docker-desktop

## 1.準備
ここでダウンロードしたデータをあとで使うのでわかりやすいところに置いておいてください
### 1-1.FTPクライアント(FileZillaなど)を使ってレンタルサーバー上のworpressディレクトリをダウンロードする
接続できなくて少し苦戦するかもしれませんが、host名、idなどをコピペでなく手打ちするとうまくいったりします。
### 1-2.レンタルサーバー上のデータベースのバックアップを作成してダウンロードする
 hogehoge.sqlという感じのファイルが取得できるはずです。
バックアップをとるデータベースを選択してExportタブをクリックしたページでダウンロードできるはずです。
![image](https://user-images.githubusercontent.com/42676382/106375949-04d1b180-63d4-11eb-8c37-dfb35b8564d4.png)

## 2.WordpressInDockerディレクトリ直下にダウンロードしたディレクトリを配置
以下の位置です。
```
-WordpressInDocker
 |- wordpress ←　ここ
 |- docker-compose.yaml
 |-.gitignore
 |-
```
## 3.コンテナを立ち上げる
WordpressInDockerに移動してから以下を実行します。

```
docker-compose up -d
```
これでコンテナが立ち上がります。

以下のコマンドを打って、下の画像のようになればOKです！
もしコンテナの数が合わなければ,もう一度上のコマンドを実行してみてください

![image](https://user-images.githubusercontent.com/42676382/106376435-a78c2f00-63d8-11eb-8a4f-278332211d5e.png)


今は実行する必要はないですが
停止するときは以下

```
docker-compose down
```
## 4. バックアップを格納するためのデータベースをMySqlサーバー内に作成する
下のコードはlocalDBと言う名前のデータベースを作成します
localDBと言う名前で作成していただければ、以降の手順で自分で書き換える必要はなくなります。
```
docker container exec -it mysql mysql -u root -proot -e "create database localDB;"
```
## 5. http://localhost:8888 を開いてphpMyAdminにアクセスし、データをインポートする
ID,PASSWORDは
ID:root
PASSWORD:root
です。

ログイン画面に入ったら、localDBデータベースを選択してImporタブを開いて先ほどダウンロードしたファイルを選択してください
![image](https://user-images.githubusercontent.com/42676382/106376012-80336300-63d4-11eb-8d87-a1a5869cbe2d.png)

これで既存のデータをローカル環境に移設できました。


## 6. 作成したデータベースの値をローカル環境用に編集する
データベースをクリックすると下の画像のようにテーブルの一覧が表示されると思います。

このなかからoptionsと言う名前のついたテーブルを開いて値を編集します。

このテーブルには現在公開しているワードレプレスページのURLが記載されているので
これを画像赤枠のように http://localhost:8080 に書き換えます。
(#5で使ったのは8888ですが、ここで設定するのは8080です。間違えないように) 
![image](https://user-images.githubusercontent.com/42676382/106375670-680e1480-63d1-11eb-9d33-96676c0091fb.png)

## 7. WordPressのデータベース設定を編集する
wordpress/wp-config.phpを開いてデータベースの設定を編集します。
赤線部のように各項目を修正してください。
一番上のDB_NAMEは #4で設定したものと一致させてください
![image](https://user-images.githubusercontent.com/42676382/106375793-aa842100-63d2-11eb-955e-f6f73827a414.png)

コピペ用
```
// ** MySQL 設定 - この情報はホスティング先から入手してください。 ** //
/** WordPress のためのデータベース名 */
define('DB_NAME', 'localDB');

/** MySQL データベースのユーザー名 */
define('DB_USER', 'root');

/** MySQL データベースのパスワード */
define('DB_PASSWORD', 'root');

/** MySQL のホスト名 */
define('DB_HOST', 'db');
```

## おわり。

以上です。

あとは

http://localhost:8080 を開けば、Wordpressの公開ページが

http://localhost:8080/admin を開けば、Wordpressの管理ページが

http://localhost:8888 を開けば、phpMyAdminがそれぞれ確認できるはずです。

管理ページへのログインID,PASSは既存のものをそのまま使ってください

ファイルを直接編集したい場合は、WordpressInDocker/wordpressの中をいじれば直接反映されます。
