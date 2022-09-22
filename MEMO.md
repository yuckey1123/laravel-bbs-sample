# MEMO.md

備忘録とか

## 今回やること

大きく分けて以下の4つ
* 基本機能
* データベース
* CRUD処理
* リレーション

今回はLaravel x sailで作成していく

## 学んだことメモ

### ルーティングについて

routes/web.phpにこのような記述がある

```
Route::get('/', function () {
    return view('welcome');
});
```

これはget形式、
通常のアクセス(URLに何もつけない)ならこの関数を実行しなさいという意味(=ルーティング)<br>
welcome = resources/welcome.blade.phpのこと

### indexへの値渡し

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

### var_dumpの便利な使い方(Laravel限定)

置き換えると見やすくなる

```
var_dump($posts);
exit;
↓
dd($posts);
```

でもddって直感的に意味が伝わりづらい気がする

### 開発の大きな流れ

* web.phpにルーティング定義
* ルーティングに対応する処理をControllerに書く
* Controllerでviewを呼びだすことでページ表示ができる
* viewではコンポーネントやControllerから渡された値を埋め込める

### Sailコマンド

#### コントローラー作成

PostControllerという名前で作成

作成先：Http/Controllers/PostController.php

```
./vendor/bin/sail artisan make:contsroller PostController
```

#### SailでDBログイン

DB名：mybbs

詳しくは.envファイルを確認

```
./vendor/bin/sail mysql mybbs
```

#### sailでマイグレーションファイル作成

作成先：database/migrations/

```
./vendor/bin/sail artisan make:migration create_posts_table
```

#### マイグレーション実行

```
./vendor/bin/sail artisan migrate
```

#### Eloquent

テーブルをPHPのオブジェクトのように扱う

app/ModelsにPost.phpを作成

```
./vendor/bin/sail artisan make:model Post
```

#### tinker

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

別の方法

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

#### マイグレーション、モデル、コントローラーをまとめて作る

-mcをつけるとマイグレーションとコントローラーを一緒に作る

```
sail artisan make:model ModelName -mc
```

#### その他
URLから渡されたidをもとに暗黙的にモデルのデータに結びつけてくれる仕組み

→Implicit Binding

Laravelではhtmlspecialcharsの代わりにe()を使って書ける

→直感的に分かりづらい表現かも

## またの機会にやれそうなこと

### ビュー、レイアウト整理
もうちょっと綺麗な(それっぽい)見た目に変更できそう

### ページング
投稿が増えれば増えるほど縦に長くなるので分けたい

### ログイン機能
Laravel使ってるのに何故か使わなかったし

### 違う環境を作って実装
例：https://qiita.com/moritalous/items/14d4099023981dcf4fd2
