
<template>
  <section>
    <b-card class="">
      <b-card class="">
        <p> {{ stdout }} </p>
        <b-table :items="items"/>
      </b-card>
      <b-input
        v-model="sqlquerry"
        @keydown.enter.native="goquerry"/>
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
      items: []
    }
  },
  created: function() {
    this.db = new sql.Database();
    this.resetdb()
  },
  methods: {
    goquerry: function(){
      let querry = this.sqlquerry
      let res = this.db.exec(querry)
      console.log(res)
      this.items = res[0].values
      console.log(this.items)
      this.sqlquerry = ''
    },
    resetdb: function() {
      let sqlstr = "CREATE TABLE users (id int, username char, password char);";
      sqlstr += "INSERT INTO users VALUES (0, 'admin','passwordhoge');"
      sqlstr += "INSERT INTO users VALUES (1, 'user1', 'passpass');"
      this.db.run(sqlstr);
      let res = this.db.exec("SELECT * FROM users");
      console.log(res)
    }
  }
}
</script>

<style>
</style>
