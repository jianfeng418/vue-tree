<template>
	<div class='cus_vtree_wrap' @click.capture='clickNodeWrap'>
		
			<v-tree-item :treeData='treeData' :clickFun='clickFun' :class='{tree_root_lonely:treeData.length === 1}' ></v-tree-item>
		
	</div>
</template>

<script>
	import Vue from 'vue'
	export default{
		name:'v-tree',
		props:['treeData','clickFun'],
		data(){
			return {
				userData:this.treeData
			}
		},
		mounted(){
			this.initData(true,true);
			console.log(this)
		},
		methods:{
			initData(expendInit,activeInit){
				var modifyDataFun = function(datas){
					if(datas){
						datas.forEach( (m,index)  => {
							if(expendInit){
								Vue.set(m,'expend',true);
							}
							if(activeInit){
								Vue.set(m,'active',false);
							}
							if(index === datas.length - 1){
								Vue.set(m,'last',true);
							}
							if(m.children){
								modifyDataFun(m.children);
							}
						})
					}
				}
				modifyDataFun(this.userData)
			},
			clickNodeWrap(){
				this.initData(false,true);
			},
			getSelectedNode(){
				var resultNode = null;
				var getSelectedFun = (datas) => {
					if(datas){
						datas.forEach((m) => {
							if(m.active === true){
								resultNode = m;
								return;
							}
							if(m.children){
								getSelectedFun(m.children)
							}
						})
					}
				};
				getSelectedFun(this.treeData);
				return resultNode;
			}
		},
		components:{'v-tree-item':{
			name:'v-tree-item',
			template:'<ul class="cus_tree_ul" :class="{cus_tree_ulLine:(treeData && treeData.length)}"><li v-for="item in treeData"><div class="cus_item_content" @click="clickNode(item)" @click.cus="clickFun" :id= item.id :class="{active:item.active}">'+
			'<span class="treeExpendBtn" @click.stop="toggleNode(item)" :class="{butopen:item.expend && item.children,btnclose:!item.expend && item.children,line: !item.last && !item.children,lastLine:item.last&&!item.children}"></span><span :class="item.icon"></span>{{item.text}} </div>'+
			'<v-tree-item :treeData="item.children" v-if="item.expend" :clickFun="clickFun"></v-tree-item> </li></ul>',

			methods:{
				clickNode(item){
					
					item.active = true;
					
				},
				toggleNode(item){
					item.expend = !item.expend;
					item.active = true;
				}
			},
			props:['treeData','clickFun']

		}}

	}
</script>

<style>
	.cus_vtree_wrap ul:first-child{
		background-image:none;
	}
	 ul.cus_tree_ul{
		padding-left:16px;
		background-image:url('../images/index.gif');
		background-position:4px 10px;
		background-repeat:repeat-y;
	}
	li:last-child ul.cus_tree_ul:last-child{
		background-image:none;
	}

	ul.cus_tree_ul li{
		text-align:left;
		cursor:pointer;
		list-style:none;
		
	}

	.cus_item_content span.treeExpendBtn{
		display:inline-block;
		width:18px;
		height:18px;
		margin-right:6px;
		background-image:url('../images/zTreeStandard.png');
		
	}
	
	li:first-child .cus_item_content span.treeExpendBtn.butopen{
		background-position:-92px 0px
	}
	li .cus_item_content span.treeExpendBtn.butopen{
		background-position:-92px -18px
	}
	li:last-child .cus_item_content span.treeExpendBtn.butopen{
		background-position:-92px -36px
	}
	ul.tree_root_lonely>li>.cus_item_content>span.treeExpendBtn.butopen{
		background-position:-92px -54px
	}

	li:first-child .cus_item_content span.treeExpendBtn.btnclose{
		background-position:-74px -0px
	}
	li .cus_item_content span.treeExpendBtn.btnclose{
		background-position:-74px -18px
	}
	li:last-child .cus_item_content span.treeExpendBtn.btnclose{
		background-position:-74px -36px
	}
	ul.tree_root_lonely>li>.cus_item_content>span.treeExpendBtn.btnclose{
		background-position:-74px -54px
	}

	.cus_item_content span.treeExpendBtn.lastLine{
		background-position:-56px -36px
	}
	.cus_item_content span.treeExpendBtn.line{
		background-position:-56px -18px
	}


	ul.cus_tree_ul li .cus_item_content{
		padding:4px;
	}
	ul.cus_tree_ul li .cus_item_content:hover{
		background:#8f83e1;
		color:white
	}
	ul.cus_tree_ul li .cus_item_content.active{
		background:#7663f8;
		color:white;
	}

  .folder{
    display:inline-block;
    width:16px;
    height:16px;
    margin-right:6px;
    background-image:url('../images/zTreeStandard.png');
    background-position:-110px -0px
  }
  .file{
    display:inline-block;
    width:18px;
    height:18px;
    margin-right:6px;
    background-image:url('../images/zTreeStandard.png');
    background-position:-110px -30px
  }
</style>