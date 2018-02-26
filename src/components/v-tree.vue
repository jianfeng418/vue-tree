<template>
	<div class='cus_vtree_wrap' @click.capture='clickNodeWrap'>
			<v-tree-item :treeData='treeData' :clickFun='clickFun' :checkBox='checkBox':class='{tree_root_lonely:treeData.length === 1}' @toggleCheckBox="checkBoxFun"></v-tree-item>
	</div>
</template>

<script>
	import Vue from 'vue'
	export default{
		name:'v-tree',
		props:['treeData','clickFun','checkBox'],
		data(){
			return {
				userData:this.treeData
			}
		},
		mounted(){
			this.initData(true,true,true);
			console.log(this)
		},
		methods:{
			initData(expandInit,activeInit,checkedInit){
				var modifyDataFun = function(datas){
					if(datas){
						datas.forEach( (m,index)  => {
							if(expandInit){
								Vue.set(m,'expand',true);
							}
							if(activeInit){
								Vue.set(m,'active',false);

							}
							if(checkedInit){
								Vue.set(m,'checked',false);
								Vue.set(m,'partchecked',false);

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
				this.initData(false,true,false);
			},
			checkBoxFun(item){
				
			},
			getCheckedNodes(){
				var resultArr = [];
				var getCheckedNodesFun = (datas) => {
					if(datas){
						datas.forEach((m)=>{
						
							if(m.checked === true){
								resultArr.push(m);
							}
							if(m.children){
								getCheckedNodesFun(m.children);
							}
						})
					}
				};
				getCheckedNodesFun(this.treeData);
			
				return resultArr;
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
			template:'<ul class="cus_tree_ul" :class="{cus_tree_ulLine:(treeData && treeData.length)}"><li v-for="item in treeData"><div class="cus_item_content" @click="clickNode(item)" @click.cus="clickFun(JSON.stringify({id:item.id,text:item.text}))" :id= item.id   :class="{active:item.active}">'+
			'<span class="treeExpandBtn" @click.stop="toggleNode(item)" :class="{butopen:item.expand && item.children,btnclose:!item.expand && item.children,line: !item.last && !item.children,lastLine:item.last&&!item.children}"></span><span :class="item.icon"></span>'+
			'<span v-if="checkBox" @click="checkBoxClick(item)" class="cus_chekcbox" :class="{cus_chekcbox_checked:item.checked,cus_chekcbox_part_checked:item.partchecked}"></span>{{item.text}}</div>'+
			'<v-tree-item :treeData="item.children" :node="item"  v-if="item.expand" :clickFun="clickFun" :checkBox="checkBox" :class="{cus_checkbox_allchecked:item.checked}" @toggleCheckBox="checkBoxFun(item)" ></v-tree-item> </li></ul>',
		
			methods:{
				clickNode(item){
					item.active = true;
				},
				toggleNode(item){
					item.expand = !item.expand;
					item.active = true;
				},
				//checkbox勾选函数
				checkBoxClick(item){

					item.partchecked = false;
					item.checked = !item.checked;
					//设置子元素是否勾选
					var checkChildFun = (childrenDatas) => {
						childrenDatas.forEach((m) => {
							m.checked = item.checked;
							if(m.children){
								checkChildFun(m.children);
							}
						})
					}
					if(item.children){
						checkChildFun(item.children);
					}
					

					this.$emit('toggleCheckBox');
				},
				checkBoxFun(item){
					var checkedNum = 0;
					var partCheckedNum = 0;
					item.children.forEach((li) => {
					
							if(li.checked === true){
								checkedNum++;
							}else if(li.partchecked === true){
								partCheckedNum++;
							}
					})
					if(checkedNum === item.children.length){	//全选
						item.checked = true;
						item.partchecked = false;
					}else if(checkedNum > 0 || partCheckedNum > 0){	//部分勾选
						item.checked = false;
						item.partchecked = true;
					}else{	//没有勾选
						item.checked = false;
						item.partchecked = false;
					}

					this.$emit('toggleCheckBox')
				},
				
			},
			props:['treeData','clickFun','checkBox']
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

	.cus_item_content span.treeExpandBtn{
		display:inline-block;
		width:18px;
		height:18px;
		margin-right:6px;
		background-image:url('../images/zTreeStandard.png');
		
	}
	
	.cus_vtree_wrap>ul> li:first-child>.cus_item_content>span.treeExpandBtn.butopen{
		background-position:-92px 0px
	}
	li .cus_item_content span.treeExpandBtn.butopen{
		background-position:-92px -18px
	}
	li:last-child .cus_item_content span.treeExpandBtn.butopen{
		background-position:-92px -36px
	}
	ul.tree_root_lonely>li>.cus_item_content>span.treeExpandBtn.butopen{
		background-position:-92px -54px
	}

	.cus_vtree_wrap>ul> li:first-child>.cus_item_content>span.treeExpandBtn.btnclose{
		background-position:-74px -0px
	}
	li .cus_item_content span.treeExpandBtn.btnclose{
		background-position:-74px -18px
	}
	li:last-child .cus_item_content span.treeExpandBtn.btnclose{
		background-position:-74px -36px
	}
	ul.tree_root_lonely>li>.cus_item_content>span.treeExpandBtn.btnclose{
		background-position:-74px -54px
	}

	.cus_item_content span.treeExpandBtn.lastLine{
		background-position:-56px -36px
	}
	.cus_item_content span.treeExpandBtn.line{
		background-position:-56px -18px
	}

	.cus_item_content .cus_chekcbox{
		display:inline-block;
		width:14px;
		height:14px;
		margin-right:6px;
		background-image:url('../images/zTreeStandard.png');
		background-position:-0px -0px
	}
	.cus_item_content .cus_chekcbox.cus_chekcbox_checked{
		background-position:-14px -0px
	}
	.cus_item_content .cus_chekcbox.cus_chekcbox_part_checked{
		background-position:0px -42px
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