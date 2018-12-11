
<template>
  <section>
    <b-card class="mx-2 my-2">
      <h2>アプリケーション本体</h2>
      <b-card v-if="isLogin">
        <p>Hello {{ username }} </p>
        <b-button
          variant="info"
          @click="isLogin = false">logout</b-button>
      </b-card>
      <b-card v-else>
        <b-input
          v-model="usernameInput"
          placeholder="username"
          @keydown.enter.native="goquerry"/>
        <b-input
          v-model="passwordInput"
          type="password"
          placeholder="password"/>
        <b-button
          variant="info"
          @click="login">login</b-button>
        <b-alert
          :show="message!=''"
          variant="danger">{{ message }}</b-alert>
      </b-card>
    </b-card>
    <b-card class="mx-2 my-2">
      <h2>デバッグ用</h2>
      <b-button
        class="mx-2 my-2"
        variant="info"
        @click="resetdb">データベースのリセット</b-button>
      <b-card class="mx-2 my-2">
        <h4>sqlの実行</h4>
        <b-input
          v-model="sqlquerry"
          placeholder="sql"
          @keydown.enter.native="goquerry"/>
        <b-card class="mx-2 my-2">
          <b-table
            :items="itemeonece"
            :fields="fieldonece"/>
        </b-card>
      </b-card>
      <b-card class="mx-2 my-2">
        <h4>tableの表示</h4>
        <vue-bootstrap-typeahead 
          v-model="tablename"
          :data="tables"
          :min-matching-chars="0"
          placeholder="table名"
          @keydown.enter.native="selectall"/>
        <b-card class="mx-2 my-2">
          <b-table
            :items="items"
            :fields="fields"/>
        </b-card>
      </b-card>
      <b-card class="mx-2 my-2">
        <h4>実行されたsql</h4>
        <p 
          style="white-space: pre;"
          v-text="querrys"/>
      </b-card>
    </b-card>
  </section>
</template>

<script>
const sql = require('sql.js')
import VueBootstrapTypeahead from 'vue-bootstrap-typeahead'

export default {
  components: {
    VueBootstrapTypeahead
  },
  data: function() {
    return {
      sqlquerry: '',
      db: {},
      stdout: '',
      items: [],
      fields: {},
      querrys: '',
      tablename: '',
      itemeonece: [],
      fieldonece: {},
      tables: [],
      isLogin: false,
      usernameInput: '',
      passwordInput: '',
      message: ''
    }
  },
  created: function() {
    this.resetdb()
  },
  methods: {
    formatlabe: function(arr) {
      let rarr = {}
      for (let i = 0; i<arr.length;i++){
        rarr[i] = {label: arr[i]}
      }
      return rarr
    },
    runquery: function(querry) {
      this.querrys += querry + "\n\n"
      this.querrys = this.querrys.split('\n').slice(-17).join('\n') 
      let res = this.db.exec(querry)
      let tables = this.db.exec("select name from sqlite_master where type='table';")
      let ts = []
      tables[0].values.forEach(c => ts.push(c[0]))
      this.tables = ts
      console.log(this.tables)
      console.log(res)
      return res
    },
    goquerry: function(){
      let querry = this.sqlquerry
      let res = this.runquery(querry)
      this.itemeonece = res[0].values
      this.fieldonece = this.formatlabe(res[0].columns)
      this.sqlquerry = ''
    },
    resetdb: function() {
      this.db = new sql.Database();
      let sqlstr = "CREATE TABLE USERS (id int, username char, password char);\n";
      sqlstr += "INSERT INTO USERS VALUES (0, 'admin','adminpassword');\n"
      sqlstr += "INSERT INTO USERS VALUES (1, 'normaluser', 'userpassword');"
      this.runquery(sqlstr)
    },
    selectall: function(){
      let res = this.runquery("SELECT * FROM " + this.tablename + ';');
      this.items = []
      this.items = res[0].values
      this.fields = this.formatlabe(res[0].columns)
    },
    login: function() {
      let user = this.runquery('SELECT username FROM USERS WHERE username="'+this.usernameInput+'" and password="'+this.passwordInput+'"')
      if(typeof user[0] !== "undefined") {
        this.isLogin = true
        this.username = user[0].values[0][0]
        this.message = ''
      } else{
        this.message = "ユーザー名又はパスワードが間違っています"
        console.log(this.message)
      }
    }
  }
}
</script>

<style>
</style>
