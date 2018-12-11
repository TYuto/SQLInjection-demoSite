
<template>
  <section>
    <b-card class="mx-5 my-5">
      <h4>sqlの実行</h4>
      <b-input
        v-model="sqlquerry"
        @keydown.enter.native="goquerry"/>
    </b-card>
    <b-card class="mx-5 my-5">
      <h4>実行されたsql</h4>
      <p 
        style="white-space: pre;"
        v-text="querrys"/>
    </b-card>
    <b-card class="mx-5 my-5">
      <h4>tableの表示</h4>
      <b-input
        v-model="tablename"
        @keydown.enter.native="selectall"/>
      <b-table :items="items"/>
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
      querrys: '',
      tablename: ''
    }
  },
  created: function() {
    this.db = new sql.Database();
    this.resetdb()
  },
  methods: {
    runquery: function(querry) {
      this.querrys += querry + "\n\n"
      this.querrys = this.querrys.split('\n').slice(-10).join('\n') 
      let res = this.db.exec(querry)
      console.log(res)
      return res
    },
    goquerry: function(){
      let querry = this.sqlquerry
      let res = this.runquery(querry)
      this.sqlquerry = ''
    },
    resetdb: function() {
      let sqlstr = "CREATE TABLE users (id int, username char, password char);\n";
      sqlstr += "INSERT INTO users VALUES (0, 'admin','passwordhoge');\n"
      sqlstr += "INSERT INTO users VALUES (1, 'user1', 'passpass');"
      this.runquery(sqlstr)
      let res = this.runquery("SELECT * FROM users");
    },
    selectall: function(){
      let res = this.runquery("SELECT * FROM " + this.tablename);
      this.items = res[0].values
    }
  }
}
</script>

<style>
</style>
