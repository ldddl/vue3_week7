<template>
  <div class="hello">
    <img :src="bird" v-if="isBird" alt="...">
    <ul v-for="item in products" v-else :key="item.id">
      <li>{{item.id}}</li>
    </ul>
  </div>
</template>

<script>
const products = [
  {
    id: '123',
    name: 'aassa'
  },
  {
    id: '456',
    name: 'aass456a'
  }
]
export default {
  data () {
    return {
      products: [],
      bird: 'https://media.discordapp.net/attachments/825605859555016706/918477721372352522/image0.gif',
      isBird: true
    }
  },
  name: 'HelloWorld',
  props: {
    msg: String
  },
  mounted () {
    setTimeout(() => {
      this.products = products
      this.isBird = false
    }, 3000)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
