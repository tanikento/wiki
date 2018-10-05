# 〜GraphQLで表示させる〜

## ■　GraphQL-Rubyの設定
### 1、Gemfileに下記を追加。
・Gemfile  
~~~
gem 'graphql'  
~~~

### 2、gemをインストール
~~~
$ bundle install  
~~~

### 3、GraphQLをインストール
~~~
$ rails g graphql:install  
~~~

### 4、作成されたディレクトリを確認
3のコマンドを実行すると下記のようなディレクトリツリーが出力される。  
~~~
app/graphql
├── graphql_ruby_demo_schema.rb
├── mutations
└── types
    ├── mutation_type.rb
    └── query_type.rb
~~~

### 5、再度gemをインストール
3のコマンドを実行するとGemfileに`graphiql-rails`というGemが自動で書き加えられるので、再度bundle  
~~~
$ bundle install  
~~~

### 6,ルーティング確認
自動で設定してくれている  
・config/routes.rb  
~~~
Rails.application.routes.draw do
+  if Rails.env.development?
+    mount GraphiQL::Rails::Engine, at: "/graphiql", graphql_path: "/graphql"
+  end

+  post "/graphql", to: "graphql#execute"
end
~~~

### 7、ローカルホストで表示
`http://localhost:3000/graphiql` でgraphiqlの画面が表示される。  

### 8、Hello World!を表示させる
下記のクエリを投げて`Hello World!`が返ってくれば設定完了！
~~~
{
  testField
}
~~~

***

## ■　DBからデータを取って表示する
### 1、モデルを作成
~~~
$ rails g model User name:string email:string  
$ rails db:migrate
~~~

### 2、GraphQLオブジェクトを作成
~~~
$ rails g graphql:object User id:ID! name:String! email:String!
~~~

### 3、オブジェクトファイルを書き換え
・app/graphql/types/user_type.rb  
~~~
Types::UserType = GraphQL::ObjectType.define do
  name 'User'

  field :id, !types.ID
  field :name, !types.String
  field :email, !types.String
end
~~~

### 4、Queryと紐付け
・app/graphql/types/query_type.rb  
~~~
Types::QueryType = GraphQL::ObjectType.define do
  name 'Query'

  field :user, !Types::UserType do
    resolve ->(_obj, _args, ctx) {
      ctx[:current_user]
    }
  end
end
~~~

### 5、GraphqlContorollerに下記を追加
・app/controllers/graphql_Controller
~~~
class GraphqlController < ApplicationController
  def execute
    variables = ensure_hash(params[:variables])
    query = params[:query]
    operation_name = params[:operationName]
    context = {
+      current_user: User.last,  # ここでcurrent_userを指定する
    }
    result = GraphqlRubyDemoSchema.execute(query, variables: variables, context: context, operation_name: operation_name)
    render json: result
  end

...
~~~

### 6、クエリを投げる
~~~
{
  user {
    id
    name
    email
  }
}
~~~
