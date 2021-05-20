
/**
 *  级联选择器
 * version 1.0.0
 * dete 2020-07-26
 *  author : flyingd
 */ 
<template>
     <div class="flyingd-dialog" v-show="visible" @click="handleClose">
        <div class="flyingd-cascader flex-column" @click="prevent"  id="flyingd-cascader" :class="$attrs['popper-class']">
            <div class="cascader-nav">
                <template v-for="(item, index) in (selectedLabel.length>placeholder.length?selectedLabel:placeholder.length?placeholder:1)">
                    <span @click=" itemStatus(index) ? sideActiveChange(index) : null"  :key="index" class="text1" 
                        :class="(sideActive==index?'active ': ' ')+( itemStatus(index) ? '' : 'is-disabled')" >
                        {{selectedLabel[index]||placeholder[index]||'请选择'}}
                    </span>
                </template>
                <div @click="handleClose" class="handleClose iconfont icon-guanbi"></div>
            </div>
            <div class="cascader-body flex-auto">
                <div class="cascader-body-box">
                    <div v-for="(item, index) in processingData()" :key="index"  :class="(selectedLabel.indexOf(item[cascaderlabel])>-1?'hovers':'')+' cascader-menu__item '+(item.disabled?'is-disabled':'')" 
                        @click="!item.disabled?cascaderChange(item,index):null">{{item[cascaderlabel]}}</div>
                    <div v-if="(processingData())<1" class="cascader-menu__item is-disabled">请设置参数</div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
     data(){
        return {
            selectedLabel:[],
            tableTagParent:[],
            tableTag:[],
            sideActive:0,
            preserve:[],
        }
    },
    props:{
        options:{
            type:Array,
            default:Array,
        },
        cascadervalue:{
            type:String,
            default:'value',
        },
        cascaderlabel:{
            type:String,
            default:'label',
        },
        cascaderchildren:{
            type:String,
            default:'children',
        },
        placeholder:{
            type:Array,
            default:Array,
        },
        visible:{
            type:Boolean,
            default:false,
        },
        /** v-model */
        value: {
            type:Array,
            default:Array,
        },
    },
    created(){
    },
    methods:{
        sideActiveChange(index){
            this.sideActive = index;
            if(!this.tableTagParent) return;
            // console.log(this.tableTagParent,index);
            this.tableTag = this.tableTagParent.slice(0,index);
        },
        /** 内容是否可选状态 */
        itemStatus(index){
            let { tableTagParent } = this;
            if((!tableTagParent&&!index)||(tableTagParent&&tableTagParent.length >= index)){
                return true;
            }else {
                return false;
            }
        },
        cascaderChange(item,index){
            this.cascaderSelectChange((JSON.parse(JSON.stringify(this.tableTag))).concat(index),item);
            /** 没有子级就关闭弹窗 */
            if(!item[this.cascaderchildren]) return (setTimeout(()=>{ this.handleClose() },200));
            setTimeout(()=>{ 
                this.tableTag.push(index);
                this.tableTagParent = this.tableTag;
                this.sideActive++;
            },200)
        },
        /** 处理选中的值 */
        cascaderSelectChange(tableTag,item){
            /** 选中的值value值 */
            let selectValue = [];
            /** 选中的值label值 */
            let selectedLabel = [];
            const { length }  = tableTag;
            if(length){
                let selectItem = null,
                    children = null,
                    value = null,
                    label = null;
                
                for(var i=0;i<length;i++){
                    if(i==0&&!selectItem){
                        children  = this.options[tableTag[i]][this.cascaderchildren] ;
                        value = this.options[tableTag[i]][this.cascadervalue] ;
                        label = this.options[tableTag[i]][this.cascaderlabel] ;
                    }else {
                        children = selectItem[tableTag[i]][this.cascaderchildren] ;
                        value = selectItem[tableTag[i]][this.cascadervalue] ;
                        label = selectItem[tableTag[i]][this.cascaderlabel] ;
                    }
                    selectValue.push(value);
                    selectedLabel.push(label);
                    if(children){
                        selectItem = children ;
                    }
                }
            }
            item[this.cascaderchildren]&&selectedLabel.push('');
            this.selectedLabel = selectedLabel;
            /** 传值给父组件v-model绑定的字段 */
            this.$emit('input',selectValue);
            /** 保存选中值用于判断 */
            this.preserve = selectValue;
            /** 判断是否接收修改参数 */
            this.$emit('change',{selectValue,selectedLabel});
        },
        /** 初始化默认选中值 */
        initSelected(){
            /** 清空之前选中的值 */
            this.tableTag = [];
            let { length } = this.value;
            /** 选中的值label值 */
            let selectedLabel = [];
            if(length){
                let item = null,children = null,value = null,label = null;
                for(var i=0;i<length;i++){
                    for (let index = 0; index < (item&&item.length||this.options.length); index++) {
                        /** 取其中对应的参数 */
                        value = item ? item[index][this.cascadervalue] : this.options[index][this.cascadervalue];
                        if(value==this.value[i]){
                            label = item ? item[index][this.cascaderlabel] : this.options[index][this.cascaderlabel];
                            selectedLabel.push(label)
                            children  = item ? item[index][this.cascaderchildren] : this.options[index][this.cascaderchildren] ;
                            if(children){
                                item = children ;
                                /** 添加索引 */
                                this.tableTag.push(index);
                            }
                            /** 判断是否有子级 */
                            if(i+1==length){
                                children&&selectedLabel.push('')
                            }
                            break;
                        }
                    }
                }
                // console.log(selectedLabel);
                /** 更改对应的label值 */
                this.selectedLabel = selectedLabel;
                /** 保存索引值 */
                this.tableTagParent = this.tableTag;
                /** 改变初始选中值 */
                this.sideActive = selectedLabel.length-1;
            }
        },
        /** 数据处理 */
        processingData(){
            let { length }  = this.tableTag;
            if(length){
                let item = null ,children = null;
                for(var i=0;i<length;i++){
                    if(i==0&&!item){
                        children  = this.options[this.tableTag[i]][this.cascaderchildren] ;
                    }else {
                        children = item[this.tableTag[i]][this.cascaderchildren] ;
                    }
                    if(children){
                        item = children ;
                    }
                }
                return item;
            }else {
              return  this.options
            }
        },
        handleClose(){
            document.getElementById("flyingd-cascader").classList.remove("active");
            setTimeout(()=>{
                this.$emit('close');
                this.$emit('update:visible', false);
            },1000)
        },
        /** 阻止事件冒泡 */
        prevent(e){
            e.stopPropagation();
            return false;
        }
        
    },
    watch: {
        visible:function(item){
            if(!item) return;
            /** 绑定值是否一样 */
            (JSON.stringify(this.preserve)!=JSON.stringify(this.value))&&(this.initSelected(),this.preserve = this.value);
            setTimeout(()=>{
                document.getElementById("flyingd-cascader").classList.add("active");
            },0)

        },
    },
}
</script>

