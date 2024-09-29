プロジェクトの初期化コマンド

```
rails new webpack-bulma -j webpack -c bulma --skip-action-cable --skip-action-text --skip-action-mailer --skip-action-mailbox --sukip-jbuilder
```

package.json

```js
{
  "name": "app",
  "private": true,
  "devDependencies": {
    "webpack": "^5.94.0",
    "webpack-cli": "^5.1.4"
  },
  "scripts": {
    "build": "webpack --config webpack.config.js",
    "build:css": "sass ./app/assets/stylesheets/application.bulma.scss:./app/assets/builds/application.css --no-source-map --load-path=node_modules"
  },
  "dependencies": {
    "@hotwired/stimulus": "^3.2.2",
    "@hotwired/turbo-rails": "^8.0.10",
    "bulma": "^1.0.2",
    "sass": "^1.79.3"
  }
}
```

JS のビルド

```js
"build": "webpack --config webpack.config.js"
```

Webpack は`webpack.config.js`に設定された記載にもとづいて、プロジェクト内の JavaScript ファイルをバンドルし、最終的なビルドファイルを生成する。

webpack.config.js はこのようになっている。

```js
// webpack.config.js
module.exports = {
  mode: "production",
  devtool: "source-map",
  entry: {
    application: "./app/javascript/application.js"
  },
  output: {
    filename: "[name].js",
    sourceMapFilename: "[file].map",
    chunkFormat: "module",
    path: path.resolve(__dirname, "app/assets/builds")
  },
  plugins: [
    new webpack.optimize.LimitChunkCountPlugin({
      maxChunks: 1
    })
  ]
}
```

- エントリーポイントを`./app/javascript/application.js`としている。ここから依存関係をたどり、コードを解析する
- ビルドしたファイルの出力先は`app/assets/builds`としている。これを Sprockets が見て、公開ディレクトリに配信する
- チャンクの生成(`new webpack.optimize.LimitChunkCountPlugin`)
  - 解析されたコードは、1 つまたは複数のチャンクに分割される。こうすることで、必要な部分だけを効率的にロードできる
  - チャンク(chunk)とは、Webpack がビルドプロセス中に生成する JS ファイルの単位のこと。チャンクを使うことで、アプリケーションのコードを複数のファイルに分割し、初回ロード時に必要なコードだけを読み込むことができ、パフォーマンス向上につながる
  - `application.js`から始まるすべての依存関係が 1 つのチャンクにまとめられて、`app/assets/builds/application.js`として出力される

## bin/dev

```
exec foreman start -f Procfile.dev --env /dev/null "$@"
```

- `exec`:
  - 後ろの foreman に制御を渡す
  - exec を使うと、新しいプロセスは作成されない。代わりに、現在のシェルプロセス自体が指定されたコマンドに「変身」する
  - 元のシェルプロセスが完全に置き換えられるので、exec の後のコマンドが終了すると、元のシェルセッションも終了する
- `start`:
  - Foremanを実行する。Foremanとは開発環境でよく使われるプロセスマネジャー。たくさんのプロセスを一度に動かすのを手伝ってくれるツール。これを使わないなら、複数のプロセスを個別に起動する必要がある
