# ## 移动端vue组件-级联选择器

[查看GitHub仓库文档详情](https://github.com/Flying-Eagle2/flyingd-cascader.git)

` https://github.com/Flying-Eagle2/flyingd-cascader.git`

### 安装

` cnpm install flyingd-cascader ` 

### CDN

**在页面上引入 js 和 css 文件即可开始使用 **

` 等我....上代码`

### 演示代码

### html

``` html
<flyingd-cascader  v-model="cascaderOption"  :visible.sync="cascaderVisible"  :options="options"  :cascadervalue="cascadervalue"  :cascaderlabel="cascaderlabel"  :placeholder="cascaderPlaceholder"
:cascaderchildren="cascaderchildren"  popper-class='flyingdwang-cascader'   @change="cascaderFun"  ></flyingd-cascader>
```

### js

<script>
import flyingdCascader from 'flyingd-cascader';
export default {
  name: 'demo',
  components:{
    flyingdCascader
  },
  data () {
    return {
      options: [
         {
          value: 'ziyuan',
          label: '资源',
              children: [{
                  value: 'axure',
                  label: 'Axure Components'
              }, {
                  value: 'sketch',
                  label: 'Sketch Templates',
                disabled:true,
              }, {
                  value: 'jiaohu',
                  label: '组件交互文档'
              }]
          }
      ],
      cascadervalue:'value',
      cascaderlabel:'label',
      cascaderchildren:'children',
      cascaderOption:["ziyuan","axure"],
      cascaderPlaceholder:['选择内容','请选择参数'],
      cascaderVisible:false,
    }
  },
  methods: {
    cascaderFun:function (option){
        console.log(option,this.cascaderOption);
    },
  },
}
</script>






### 参数说明 :

| 参数                | 说明                                    | 类型    | 默认值   |
| ------------------- | --------------------------------------- | ------- | -------- |
| visible             | 是否显示 cascader                       | boolean | false    |
| options             | 可选项数据源，键名可通过 props 属性配置 | array   | -        |
| value               | 选中项绑定值                            | array   | -        |
| cascadervalue       | 指定选项的值为选项对象的某个属性值      | string  | value    |
| cascaderlabel       | 指定选项标签为选项对象的某个属性值      | string  | label    |
| cascaderchildren    | 指定选项的子选项为选项对象的某个属性值  | string  | children |
| disabled            | 指定选项的禁用为选项对象的某个属性值    | Boolean | false    |
| popper-class        | 自定义浮层类名                          | string  | -        |
| cascaderPlaceholder | 占位文本                                | array   | -        |

### props

| 参数     | 说明       | 类型    | 默认值 |
| -------- | ---------- | ------- | ------ |
| value    | 对象属性值 | string  | -      |
| label    | 对象属性值 | string  | -      |
| children | 子选项     | Array   | -      |
| disabled | 是否禁用   | Boolean | -      |

### Events

| 事件名称 | 说明                                           | 回调参数 |
| -------- | ---------------------------------------------- | -------- |
| change   | 当绑定值变化时触发的事件 ==>  function(option) | 所有值   |
| close    | 关闭弹窗回调                                   |          |