# Laravel8入門 Sailで簡易掲示板を作ろう

# 1 今回やること

大きく分けて以下の4つ
* 基本機能
* データベース
* CRUD処理
* リレーション

今回はLaravel x sailで作成していく

# 2 環境構築
## 用意するもの
* VSCode
## 手順
### ①Laravelファイル生成
MyBBSというフォルダを作り、その中でLaravelに必要なファイルを生成してくれる下記コマンド実行<br>
Windowsで実行する際、ターミナルはWSLを使用<br>
```
curl -s "https://laravel.build/MyBBS" | bash
```
※上のコマンドを入力しても何も起きなかった場合、下記コマンドを直接実行したら解決
```
docker run --rm \
    -v "$(pwd)":/opt \
    -w /opt \
    laravelsail/php81-composer:latest \
    bash -c "laravel new MyBBS && cd MyBBS && php ./artisan sail:install --with=mysql,redis,meilisearch,mailhog,selenium "
```

### ②VSCodeで生成したMyBBSフォルダを開く
各種フォルダが生成されていることを確認<br>

主なフォルダ達(※間違って消さないように)
- app : アプリケーションに関するコードを書く場所
- config：各種設定
- database：データベースの設定
- public：JSやCSS等ブラウザからアクセスできるフォルダ

### ③VSCode拡張機能の導入
下記2つの拡張機能を入れる
- EditorConfig for VS Code 
<br>→Laravel標準のエディタ設定を使用する為
- Laravel Blade Snippets
<br>→Blade記法をVSCodeで扱いやすくする為

### ④Laravel初期設定
.envファイルを開き、下記コマンドを追加
(ブラウザでアクセスする際のポートを変更)
```
APP_PORT=8573
```
config/app.phpを開き、timezoneをAsia/Tokyoに変更(だいたい70行目)
```
'timezone' => 'UTC',
↓
'timezone' => 'Asia/Tokyo',
```
localeをjaに変更(だいたい83行目)
```
'locale' => 'en',
↓
'locale' => 'ja',
```

### ⑤コンテナ立ち上げ
ターミナルで下記を入力
```
./vendor/bin/sail up -d
```
通常は docker-compose up -d とするところだが、 docker のコンテナに対する操作に使える sail という便利なコマンドが Laravel に用意されているので、そちらを使う<br>

Dockerを停止したい場合はターミナルで下記を入力
```
./vendor/bin/sail down
```

**環境構築は以上です**

# 3 学んだことメモ

## ルーティングについて
routes/web.phpにこのような記述がある
```
Route::get('/', function () {
    return view('welcome');
});
```
これはget形式、
通常のアクセス(URLに何もつけない)ならこの関数を実行しなさいという意味(=ルーティング)<br>
welcome = resources/welcome.blade.phpのこと

## indexへの値渡し

web.phpで下記のように定義
```
    $posts = [
        'Title A',
        'Title B',
        'Title C',
    ];

    return view('index')
        ->with(['posts' => $posts]);
```
こうすると、viewのindexで$postsの内容をpostsという変数名で受け取れる

## var_dumpの便利な使い方(Laravel限定)

置き換えると見やすくなる
```
var_dump($posts);
exit;
↓
dd($posts);
```
でもddって直感的に意味が伝わりづらい気がする

## 開発の大きな流れ

* web.phpにルーティング定義
* ルーティングに対応する処理をControllerに書く
* Controllerでviewを呼びだすことでページ表示ができる
* viewではコンポーネントやControllerから渡された値を埋め込める

## Sailコマンド

### コントローラー作成

PostControllerという名前で作成<br>
作成先：Http/Controllers/PostController.php
```
./vendor/bin/sail artisan make:contsroller PostController
```

### sailでDBログイン

DB名：mybbs<br>
詳しくは.envファイルを確認
```
./vendor/bin/sail mysql mybbs
```

### sailでマイグレーションファイル作成

作成先：database/migrations/
```
./vendor/bin/sail artisan make:migration create_posts_table
```

### マイグレーション実行

```
./vendor/bin/sail artisan migrate
```

### Eloquent

テーブルをPHPのオブジェクトのように扱う<br>
app/ModelsにPost.phpを作成
```
./vendor/bin/sail artisan make:model Post
```

### tinker

ターミナルからデータを操作できる
```
./vendor/bin/sail tinker

(データ追加する際の入力例)
$post = new App\Models\Post();
$post->title = 'Title 1';
$post->body = 'Body 1';
$post->save();
exit;
```
別の方法<br>
Post.phpにfillable追加
```
class Post extends Model
{
    use HasFactory;

    protected $fillable = [
        'title',
        'body',
    ];
}
```

### マイグレーション、モデル、コントローラーをまとめて作る<br>
-mcをつけるとマイグレーションとコントローラーを一緒に作る
```
sail artisan make:model ModelName -mc
```

## その他
URLから渡されたidをもとに暗黙的にモデルのデータに結びつけてくれる仕組み<br>
→Implicit Binding

Laravelではhtmlspecialcharsの代わりにe()を使って書ける<br>
→直感的に分かりづらい表現かも

# 4 またの機会にやれそうなこと

## ビュー、レイアウト整理
もうちょっと綺麗な(それっぽい)見た目に変更する

## ページング
投稿が増えれば増えるほど縦に長くなるので

## ログイン機能
Laravel使ってるのに何故かレッスンで触れなかった為

## 違う環境で作成
例：https://qiita.com/moritalous/items/14d4099023981dcf4fd2
