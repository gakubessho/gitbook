# セットアップ
[前提]  
※linux,mac os環境であること  
※npm,node.jsがインストール済であること  
```
npm install -g gitbook-cli
mkdir /home/vagrant/document
cd document
gitbook init

vi book.json
{
    "plugins": ["-sharing","hide-published-with"],
    "language": "ja"
}
gitbook install

```
# 使い方  
#### ビルド  
```
gitbook build  
```
