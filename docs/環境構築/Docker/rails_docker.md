# Ruby on Rails / Docker

## Dockerディレクトリを作成

### 1、ディレクトリ構成
``` Directory
docker-rails/
  |-docker-compose.yml  
  |-Gemfile  
  |-Gemfile.lock  
  |-docker/  
     |-mysql/  
     |  |-password.yml    #MySQLのパスワードはここに記述  
     |  |-volumes/        #このフォルダにMySQLのデータを永続化  
     |-rails/  
     |  |-Dockerfile      #Rails用のDockerfile  
```

## 2、Dockerfileを作成
`./docker/rails/Dockerfile`  
``` Docker
FROM ruby:2.5.1

ENV LANG C.UTF-8

# for MySQL
# 必要なライブラリインストール  
RUN apt-get update -qq && apt-get install -y build-essential mysql-client nodejs

RUN gem install bundler

WORKDIR /tmp
ADD Gemfile Gemfile
ADD Gemfile.lock Gemfile.lock
RUN bundle install

ENV APP_HOME /myapp
RUN mkdir -p $APP_HOME
WORKDIR $APP_HOME
ADD . $APP_HOME
```

## 3、docker-compose.ymlを作成
`./docker-compose.yml`
``` Docker
version: '2'
services:
  db:
    image: mysql:5.7.17
    ports:
      - "3306:3306"
    volumes:
      - ./docker/mysql/volumes:/var/lib/mysql  
    extends:
      file: ./docker/mysql/password.yml
      service: password
  web:
    build:
      context: .
      dockerfile: ./docker/rails/Dockerfile
    command: bundle exec rails s -p 3000 -b '0.0.0.0'
    ports:
      - "3000:3000"
    volumes:
      - .:/myapp
    environment:
      RAILS_ENV: development
    extends:
      file: ./docker/mysql/password.yml
      service: password
    depends_on:
      - db
```

## 4、MySQLのパスワードを設定
./docker/mysql/password.yml
``` Docker
version: '2'
services:
  password:
    environment:
      MYSQL_ROOT_PASSWORD: password
```

## 5、Gemfileを作成
`./Gemfile`
``` Ruby
source 'http://rubygems.org'
gem 'rails', '5.2.1'
```

## 6、Gemfile.lockを作成
`./Gemfile.lock`
``` Ruby
空ファイル
```

***

## ■ プロジェクトの作成

## 1、新規アプリを作成
``` bash
$ docker-compose run --rm web rails new . --force --database=mysql --skip-bundle  
```

## 3、Dockerイメージを構築
``` bash
$ docker-compose build
```

## 2、データベースに接続
./config/database.yml
``` Ruby hl_lines="6 7"
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password: <%= ENV['MYSQL_ROOT_PASSWORD'] %>         #追記
  host: db                                            #追記  

development:
  <<: *default
  database: myapp_development

test:
  <<: *default
  database: myapp_test
```

## 3、データベースを作成
``` bash
$ docker-compose up -d  
$ docker exec -it <コンテナID> bash  
# rails db:create  
```

## 4、表示確認
`localhost:3000`でRailsのウェルカムページが表示されればOK！  
