# Bootstrap => Vue.js

## 1、 環境
下記を参照
<!-- * [Vue.js_Rails_Docker](https://tanikento.github.io/wiki/環境構築/Vue.js_Rails_Docker/) -->

## 2、bootstrap-vueをインストール
``` bash
npm install --save bootstrap-vue
```

## 3、下記を`main.js`に書き込む
``` javascript
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'
import BootstrapVue from 'bootstrap-vue'
Vue.use(BootstrapVue)
```

## 4、表示確認
`App.vue`などで表示確認（タグで指定するだけ！）
``` Vue
<button type="button" class="btn btn-primary" v-on:click="add"> Add </button>
```