<style scoped>
@import '../assets/iconfont/iconfont.css' ;
/*定义滚动条高宽及背景 高宽分别对应横竖滚动条的尺寸*/
.cascader-body-box::-webkit-scrollbar {
  width: 0.12rem;
  height: 0.12rem;
  background-color: #F5F5F5;
}
/*定义滚动条轨道 内阴影+圆角*/
.cascader-body-box::-webkit-scrollbar-track {
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  border-radius: 10px;
  background-color: #F5F5F5;
}
/*定义滑块 内阴影+圆角*/
.cascader-body-box::-webkit-scrollbar-thumb {
  border-radius: 10px;
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  background-color: #555;
}
.text1 {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
}
/** 对话框 */
.flyingd-dialog {
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%,-50%);
  height: 100%;
  width: 100%;
  margin: 0 auto;
  max-width: 700px;
  min-width: 320px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 99;
}
.flex-column {
  display: flex;
  flex-flow: column;
}
.flex-auto {
  overflow: hidden;
  flex: 1;
}
.flex-auto > div {
  height: 100%;
  overflow: auto;
  overflow-x: hidden;
}
/** 多级选择框 */
.flyingd-cascader {
  position: absolute;
  left: 0;
  bottom: -65%;
  height: 65%;
  background-color: #fff;
  width: 100%;
  border-top-right-radius: 0.2rem;
  border-top-left-radius: 0.2rem;
  transition: all 1s;
  opacity: 0.5;
}
.flyingd-cascader.active {
  bottom: 0;
  opacity: 1;
}
.cascader-nav {
  height: 0.7rem;
  display: flex;
  justify-content: space-between;
  padding: 0 0.35rem;
  align-items: center;
}
.cascader-nav  span {
  position: relative;
  padding: 0 0.1rem 0.13rem;
  max-width: 2.2rem;
  min-width: 0.8rem;
  cursor: pointer;
}
.cascader-nav  .active {
  color: #5a8eca;
}

.cascader-nav .active::before {
  content: ' ';
  position: absolute;
  width: 0.8rem;
  height: 0.07rem;
  background-color: #ec0a0a;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  border-radius: 50vh;
}
.cascader-nav .is-disabled{
    color: #c0c4cc;
    cursor: not-allowed;
}
.cascader-body .cascader-menu__item {
  width: 100%;
  font-size: 0.32rem;
  height: 0.7rem;
  line-height: 0.7rem;
  padding: 0 0.5rem;
  border-bottom: 1px solid #e0e0e0;
  cursor: pointer;
}
.cascader-body .cascader-menu__item.hovers {
  background: #e7ebec;
  color: #409EFF;
}
.cascader-body .is-disabled {
  color: #c0c4cc;
  cursor: not-allowed;
}
.handleClose {
  width: 0.35rem;
  height: 0.35rem;
  display: flex;
  justify-items: center;
  align-items: center;
  align-items: center;
  font-size: .35rem;
  color: #909399;
  line-height: .35rem;
  cursor: pointer;

}

</style>