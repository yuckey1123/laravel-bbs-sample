# laravel-bbs-sample

Laravel8 + Sailで制作した簡易掲示板

## このプロジェクトについて

PHPの学習を目的に、[ドットインストール](https://dotinstall.com/lessons)のレッスン["Laravel 8入門 基本機能編"](https://dotinstall.com/lessons/basic_laravel_v3)にて制作したプロジェクトです。

学習完了後、私なりに一部機能追加や修正したものとなります。未来の自分がやっていれば。

## 環境構築

### 用意するもの

- エディタ： [Visual Studio Code(VSCode)](https://azure.microsoft.com/ja-jp/products/visual-studio-code/)

### 1. Laravelファイル生成

MyBBSというフォルダを作り、その中でLaravelに必要なファイルを生成してくれる下記コマンドを実行。

Windowsで実行する場合、ターミナルはWSLを使用する。

```
curl -s "https://laravel.build/MyBBS" | bash
```
※上記コマンドを実行してもファイルが生成されない(何も起こらない)場合がある。その場合は下記コマンドを実行したら解決することがある。
```
docker run --rm \
    -v "$(pwd)":/opt \
    -w /opt \
    laravelsail/php81-composer:latest \
    bash -c "laravel new MyBBS && cd MyBBS && php ./artisan sail:install --with=mysql,redis,meilisearch,mailhog,selenium "
```

### 2. VSCodeでMyBBSフォルダを開く

各種フォルダが生成されていることを確認する。

主なフォルダ構成(※間違って消さないように)
- app: アプリケーションに関するコードを書く場所
- config： 各種設定
- database： データベースの設定
- public： JSやCSS等ブラウザからアクセスできるフォルダ

### 3. VSCode拡張機能の導入

下記2つの拡張機能を入れる。

- EditorConfig for VS Code
<br>→ Laravel標準のエディタ設定を使用する
- Laravel Blade Snippets
<br>→ Blade記法をVSCodeで扱いやすくする

### 4. Laravel初期設定

.envファイルを開き、下記コマンドを追加する。 (ブラウザでアクセスする際のポートを変更)

```
APP_PORT=8573
```

config/app.phpを開き、timezoneをAsia/Tokyoに変更。 (だいたい70行目あたり)

```
'timezone' => 'UTC',
↓
'timezone' => 'Asia/Tokyo',
```

localeをjaに変更。(だいたい83行目あたり)

```
'locale' => 'en',
↓
'locale' => 'ja',
```

### 5. コンテナ立ち上げ

ターミナルで下記を入力。

```
./vendor/bin/sail up -d
```

通常は`docker-compose up -d`とするところだが、dockerのコンテナに対する操作に対応した`sail`というコマンドがLaravelに用意されていて便利なので、使用する。

Dockerを停止したい場合はターミナルで下記を入力。

```
./vendor/bin/sail down
```
