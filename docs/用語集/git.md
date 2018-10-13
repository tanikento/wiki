# Git

## コマンド集
* リポジトリを作成  
```
$ git init
```  

* ローカルにクローンする  
```
$ git clone <url>
```  

* インデックスに追加(Git管理の対象にあげる)  
```
$ git add <ディレクトリ名やファイル名>  
(例) `$ git add test`、`$ git add test.md
```  

* コミットする  
```
$ git commit -m <"コメント">  
(例) `$ git commit -m "テストです"
```  

* addとcommitを同時に行う  
```
$ git commit -a <ディレクトリ名やファイル名>  
(例) `$ git commit -a test`、`$ git commit -a test.md
```  

* ローカルのブランチ名を変更(現在いないブランチの場合)  
```
$ git branch -m <古いブランチ名> <新しいブランチ名>
```  

* ローカルのブランチ名を変更(現在いるブランチの場合)  
```
$ git branch -m <新しいブランチ名>
```  

* リモートリポジトリの確認  
```
$ git remote -v
```

* リモートリポジトリの追加  
```
$ git remote add origin <追加したいリポジトリ>  
(例) `$ git remote add origin https://github.com/○○○/×××.git
```  

* リモートリポジトリの削除  
```
$ git remote rm origin
```  

* リモートリポジトリの変更  
```
$ git remote set-url origin <new url>
```  

* プッシュする  
```
$ git push -u origin master  
(2回目以降は `$ git push` で可能)
```  

* ブランチを作成  
```
$ git branch <ブランチ名>  
(例) `$ git branch new-branch
```  

* ブランチの一覧を表示(＊が付いているのが現在のブランチ)  
```
$ git branch
```  
