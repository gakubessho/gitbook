# LaravelMixインストール
```
vagrant up
vagrant ssh
#laravelルートディレクトリに移動
cd /home/vagrant/code/laravel

#laravel mixインストール
npm install --no-optional
```

# Laravelmix機能
#### ビルド
タスクの実行
```
npm run dev
```

#### Watch
対象ファイルを監視し変更があれば変更のあったファイルをコンパイル
```
npm run watch
```

#### hot
HMR(Hot Module Replacement)の実行
```
npm run hot
```

#### BrowserSync
```
.browserSync();
```

#### キャッシュバスティング
自動的に全コンパイルファイルのファイル名へ一意のハッシュを追加し、キャッシュを更新できる
ただし実際のファイル名が不明になるため、ビュー中でLaravelのグローバルmix関数を利用する必要がある
```
.version();
```

#### SourceMap
ブラウザの開発ツール向けの追加デバッグ情報を提供
```
.sourceMaps();
```

