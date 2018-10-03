# 概要
envoy + github のwebhook機能を利用して簡易的な自動デプロイの仕組みを作る
#### envoy
blade記法により設定が可能なタスクランナー  
artisanコマンド、gitコマンド、linuxコマンドを実行可能  

#### webhook
- アプリケーションの更新情報を他のアプリケーションへリアルタイム提供する仕組みや概念のこと。  
- イベント（リポジトリにプッシュなど）発生時、指定したURLにPOSTリクエストする仕組みのこと。

#### 自動デプロイの流れ
githubのmasterブランチにソースをマージ  
↓  
webhookによりデプロイサーバのtrigger.phpを実行  
↓  
trigger.phpのexec()関数でenvoyコマンドを実行  
↓  
本番環境デプロイ  

# Envoyインストール  
[前提]  
※デプロイサーバで作業  
※composerがインストールされること 
```
#インストール
composer global require laravel/envoy
composer global update

#パス追加
vi ~/.bash_profile
/home/frontapp/.config/composer/vendor/bin
source ~/.bash_profile

#インストール確認
#バージョン確認ができればOK
[frontapp@ip-172-31-46-185 ~]$ envoy -v
Laravel Envoy 1.4.1
```

# 初期設定
### ディレクトリ作成
```
#今回はdeployディレクトリを作成
cd /mnt/data/laravel
mkdir deploy
```
### Envoyファイル作成
```
vi Envoy.blade.php
```
```php

//本来、Envoyコマンドを実行するサーバとデプロイ先のサーバは分離すべきだが今回は同一サーバで実行
@servers(['localhost' => '127.0.0.1'])

@setup
    $path = '/mnt/data/laravel';
    $repo = 'front_app';
    $branch = 'master';
    $timezone = 'Asia/Tokyo';
    $date    = new DateTime('now', new DateTimeZone($timezone));
    $backup = $repo  . $date->format('YmdHis');
@endsetup

@story('deploy')
    backup
    pull
    migrate
    composer
    httpd
    done
@endstory

@task('backup', ['on' => $on])
    cd {{$path}}
    cp -rp {{$repo}} {{$backup}}
@endtask

@task('pull', ['on' => $on])
    cd {{$path}}/{{$repo}}
    git pull origin master
    echo "Repository has been pulled"
@endtask

@task('migrate', ['on' => $on])
    cd {{$path}}/{{$repo}}
    php artisan migrate --force
    echo "done migrate"
@endtask

@task('composer', ['on' => $on])
    cd {{$path}}/{{$repo}}
    composer install
    echo "Composer dependencies have been installed"
@endtask

@task('httpd', ['on' => $on])
    sudo systemctl restart httpd
    echo "httpd have been restarted"
@endtask

@task('done', ['on' => $on])
    echo "Deployment finished successfully!"
@endtask
```
### trigger.php作成
```
vi trigger.php
```
```php
<?php
 
// 設定
$timezone = 'Asia/Tokyo';
$date    = new DateTime('now', new DateTimeZone($timezone));
$LOG_FILE = dirname(__FILE__).'/logs/hook.log';
$LOG_FILE_ERR = dirname(__FILE__).'/logs/hook-error.log';
$SECRET_KEY = 'fhtxwaGQ';
 // 実行するコマンド
 $COMMANDS = array(
    'refs/heads/develop'=>'',//developブランチ
    'refs/heads/master'=>'cd /var/laravel/deploy;envoy run deploy --on=localhost' // masterブランチ
  );
 
 
$header = getallheaders();
$hmac = hash_hmac('sha1', $HTTP_RAW_POST_DATA, $SECRET_KEY);
if ( isset($header['X-Hub-Signature']) && $header['X-Hub-Signature'] === 'sha1='.$hmac ) {
    $payload = json_decode($HTTP_RAW_POST_DATA, true);  // 受け取ったJSONデータ
    // ここに実行したいコードを書く
    foreach ($COMMANDS as $branch => $command) {
        // ブランチ判断
        if(  $payload['ref'] == $branch){
          if($command !== ''){
            // コマンド実行
            exec($command,$output,$status);
            file_put_contents($LOG_FILE, $date->format('Y-m-d H:i:s')." ".$_SERVER['REMOTE_ADDR']." ".$branch." ".$payload['commits'][0]['message']."\n", FILE_APPEND|LOCK_EX);
            if($status > 0)$LOG_FILE = $LOG_FILE_ERR;
            foreach ($output as $line){
                file_put_contents($LOG_FILE, $line."\n", FILE_APPEND|LOCK_EX);
            }
          }
        }
      }
} else {
    // 認証失敗
    file_put_contents($LOG_FILE_ERR, $date->format('Y-m-d H:i:s')." invalid access: ".$_SERVER['REMOTE_ADDR']."\n", FILE_APPEND|LOCK_EX);
}
?>
```


# 使い方  
### Envoy.blade.phpの実行 
```
#storyの実行
#task単体で実行したい場合はtask名で実行
envoy run deploy --on=localhost
```
#### エラー
下記エラーが発生
```
PHP Fatal error:  Uncaught Error: Call to undefined function Laravel\Envoy\posix_getpwuid() in /home/frontapp/.config/composer/vendor/laravel/envoy/src/ConfigurationParser.php:63
```
php-posixをインストールして解決
```
sudo yum install --enablerepo=remi-php71 php-posix
```

### trigger.phpの実行
```
php -f trigger.php
```

### 実行結果のログ
実行ログ
* deplpy/logs/hook.logに出力

エラーログ
* deplpy/logs/hook-error.logに出力