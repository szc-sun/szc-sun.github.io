---
layout: post
title: 'wangEditor 富文本编辑器的使用'
subtitle: 'wangEditor 富文本编辑器的使用'
date: 2021-04-06
categories: 技术
cover: ''
tags: vue 富文本 wangEditor
---

### 写在前面
开源的富文本 wangEditor 去年有了团队了，看了他们的文章有了团队、QQ群、中文api，用着应该不错。

[wangEditor V4.0 探索以团队的形式做开源项目](https://juejin.cn/post/6882522512861691912 "wangEditor V4.0 探索以团队的形式做开源项目")
[wangEditor-v4----官网](https://www.wangeditor.com/ "wangEditor-v4----官网")
[wangEditor-v4----api文档](https://doc.wangeditor.com/ "wangEditor-v4----api文档")

### 浏览器兼容性
兼容主流 PC 浏览器，IE11+
不支持移动端和 ipad

### 简单说下使用方法

#### 1、引入方法一：NPM
npm 安装 `npm i wangeditor --save` ，几行代码即可创建一个编辑器。
```javascript
import E from 'wangeditor'
const editor = new E('#div1')
editor.create()
```
#### 2、引入方法二：CDN
```javascript
<script
  type="text/javascript"
  src="https://cdn.jsdelivr.net/npm/wangeditor@latest/dist/wangEditor.min.js"
></script>
<script type="text/javascript">
  const E = window.wangEditor
  const editor = new E("#div1")
  // 或者 const editor = new E(document.getElementById('div1'))
  editor.create()
</script>
```

### vue中使用--封装成组件
#### 1、组件 src\components\wangEditor\index.vue
```javascript
<template lang="html">
    <div id="div1" ref="div1">
        <p>欢迎使用 <b>wangEditorvue</b> 富文本编辑器</p>
    </div>
</template>
<script>
  import E from 'wangeditor'
  import uploadImg from '@/api/uploadImg'
  export default {
    name: 'wangEditor',
    data() {
      return {
        editor: null,
        info_: null
      }
    },
    model: {
      prop: 'value',
      event: 'change'
    },
    props: {
      value: {
        type: String,
        default: ''
      },
      isClear: {
        type: Boolean,
        default: false
      },
      config:{
        type: Object,
        default: ()=>{}
      }
    },
    watch: {
      isClear(val) {
        // 触发清除文本域内容
        if (val) {
          this.editor.txt.clear()
          this.info_ = null
        }
      },
      value: function(value) {
        if (value !== this.editor.txt.html()) {
          this.editor.txt.html(this.value)
        }
      }
      //value为编辑框输入的内容，这里监听了一下值，当父组件调用得时候，如果给value赋值了，子组件将会显示父组件赋给的值
    },
    mounted() {
		// 创建标签
		this.seteditor()
		// 重新设置编辑器内容
		this.editor.txt.html(this.value)
    },
	beforeDestroy() {
		// 调用销毁 API 对当前编辑器实例进行销毁
		this.editor.destroy()
		this.editor = null
	},
    methods: {
      seteditor() {
        this.editor = new E(this.$refs.div1)
        // 配置菜单栏，删减菜单，调整顺序
        this.editor.config.menus = [
          'head', // 标题
          'bold', // 粗体
          'fontSize', // 字号
          'fontName', // 字体
          'italic', // 斜体
          'underline', // 下划线
          'strikeThrough', // 删除线
          'foreColor', // 文字颜色
          'backColor', // 背景颜色
          'link', // 插入链接
          'list', // 列表
          'justify', // 对齐方式
          'quote', // 引用
          'emoticon', // 表情
          'image', // 插入图片
          'table', // 表格
          // 'video', // 插入视频
          'code', // 插入代码
          'undo', // 撤销
          'redo', // 重复
          'fullscreen' // 全屏
        ]
        // 自己实现上传图片
        this.editor.config.customUploadImg = (resultFiles, insertImgFn) => {
          // resultFiles 是 input 中选中的文件列表
          // insertImgFn 是获取图片 url 后，插入到编辑器的方法

          // 上传图片，返回结果，将图片插入到编辑器中
          for( let i = 0, len = resultFiles.length; i < len; i++){
            this.requestUpload(resultFiles[i], insertImgFn)
          }
        }
        this.editor.config.onchange = (newHtml) => {
          this.info_ = newHtml // 绑定当前逐渐地值
          this.$emit('change', this.info_) // 将内容同步到父组件中
        }
        Object.assign(this.editor.config, this.config)
        // 创建富文本编辑器
        this.editor.create()
      },
      // 自定义上传
      async requestUpload(file, insertImgFn){
		var formData = new FormData();
		formData.append("file", file);
        try {
          	let res = await uploadImg(formData)
			if (res.code === 200) {;
				// 上传图片，返回结果，将图片插入到编辑器中
				insertImgFn(res.data.url)
			}
        } catch (error) {}
      }
    }
  }
</script>
<style lang="css">
  .editor {
    width: 100%;
    margin: 0 auto;
    position: relative;
    z-index: 0;
  }
  .toolbar {
    border: 1px solid #ccc;
  }
  .text {
    border: 1px solid #ccc;
    min-height: 500px;
  }
</style>
```
#### 2、调用
```javascript
<template>
  <div>
    <wangEditor v-model="content" :isClear="false"></wangEditor>
  </div>
</template>

<script>
import wangEditor from '@/components/wangEditor'
export default {
  components: { wangEditor },
  data () {
    return {
      content: '😄'
    }
  }
}
</script>

<style>

</style>
```

![wangEditor](https://upload-images.jianshu.io/upload_images/12200279-cb01595ec26b540a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "wangEditor")

#### 3、说明 ：
> editor.config.customUploadImg直接重写了上传事件，自己实现上传图片，适应比较复杂的上传情况，例如多个接口异步问题。简单的接口调用时配置下上传参数就可以了

```javascript
// 配置 server 接口地址
editor.config.uploadImgServer = '/upload-img'

// 默认限制图片大小是 5M ，可以自己修改。
editor.config.uploadImgMaxSize = 2 * 1024 * 1024 // 2M

// 限制类型
editor.config.uploadImgAccept = ['jpg', 'jpeg', 'png', 'gif', 'bmp']

// 默认为 100 张，需要限制可自己配置。
editor.config.uploadImgMaxLength = 5 // 一次最多上传 5 个图片

// 自定义上传参数
editor.config.uploadImgParams = {
    token: 'xxxxx',
    x: 100
}

// 如果需要将参数拼接到 url 中，可再加上如下配置。
editor.config.uploadImgParamsWithUrl = true

// 可自定义 filename
editor.config.uploadFileName = 'your-custom-fileName'
// 上传图片时添加 http 请求的 header 。
editor.config.uploadImgHeaders = {
    Accept: 'text/x-json',
    a: 100,
}

//跨域上传中如果需要传递 cookie 需设置 withCredentials 。
editor.config.withCredentials = true

// timeout 即上传接口等待的最大时间，默认是 10 秒钟，可以自己修改。
editor.config.uploadImgTimeout = 5 * 1000

//回调函数
editor.config.uploadImgHooks = {
    // 上传图片之前
    before: function(xhr) {
        console.log(xhr)

        // 可阻止图片上传
        return {
            prevent: true,
            msg: '需要提示给用户的错误信息'
        }
    },
    // 图片上传并返回了结果，图片插入已成功
    success: function(xhr) {
        console.log('success', xhr)
    },
    // 图片上传并返回了结果，但图片插入时出错了
    fail: function(xhr, editor, resData) {
        console.log('fail', resData)
    },
    // 上传图片出错，一般为 http 请求的错误
    error: function(xhr, editor, resData) {
        console.log('error', xhr, resData)
    },
    // 上传图片超时
    timeout: function(xhr) {
        console.log('timeout')
    },
    // 图片上传并返回了结果，想要自己把图片插入到编辑器中
    // 例如服务器端返回的不是 { errno: 0, data: [...] } 这种格式，可使用 customInsert
    customInsert: function(insertImgFn, result) {
        // result 即服务端返回的接口
        console.log('customInsert', result)

        // insertImgFn 可把图片插入到编辑器，传入图片 src ，执行函数即可
        insertImgFn(result.data[0])
    }
}

```

### 其他

更多配置，查看官方文档，写的很详细。 [wangEditor-v4----api文档](https://doc.wangeditor.com/ "wangEditor-v4----api文档")

### 交流
##### 加入 QQ 群
- 164999061
- 710646022（人已满）
- 901247714（人已满）

##### 提交 bug 或建议
[github issues 提交问题](https://github.com/wangeditor-team/wangeditor/issues "github issues 提交问题")




