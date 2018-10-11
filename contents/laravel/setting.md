
#### vagrant立ち上げ
```
vagrant up
```
#### sshログイン
```
vagrant ssh
```

#### 時刻設定
※バックログにアップしたHomestead.zip利用の場合この手順は不要
```
sudo ln -sf /usr/share/zoneinfo/Japan /etc/localtime
sudo locale-gen ja_JP.UTF-8
sudo /usr/sbin/update-locale LANG=ja_JP.UTF-8
sudo timedatectl set-timezone Asia/Tokyo
```

#### 確認
```
date
//現在時刻になっていること
```

```
timedatectl
//Time zone: Asia/Tokyo (JST, +0900)になっていること
```


node,npmインストール確認  
※laravel Homestaedを利用した場合、標準でインストールされている。
```
node -v
npm -v
```

#### npm自身を最新にアップデート
```
sudo npm install -g npm  
```

#### package一括アップデート用にnpm-check-updatesをインストール
```
npm install -g npm-check-updates
```

#### npmのバージョンを最新に更新
```
ncu

```
