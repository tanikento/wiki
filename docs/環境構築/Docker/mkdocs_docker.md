# MkDocs / Docker

## 1、ディレクトリを作成
``` Directory
mkdocs/                   <= ディレクトリ名は何でも大丈夫
  |-mkdocs.yml            <= mkdocsの設定ファイル
  |-docs/                 <= 基本docsフォルダ
     |-index.md           <= トップページ
     |-memo/
     |  |-memo1.md
     |  |-memo2.md
     |-etc/
     |  |-memo3.md
```

## 2、mkdocs.ymlを作成
`./mkdocks/mkdocs.yml`  
``` file
site_name: My MEMO

# Repository
repo_name:              <= GitHUBのリポジトリ名
repo_url:               <= GitHUBのリポジトリのURL

# Configuration
theme:
  name: 'material'
  language: 'ja'
  feature:
    tabs: true
  palette:
    primary: 'brown'
    accent: 'indigo'
  font:
    text: 'Roboto'
    code: 'Roboto Mono'

# 日本語検索設定
extra:
  search:
    language: 'jp'

# Extensions
markdown_extensions:
  - admonition
  - footnotes
  - pymdownx.keys
  - def_list
  - codehilite:
      linenums: true
  - toc:
      permalink: true
  - pymdownx.tasklist:
      custom_checkbox: true
```

## 3、Dockerイメージを取得
``` bash
$ docker pull squidfunk/mkdocs-material
```

## 4、MkDocsのコンテナを起動
``` bash
$ docker run --rm -it -p 8000:8000 -v `pwd`:/docs squidfunk/mkdocs-material
```

## 5、localhostで表示確認
- `http://localhost:8000`で表示確認ができたらOK

## 6、GitHubでリポジトリを作成

## 7、GitHUB Pagesにアップロード

``` bash
$ docker run --rm -it -v ${PWD}:/docs squidfunk/mkdocs-material gh-deploy
```

!!! NOTE "MEMO"
    - 上記のコマンドでアップロードができる。  
    - siteというディレクトリが自動生成されそのHTMLが表示される。  
    - gh-pagesというブランチが切られ、siteディレクトリがプッシュされる。  

## 8、GitHUBのリポジトリの設定を変更
- リポジトリのSettingsを選択
- 下の方のGitHUB Pagesのsourceの箇所でgh-pages branchでsave  

## 9、WEBで表示確認
- `https://ユーザー名.github.io/リポジトリ名/`で表示確認ができたらOK  
