# RSpec => Ruby on Rails

## ■ 導入
### 1、Gemfileに下記を追加。
    group :development, :test do
      ...
      gem 'rspec-rails'
      gem 'factory_bot_rails'
      gem 'database_cleaner'
      gem 'faker'
      gem 'pry-rails'
      gem 'pry-coolline'
    end

### 2、gemをインストール
    $ bundle install

### 3、Rspecの実行に必要なファイルを作成
    $ rails g rspec:install
    ...
      create    .rspec
      create    spec
      create    spec/spec_helper.rb
      create    spec/rails_helper.rb

### 4、実際にRspecが動くか確認
    $ bundle exec rspec
    ...
    No examples found.


    Finished in 0.00048 seconds (file took 0.50296 seconds to load)
    0 examples, 0 failures

### 5、DatabaseCleanerを設定
・spec/rails_helper.rb

    RSpec.configure do |config| ←元から記載ある
      ...（うじゃうじゃ文字があるので、ブロックの最後に書く）
      ...
      config.before(:suite) do
        DatabaseCleaner.strategy = :transaction
        DatabaseCleaner.clean_with(:truncation)
        end

      config.around(:each) do |example|
        DatabaseCleaner.cleaning do
          example.run
        end
      end
    end

### 6、FactoryBotを設定
・spec/rails_helper.rb
↓5と同じブッロクで、5で記載したものの下に記載する

    RSpec.configure do |config| ←元から記載ある
      ...（うじゃうじゃ文字があるので、ブロックの最後に書く）
      ...
      config.include FactoryBot::Syntax::Methods
    end

### 7、再度Rspecが動くか確認
    $ bundle exec rspec
    ...
    No examples found.


    Finished in 0.00048 seconds (file took 0.50296 seconds to load)
    0 examples, 0 failures

### 8、モデルを作成
モデルを作成すると、テスト用のファイルも作成される。
（例）model = article

【作成されるファイル】  
・models/article.rb
→モデルの設定をする（validatesなど）

・spec/rails_helper.rb
→さっきのDatabaseCleanerなどを記載

・spec/spec_helper.rb
→触ってない

・spec/factories/article.rb
→FactoryBotがサンプルデータを作成してくれる

・spec/models/article_spec.rb
→テストコードを書く

### 9、FactoryBotのサンプルデータをDBに書き込む
    rails c
    [1]pry(main)>FactoryBot.create(:article)
    ...
    → #<Article:0x007~>
      id: 1,
      title: "MyText",
      body: "MyText",
      ...
### 10、この後にQiitaのModel編でバリデーションテストをして、宮島さんの記事を読むと理解しやすかった！
