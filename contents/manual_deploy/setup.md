#### gitからレポジトリクローン
```
cd  /mnt/data/laravel
git clone https://github.com/AMkabi/front_app.git
```

#### envファイル作成
```
#.env.exampleの内容をコピー
cp .env.example .env

#プロジェクト毎に.envの必要箇所を修正
DB_HOST = { ＤＢのホスト名 }
DB_DATABASE = { ＤＢ名 }
DB_USERNAME = { ユーザー名 }
DB_PASSWORD = { パスワード }
```

#### アプリケーションキー生成
```
php artisan key:generate
```

#### シンボリックリンク
```
ln -s /mnt/data/laravel/front_app/public  frontapp
```

#### apache再起動
```
sudo systemctl restart httpd
```
