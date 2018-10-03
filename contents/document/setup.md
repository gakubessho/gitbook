# 機能
* マークダウン記法で記載したファイルをコマンド1発でPDF,HTML化可能
* githubで管理することでバージョン管理が可能
* ドキュメント全体でテキスト検索が可能
* https://www.gitbook.com/ にサインアップして利用することも可能だが今回はソースインストールで利用  

# セットアップ
[前提]  
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
カレントディレクトリの.mdをHTML変換しdocs配下に出力
```
gitbook build ./ docs/ 
```