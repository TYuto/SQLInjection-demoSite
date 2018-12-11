
<template>
  <section>
    <b-button
      class="mx-2 my-2"
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
      <b-input
        v-model="tablename"
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
  </section>
</template>

<script>
const sql = require('sql.js')
export default {
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
      tables: []
    }
  },
  created: function() {
    this.resetdb()
  },
  methods: {
    formatlabe: function(arr) {
      let rarr = {}
      console.log(arr)
      for (let i = 0; i<arr.length;i++){
        rarr[i] = {label: arr[i]}
      }
      console.log(rarr)
      return rarr
    },
    runquery: function(querry) {
      this.querrys += querry + "\n\n"
      this.querrys = this.querrys.split('\n').slice(-17).join('\n') 
      let res = this.db.exec(querry)
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
      sqlstr += "INSERT INTO USERS VALUES (0, 'admin','passwordhoge');\n"
      sqlstr += "INSERT INTO USERS VALUES (1, 'user1', 'passpass');"
      this.runquery(sqlstr)
      let res = this.runquery("SELECT * FROM USERS;");
    },
    selectall: function(){
      let res = this.runquery("SELECT * FROM " + this.tablename + ';');
      this.items = []
      this.items = res[0].values
      this.fields = this.formatlabe(res[0].columns)
    }
  }
}
</script>

<style>
</style>
