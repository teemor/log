# 安装
### 1. 确定电脑已装node和npm
```
 node -v
 ```
 ```
 npm -v
```
出现版本号则说明电脑已经安装好node和npm
### 2. 创建一个基于webpack的项目

```
vue init webpack dailyrecord
```
### 3. 在项目里安装依赖
```
cd dailyrecord
```
```
npm install
```
### 4. 运行
```
npm run dev
```
这样你的vue就跑起来啦
# 配置路由
为了动态渲染各个页面的组件，这个是必须的，这些都在router文件夹里的index.js配置好，新建dailyrecord文件夹在下面新建index.vue
### 1. 导入页面
```
import Index from '@/dailyrecord/Index'
```
### 2. 配置全局路径
```
path:'\'
component:'Index'
```
# Element-UI
使用element-ui编写页面样式，具体操作参照官网
### 1. 安装
```
npm i element-ui -s
```
### 2. 全局配置
找到main.js文件
```
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
```
# 页面
参考element-ui官网做出一些假数据写出index页
# 绑定table数据
关于接口传值必须要知道的，后台传的值是数组还是对象。一定要确定好，不然后期很容易传错或者接收错的值，导致数据传输不正确，不显示数据
### 1. 安装并全局导入axios
```
npm i axios
```
```
Vue.prototype.$axios = axios
```
axios 并不属于 vue的插件
框架与Http本身没有必然的归属性关系，是要实现了Http标准，都可以在任意框架中使用
想要使用this调用的话，可以绑定到vue.prototype上
### 2. 获取接口，绑定数据
```
loadlist(){
    this.$axios.get('')
    .then(res=>{
        this.dateb=res.data
    })
}
```
# 新增日志数据
### 1. 新建新增日志组件addlog,并且在父组件里调用
非空验证（详情看elementUi form表单验证）
```
<add-log .....</add-log>
```
```
import AddLog from '@/dailyrecord/AddLog'
```
```
  components: {
    AddLog
  }
```
### 2. 在子组件里method里写新增方法 addlogone()
新增事件：submit-btn-click，取消事件：cancel-dialog一起传到父组件，在父组件里写方法操作
```
 @click="$emit('cancel-dialog')"  子
```
```
@cancel-dialog="addmodel=false"   父
```
```
@submit-btn="addlogl"   父
```
```
  this.$emit('submit-btn', this.formdata)  子
```
1. 新增里面验证非空通过就提交model到父组件。
```
  addlogone: function () {
      this.$refs.forma.validate((val) => {
        if (val) {
          this.$emit('submit-btn', this.formdata)
        }
      })
    }
```
2. 父组件写方法获取api并传入model,关闭dialog,清空表单（在子组件写清空方法然后父组件调用，ref，refs）
```
    addlogl (formdata) {
      this.$axios.post('http://qianjia.space:8000/logs', formdata)
        .then((res) => {
          this.addlog = false
          this.loadlist()
          this.$refs.form.reset()
        })
    }
```
3. 赋值到页面中,再加载一遍页面
```
this.loadlist()
```
# 删除日志
### 写删除方法
1. 用element组件的删除方法，传入索引和数据
```
@click.native.prevent="deleteRow(scope.$index, datab)"
```
2. 写删除方法接收索引，获取当前数据行的Id，传入，然后调用api方法删除数据，最后删除单元格
```
detleteRow(index,rows){
    var id = rows[index]._id.$oid
    ....
}
```
# 修改日志
1. 点击弹出表单，赋值到子组件
:data="formdata"
```
  edit (row) {
      this.formdata = row
      this.editlog = true
    }
```
```
<edit :data="formdata"></edit>
```
2. 子组件接收父组件传值
props,mount初始化数据，watch监听数据变化<不监听的话，文本框的值不会变>
```
  props: {
    data: Object
  }
```
```
  mounted () {
    if (this.data && Object.keys(this.data).length) {
      this.setlog()
    } else {
      this.formdata = this.initModel()
    }
  }
```
```
  watch: {
    data: function () {
      this.setlog()
    }
  }
```
3. 点击提交edita方法将数据传到父组件
```
 @click="edita"
```
```
   edita: function () {
      this.$refs.hh.validate(valid => {
        if (valid) {
          console.log(this.formdata)
          this.$emit('edita', this.formdata)
        }
      })
    }
```
4. 父组件页面接收@edita，并定义方法提交数据到修改的接口（需要看数据是什么格式的再决定传入），关闭弹窗
```
    edita (model) {
      let json = {
        'summary': this.formdata.summary,
        'content': this.formdata.content,
        'user': this.formdata.user
      }
      this.$axios.put('http://qianjia.space:8000/logs' + '/' + model._id.$oid, json)
      .then((res) => {
        this.editlog = false
      })
    }
```
5. 合并addlog和editlog
```
  mounted () {
    if (this.data && Object.keys(this.data).length) {
      this.setlog()
    } else {
      this.formdata = this.initModel()
    }
  },
  watch: {
    data: function () {
      this.setlog()
    }
  
```
挂载的时候因为add走了Init
edit走了setlog，点击确定的时候，都传入了修改的Model，然后到父组件的时候用不同的方法传递一下api接口就行了