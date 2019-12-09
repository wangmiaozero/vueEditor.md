<template>
  <div>
    <div class="editormd"
         :id="editorId">
      <textarea class="editormd-markdown-textarea"
                name="$id-markdown-doc"></textarea>

      <!-- html textarea 需要开启配置项 saveHTMLToTextarea == true -->
      <textarea class="editormd-html-textarea"
                name="$id-html-code"></textarea>
    </div>
    <button style="width:100px;height:50px;font-size:20px;"
            @click="getValue">
      get-value
    </button>
  </div>
</template>
<script>
import $ from 'jquery'
import uuid from 'uuid/v4'
import { defaultConfig } from './config'
export default {
  name: 'MarkdownEditor',
  props: {
    /**
     * editormd挂载元素的id
     */
    editorId: {
      type: String,
      default: uuid()
    },
    /**
     * editormd的选项对象
     */
    options: {
      type: Object
    },
    initData: {
      'type': String
    },
    /*
     * 编辑器配置
     */
    config: {
      type: String
    },
    /*
     *  内容改变时回调，返回（html, markdown, text
     */
    onchange: {
      type: Function
    },
    data: function () {
      return {
        //最终生成的编辑器
        editor: null,
        timer: null,
        doc: {},
        jsLoadOver: false
      }
    }
  },
  mounted () {
    let vm = this
    let docId = vm.$router.currentRoute.name
    //加载editormd
    this.requireEditor(function () {
      vm.editor = editormd(vm.editorId, defaultConfig)
    })
  },
  methods: {
    getConfig: function () {
      return { ...defaultConfig, ...this.config }
    },
    /**
     * 异步加载editormd
     * callback 成功后的回调函数
     */
    requireEditor (callback) {
      let vm = this
      //设置全局变量，因为editormd依赖jquery
      window.$ = window.jQuery = $
      //异步加载并执行
      $.getScript('/lib/editor.md/editormd.min.js', function (script) {
        callback()
      })
      //加载css
      $('head').append(
        $('<link rel="stylesheet" type="text/css" />').attr(
          'href',
          '/lib/editor.md/css/editormd.min.css'
        )
      )
    },
    getValue () {
      let vm = this
      let html = this.editor.getPreviewedHTML()
      let markdown = this.editor.getMarkdown()
      console.log(this.getConfig())
      let gethtml = this.editor.getHTML()
      console.log('%c this is a message', 'color:#0f0;', '获取 Markdown 源码:', markdown)
      console.log('%c this is a message', 'color:#0f0;', '获取 Textarea 保存的 HTML 源码:', gethtml)
      console.log('%c this is a message', 'color:#0f0;', '获取预览窗口里的 HTML，在开启 watch 且没有开启 saveHTMLToTextarea 时使用:', html)
    }
  }
}
</script>
<style></style>
