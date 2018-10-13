# GraphQL => Ruby on Rails

## ■ GraphQL-Rubyの設定

### 1、Gemfileに下記を追加。
・Gemfile  
``` Gemfile
gem 'graphql'  
```

### 2、gemをインストール
``` Gemfile
$ bundle install  
```

### 3、GraphQLをインストール
``` bash
$ rails g graphql:install  
```

### 4、作成されたディレクトリを確認
3のコマンドを実行すると下記のようなディレクトリツリーが出力される。  
``` Directory
app/graphql
├── graphql_ruby_demo_schema.rb
├── mutations
└── types
    ├── mutation_type.rb
    └── query_type.rb
```

### 5、再度gemをインストール
3のコマンドを実行するとGemfileに`graphiql-rails`というGemが自動で書き加えられるので、再度bundle  
``` bash
$ bundle install  
```

### 6,ルーティング確認
自動で設定してくれている  
・config/routes.rb  
``` file
Rails.application.routes.draw do
  if Rails.env.development?                                                         #追記
    mount GraphiQL::Rails::Engine, at: "/graphiql", graphql_path: "/graphql"        #追記
  end                                                                               #追記

  post "/graphql", to: "graphql#execute"                                            #追記
end
```

### 7、ローカルホストで表示
`http://localhost:3000/graphiql` でgraphiqlの画面が表示される。  

### 8、Hello World!を表示させる
下記のクエリを投げて`Hello World!`が返ってくれば設定完了！
``` query
{
  testField
}
```

***

## ■ DBからデータを取って表示する

### 1、モデルを作成
``` bash
$ rails g model User name:string email:string  
$ rails db:migrate
```

### 2、GraphQLオブジェクトを作成
``` bash
$ rails g graphql:object Task id:ID! title:String! contet:String!
```

### 3、オブジェクトファイルを書き換え
・app/graphql/types/user_type.rb  
``` file
Types::TaskType = GraphQL::ObjectType.define do
  name 'Task'

  field :id, !types.ID
  field :title, !types.String
  field :content, types.String
  field :lane_id, types.String
end
```

### 4、Queryと紐付け
・app/graphql/types/query_type.rb  
``` file
Types::QueryType = GraphQL::ObjectType.define do
name 'Query'

  field :tasks do
    type types[Types::TaskType]
    resolve -> (obj, args, ctx)  {
      Task.all
    }
  end
end
```

### 5、GraphqlContorollerを確認
・app/controllers/graphql_Controller
``` file
class GraphqlController < ApplicationController
  def execute
    variables = ensure_hash(params[:variables])
    query = params[:query]
    operation_name = params[:operationName]
    context = {
    }
    result = GraphqlRubyDemoSchema.execute(query, variables: variables, context: context, operation_name: operation_name)
    render json: result
  end

...
```

### 6、クエリを投げる
``` query
{
  tasks {
    id
    title
  }
}
```

### 7、結果
``` query
{
  "data": {
    "tasks": [
      {
        "id": "1",
        "title": "task01"
      },
      {
        "id": "2",
        "title": "task02"
      },
      {
        "id": "3",
        "title": "task03"
      }
    ]
  }
}
```
