# npmとwebpackで作るReact.js学習のためのいい感じのフロントエンド開発環境 2016年5月の場合。

[チュートリアル | React](https://facebook.github.io/react/docs/tutorial-ja-JP.html)

Reactのチュートリアルをはじめるとしたら公式が配布している環境を使うらしい。

[https://github.com/reactjs/react-tutorial](https://github.com/reactjs/react-tutorial)

```
npm install
node server.js
```
これでOKらしい。  
でもせっかくなのでnpmとwebpackで環境作ってみました。  
ついでなのでBabelでES2015の機能を取り入れた書き方ができるようにしています。  

[Babel · The compiler for writing next generation JavaScript](https://babeljs.io/)

## 必要なものあるかどうか確認

```
node -v
```
まずはお決まりのNode.js入ってるか確認。

```
npm -v
```
npm（Node.jsのパッケージマネージャー）も入ってるか確認。

```
webpack -v
```
webpackも入っているか確認。  
もし入ってなかったら

```
npm install -g webpack
```
-gオプションはGlobalオプションのこと。

## ファイル・フォルダ構成

```
git clone https://github.com/shigekitakeguchi/npm-webpack-reactjs.git
```
[https://github.com/shigekitakeguchi/npm-webpack-reactjs](https://github.com/shigekitakeguchi/npm-webpack-reactjs)

GitHubから落として使ってください。  
カスタマイズなりなんなりして。

```
cd npm-webpack-reactjs
```
落としたフォルダ内に移動する。その前に

```
├── README.md
├── app
│   ├── index.html
│   └── scripts
├── bs-config.json
├── package.json
├── src
│   └── scripts
│       └── index.js
└── webpack.config.js
```
ファイル・フォルダ構成はこんな感じ。README.mdは不要。

```
npm install
```
これで必要なパッケージがインストールされるはず。  

## package.json

```json
{
  "name": "npm-webpack-reactjs",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "devDependencies": {
    "babel-core": "^6.9.0",
    "babel-loader": "^6.2.4",
    "babel-preset-es2015": "^6.9.0",
    "babel-preset-react": "^6.5.0",
    "concurrently": "^2.1.0",
    "lite-server": "^2.2.0",
    "webpack": "^1.13.0"
  },
  "scripts": {
    "webpack": "webpack -w",
    "lite": "lite-server",
    "start": "concurrently \"npm run lite\" \"npm run webpack\""
  },
  "keywords": [],
  "author": "shigeki.takeguchi",
  "license": "MIT",
  "dependencies": {
    "react": "^15.1.0",
    "react-dom": "^15.1.0",
    "react-redux": "^4.4.5",
    "redux": "^3.5.2"
  }
}
```
中身はこんな感じ。

### パッケージの説明

落としてきたpackage.jsonからインストールすればいいんですがそれぞれのパッケージの説明を。

```
npm install --save-dev babel-core
```
[https://github.com/babel/babel/tree/master/packages/babel-core](https://github.com/babel/babel/tree/master/packages/babel-core)

言わずもがな、Babelのコンパイラのコア。

```
npm install --save-dev babel-loader
```
[https://github.com/babel/babel-loader](https://github.com/babel/babel-loader)

webpackでReact.jsをトランスパイルするのに必要。

```
npm install --save-dev babel-preset-es2015
```
[https://github.com/babel/babel/tree/master/packages/babel-preset-es2015](https://github.com/babel/babel/tree/master/packages/babel-preset-es2015)

BabelでES2015を扱うためのプラグイン。

```
npm install --save-dev babel-preset-react 
```

[https://github.com/babel/babel/tree/master/packages/babel-preset-react](https://github.com/babel/babel/tree/master/packages/babel-preset-react)

BabelでReactを扱うためのプラグイン。

```
npm install -save react
```
[https://github.com/facebook/react](https://github.com/facebook/react)

Reactですね。

```
npm install -save react-dom
```
[https://github.com/facebook/react/tree/master/packages/react-dom](https://github.com/facebook/react/tree/master/packages/react-dom)

ReactでDOMを扱うためのものですね。Reactから分離された経緯があるとのこと。

```
npm install -save react-redux
```
[https://github.com/reactjs/react-redux](https://github.com/reactjs/react-redux)

ReactでReduxを扱うためのパッケージ。

```
npm install -save redux
```
[https://github.com/reactjs/redux](https://github.com/reactjs/redux)

Reduxですね。Reduxについては以下などをお読みいただけるとわかりよいかと。  
[Redux入門 1日目 Reduxとは(公式ドキュメント和訳)](http://qiita.com/kiita312/items/b001839150ab04a6a427)

```
npm install --save-dev concurrently
```
[https://github.com/kimmobrunfeldt/concurrently](https://github.com/kimmobrunfeldt/concurrently)

concurrentlyは複数のコマンド実行できるようにするため。具体的に何をしているかは後ほど説明。

```
npm install --save-dev lite-server
```
[https://github.com/johnpapa/lite-server](https://github.com/johnpapa/lite-server)

webpackにもwebpack dev serverというのがあるみたいだけどlite-serverのがシンプルで良さそうなので使ってみた。  
ただし設定ファイルは必要でした。

```
npm install --save-dev webpack
```
[https://github.com/webpack/webpack](https://github.com/webpack/webpack)

webpack。もうすでに何をするツールなのか説明しがたいくらい機能がある。  
静的なファイル（JavaScript系、CSS系、画像ファイル）の依存関係を解決するためのビルドツールってことなんだけど、ここでははJavaScriptだけを扱うようにしている。

## package.jsonの中のscriptsで何をしているか

```json
"scripts": {
  "webpack": "webpack -w",
  "lite": "lite-server",
  "start": "concurrently \"npm run lite\" \"npm run webpack\""
},
```

```
npm start
```
このコマンドでlite-serverを立ち上げwebpackでwatchを行いJavaScriptの変更を監視するようにしている。

```json
"start": "concurrently \"npm run lite\" \"npm run webpack\""
```
scriptsの中にあるstartがこれにあたる。

```
npm run lite
```
```
npm run webpack
```

これらのコマンドはそれぞれ独立したコマンドですが、最初にちょっと触れたがconcurrentlyにダブルクオーテーションでくくってスペースで区切って引数で渡せば並行して実行することになる。便利。

```json
"webpack": "webpack -w",
```
これはwebpackのwatch（監視）を走らせている。こちらも後ほど触れるがwebpack.config.jsonで記述されたことをもとに監視している。

```json
"lite": "lite-server",
```
lite-serverを立ち上げている。bs-config.jsonに設定ないようを記述している（こちらも後ほど触れる）。

## webpack.config.json
```javascript
var webpack = require("webpack");

module.exports = {
  entry: './src/scripts/index.js',
  output: {
    path: __dirname + '/app/scripts',
    filename: 'bundle.js',
		publicPath: '/app/',
  },
  module: {
    loaders: [
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel',
        query: {
          presets: ['react', 'es2015'],
        },
      }
    ]
  },
  plugins: [
    new webpack.optimize.UglifyJsPlugin()
  ]
}
```
webpackの設定はいたってシンプル。entryにもとファイル（複数ある場合は配列で持たせる）。outputに出力される設定を記述。今回はbundle.jsっていう一般的によく使われているらしい名称のまま。  
moduleのなかのloadersでReactとES2015をコンパイル、トランスパイルするように設定している。
素のままのファイルだともうファイルがでかくてあれなんでUglifyJsPluginで圧縮・最適化。

## bs-config.json

```json
{
  "injectChanges": "true",
  "files": ["./app/**/*.{html,htm,css,js}"],
  "watchOptions": { "ignored": "node_modules" },
  "server": { "baseDir": "./app" }
}
```
lite-serverの設定はドキュメントルートをappの直下にしたかったのと監視対象のファイル（html、css、js）が変更されたらリロードしてinjectChangesというBrowsersyncを動すため。

## /src/scripts/index.js

```javascript
var React = require('react');
var ReactDOM = require('react-dom');

var CommentBox = React.createClass({
  render: function() {
    return (
      <div className="commentBox">
        Hello, world! I am a CommentBox.
      </div>
    );
  }
});
ReactDOM.render(
  <CommentBox />,
  document.getElementById('content')
);

```
ReactのチュートリアルにあるHello, world!です。  
id、contentにCommentBox内で記述されている内容を出力しているごくごくシンプルなもの。

---

個人的にはReactを勉強するために環境をさくっと用意したかったために作った。  
なのでReactもまだよくわかってない。またwebpackをはじめて数日とかという状態なので間違えや指摘をいただけると助かります。
