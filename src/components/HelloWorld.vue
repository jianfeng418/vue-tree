<template>
  <div class="hello">
    <h1>{{ msg }}</h1>

    <vtree :treeData='datas' ref='vtree' :clickFun='clickNode'></vtree>

    <button @click='findSelected'>获取选中项</button>

  </div>
</template>

<script>
  import mytree from './mytree'
  import vtree from './v-tree'

 
export default {
  name: 'HelloWorld',
  methods:{
      findParent(){
          console.log(this.$refs)
      },
      findSelected(){
          console.log(this.$refs.vtree.getSelectedNode());
      },
      clickNode($event){
        console.log('you clicked '+ $event.target.id)
      }
  },
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      datas:[
        {text:'xiaoming',
          id:'1',
          icon:'folder',
          children:[
              {text:'1-1',
                id:'1-1',
                icon:'folder',
                children:[
                  {text:'1-1-1',
                    id:'1-1-1',
                    icon:'file'

                  },{text:'1-1-2',
                    id:'1-1-2',
                    icon:'file'

                  }]
              
              }]
        },
        {text:'hua',
          id:'2',
          icon:'folder',
          children:[{
            text:'2-2',
            id:'2-2',
            icon:'file',
          }]
        },
        {text:'hua',
          id:'3',
          icon:'folder',
          children:[{
            text:'3-3',
            id:'3-3',
            icon:'file',
          }]
        }
        ]
    }
  },
  components:{mytree,vtree}
}
</script>

<style scoped>
h1, h2 {
  font-weight: normal;
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
