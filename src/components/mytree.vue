<script>
	import Vue from 'vue'
	export default{
		name:'vtree',
		template:'<div class="cus_tree" ><ul><li v-for="item in userData" >'+
		'<div :id=item.id @click.stop="toggleNode(item)" class="cus_tree_content" :class="{active:item.active}"><span>□</span>{{item.text}}</div>'+
		'<vtree :treeData="item.children" v-if="item.expend" ></vtree>'+
		'</li></ul></div>',
		data(){
			return {
				show:true,
				userData:null
			}
		},
		mounted(){
			this.initData();
		},
		methods:{
			toggleNode(item){
				
				var interceptFun = function(data){
					if(data){
						data.forEach(function(m){
						
							if(m.id === item.id){
								m.expend = !m.expend;
								m.active = true;	
							}else{
								m.active = false;
							}
							if(m.children){
								interceptFun(m.children);
							}
						})
					}
				};
				interceptFun(this.treeData);

					
			},
			initData(){
				this.userData = this.treeData;
				var modifyDataFun = function(datas){
					if(datas){
						datas.forEach(function(m) {
							Vue.set(m,'expend',true);
							Vue.set(m,'active',false);
							if(m.children){
								modifyDataFun(m.children);
							}
						})
					}
				};
				modifyDataFun(this.userData)
			},
			intercept(list){

			},
			getNodeParent(nodeId){
				this.userData.forEach((li) => {
					var resultNode = li;
					var flag = false;
					if(li.children){
						li.children.forEach(function(m){
							if(m.id === nodeId){
								flag = true;
								return ;
							}
						})
					};
					if(flag){
						return li;
					}
				})
			},
		},
		props:['treeData'],
	}
</script>
<style>
	div.cus_tree li{
		text-align:left;
		list-style:none;
		cursor:pointer;

	}
	div.cus_tree .cus_tree_content:hover{
		background:#647af6;
		color:white
	}
	div.cus_tree .cus_tree_content.active{
		background:#ddd
	}

</style>