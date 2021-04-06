---
layout: post
title: 'Vue CLI 3+tinymce 5富文本编辑器整合'
subtitle: 'Vue CLI 3+tinymce 5富文本编辑器整合'
date: 2021-04-06
categories: 技术
cover: ''
tags: vue 富文本 tinymce
---

富文本编辑器里大佬们都说tinymce   NB！
### 插件安装
inymce官方提供了一个vue的组件tinymce-vue
如果有注册或购买过服务的话，直接通过组件配置api-key直接使用，懒的注册或者购买的直接下载tinymce
#### 安装tinymce
> npm install tinymce -S
##### 安装tinymce-vue
> npm install @tinymce/tinymce-vue -S

package.json
> "@tinymce/tinymce-vue": "^2.1.0",
"tinymce": "^5.0.11",

#### 下载中文语言包
tinymce提供了很多的语言包，这里我们下载中文语言包
地址：<a href="https://www.tiny.cloud/get-tiny/language-packages/"  target="_Blank">https://www.tiny.cloud/get-tiny/language-packages/</a>

![image](https://upload-images.jianshu.io/upload_images/12200279-b13b3fce890ff13b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###### 下载完后放到静态文件public目录下
1、在public目录下新建tinymce目录，将上面下载的语言包解压到该目录
2、在node_modules里面找到tinymce目录,将此目录下skins目录复制到public/tinymce里面


![image](https://upload-images.jianshu.io/upload_images/12200279-b50780638cc1ea1a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### tinymce使用

##### 封装成组件
```javascript
<template>
  <div class="tinymce-box">
    <editor
      v-model="myValue"
      :init="init"
      :disabled="disabled"
      @click="onClick">
    </editor>
  </div>
</template>

<script>
// 文档 http://tinymce.ax-z.cn/
// 引入组件
import tinymce from 'tinymce/tinymce' // tinymce默认hidden，不引入不显示
import Editor from '@tinymce/tinymce-vue'

// 引入富文本编辑器主题的js和css
import 'tinymce/skins/content/default/content.css'
import 'tinymce/themes/silver/theme.min.js'
import 'tinymce/icons/default/icons' // 解决了icons.js 报错Unexpected token '<'

// 编辑器插件plugins
// 更多插件参考：https://www.tiny.cloud/docs/plugins/
import 'tinymce/plugins/image'// 插入上传图片插件
import 'tinymce/plugins/media'// 插入视频插件
import 'tinymce/plugins/table'// 插入表格插件
import 'tinymce/plugins/lists'// 列表插件
import 'tinymce/plugins/wordcount'// 字数统计插件
import 'tinymce/plugins/link'
import 'tinymce/plugins/code'
import 'tinymce/plugins/preview'
import 'tinymce/plugins/fullscreen'
import 'tinymce/plugins/help'
export default {
  components: {
    Editor
  },
  name: 'Tinymce',
  props: {
    // 默认的富文本内容
    value: {
      type: String,
      default: ''
    },
    // 基本路径，默认为空根目录，如果你的项目发布后的地址为目录形式，
    // 即abc.com/tinymce，baseUrl需要配置成tinymce，不然发布后资源会找不到
    baseUrl: {
      type: String,
      default: window.location.origin ? window.location.origin : ''
    },
    // 禁用
    disabled: {
      type: Boolean,
      default: false
    },
    plugins: {
      type: [String, Array],
      default: 'link lists image code table wordcount media preview fullscreen help'
    },
    toolbar: {
      type: [String, Array],
      default: 'bold italic underline strikethrough | fontsizeselect | formatselect | forecolor backcolor | alignleft aligncenter alignright alignjustify | bullist numlist outdent indent blockquote | undo redo | link unlink code lists table image media | removeformat | fullscreen preview'
    }
  },
  data () {
    return {
      init: {
        language_url: `${this.baseUrl}/tinymce/langs/zh_CN.js`,
        language: 'zh_CN',
        skin_url: `${this.baseUrl}/tinymce/skins/ui/oxide`,
        // skin_url: 'tinymce/skins/ui/oxide-dark', // 暗色系
        convert_urls: false,
        height: 300,
        // content_css（为编辑区指定css文件）,加上就不显示字数统计了
        // content_css: `${this.baseUrl}tinymce/skins/content/default/content.css`,
        // （指定需加载的插件）
        plugins: this.plugins,
        toolbar: this.toolbar, // （自定义工具栏）

        statusbar: true, // 底部的状态栏
        menubar: 'file edit insert view format table tools help', // （1级菜单）最上方的菜单
        branding: false, // （隐藏右下角技术支持）水印“Powered by TinyMCE”
        // 此处为图片上传处理函数，这个直接用了base64的图片形式上传图片，
        // 如需ajax上传可参考https://www.tiny.cloud/docs/configure/file-image-upload/#images_upload_handler
        images_upload_handler: (blobInfo, success, failure) => {
          const img = 'data:image/jpeg;base64,' + blobInfo.base64()
          success(img)
          console.log(failure)
        }
      },
      myValue: this.value
    }
  },
  mounted () {
    console.log(window)
    tinymce.init({})
  },
  methods: {
    // 添加相关的事件，可用的事件参照文档=> https://github.com/tinymce/tinymce-vue => All available events
    // 需要什么事件可以自己增加
    onClick (e) {
      this.$emit('onClick', e, tinymce)
    },
    // 可以添加一些自己的自定义事件，如清空内容
    clear () {
      this.myValue = ''
    }
  },
  watch: {
    value (newValue) {
      this.myValue = newValue
    },
    myValue (newValue) {
      this.$emit('input', newValue)
    }
  }
}

</script>
<style scoped>

</style>


```
##### 组件引用
```javascript
<template>
  <div class="home">
    <tinymce
    id="myedit"
    ref="editor"
    v-model="msg"
    :disabled="disabled"
    @onClick="onClick"
    />
    <button @click="clear">清空内容</button>
    <button @click="disabled = true">禁用</button>
  </div>
</template>
<script>
import tinymce from '@/components/tinymce'

export default {
  name: 'tinymcedemo',
  components: {
    tinymce
  },
  data () {
    return {
      disabled: false,
      msg: 'Welcome to Use Tinymce Editor',
      isabled: false
    }
  },
  methods: {
    // 内容
    getContent () {
      console.log('内容', this.msg)
    },
    // 鼠标单击的事件
    onClick (e, editor) {
      console.log('Element clicked')
      console.log(e)
      console.log(editor)
    },
    // 清空内容
    clear () {
      console.log(this.$refs.editor)
      this.$refs.editor.clear()
    }
  }
}
</script>

```

![image](https://upload-images.jianshu.io/upload_images/12200279-8b876ad8b8652b41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

更详细看官方api


## tinymce高版本
#### 1、 ==报错==  icons.js 报错Unexpected token '<'

import 'tinymce/icons/default/icons' //解决了icons.js 报错Unexpected token '<'

#### 2、@tinymce/tinymce-vue版本过高报错（4.0.0以上版本是vue3.0的）
解决方法将@tinymce/tinymce-vue版本改成3.2.8

```
 warning  in ./node_modules/@tinymce/tinymce-vue/lib/es2015/main/ts/components/Editor.js

"export 'defineComponent' was not found in 'vue'

 warning  in ./node_modules/@tinymce/tinymce-vue/lib/es2015/main/ts/components/Editor.js

"export 'h' was not found in 'vue'

 warning  in ./node_modules/@tinymce/tinymce-vue/lib/es2015/main/ts/components/Editor.js

"export 'onActivated' was not found in 'vue'

 warning  in ./node_modules/@tinymce/tinymce-vue/lib/es2015/main/ts/components/Editor.js

"export 'onBeforeUnmount' was not found in 'vue'

 warning  in ./node_modules/@tinymce/tinymce-vue/lib/es2015/main/ts/components/Editor.js

"export 'onDeactivated' was not found in 'vue'

 warning  in ./node_modules/@tinymce/tinymce-vue/lib/es2015/main/ts/components/Editor.js

"export 'onMounted' was not found in 'vue'

 warning  in ./node_modules/@tinymce/tinymce-vue/lib/es2015/main/ts/components/Editor.js

"export 'ref' was not found in 'vue'

 warning  in ./node_modules/@tinymce/tinymce-vue/lib/es2015/main/ts/components/Editor.js

"export 'toRefs' was not found in 'vue'

 warning  in ./node_modules/@tinymce/tinymce-vue/lib/es2015/main/ts/components/Editor.js

"export 'watch' was not found in 'vue'
```
![image](https://upload-images.jianshu.io/upload_images/12200279-567c170c29d103a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

