<template>
  <section class="container">
    <client-only>
      <quill-editor
        ref="editor"
        v-model="content"
        :options="editorOption"
        @blur="onEditorBlur($event)"
        @focus="onEditorFocus($event)"
        @ready="onEditorReady($event)"
      />
    </client-only>
  </section>
</template>

<script>
// import Quill from "quill";
// import hljs from "highlight.js";
// import { ImageResize } from "quill-image-resize-module";
// Quill.register("modules/imageResize", ImageResize);
// import "highlight.js/styles/darcula.css";
// hljs.configure({
//   languages: [
//     "java",
//     "dart",
//     "javascript",
//     "ruby",
//     "python",
//     "rust",
//     "css",
//     "php",
//     "html",
//   ],
// });
export default {
  name: "quill-example-nuxt",
  data() {
    return {
      content: "<p>I am Example</p>",
      editorOption: {
        // Some Quill options...
        theme: "snow",
        modules: {
          // imageResize: {
          //   displaySize: true,
          // },
          // syntax: {
          //   highlight: (text) => hljs.highlightAuto(text).value,
          // },
          toolbar: [
            ["bold", "italic", "underline", "strike"],
            ["blockquote", "code-block"],
            [{ header: 1 }, { header: 2 }],
            [{ list: "ordered" }, { list: "bullet" }],
            [{ script: "sub" }, { script: "super" }],
            [{ indent: "-1" }, { indent: "+1" }],
            [{ direction: "rtl" }],
            [{ size: ["small", false, "large", "huge"] }],
            [{ header: [1, 2, 3, 4, 5, 6, false] }],
            [{ color: ["red"] }, { background: ["red"] }],
            [{ font: [] }],
            [{ align: [] }],
            ["clean"],
            ["link", "image", "video"],
          ],
        },
      },
    };
  },
  mounted() {
    // console.log('App inited, the Quill instance object is:', this.$refs.editor.quill)
    // setTimeout(() => {
    //   this.content = 'I was changed!'
    // }, 3000)[]
    if (process.client) {
      Quill = require("quill");
    }
  },
  methods: {
    onEditorBlur(editor) {
      console.log("editor blur!", editor);
    },
    onEditorFocus(editor) {
      console.log("editor focus!", editor);
    },
    onEditorReady(editor) {
      console.log("editor ready!", editor);
    },
  },
};
</script>

<style lang="scss" scoped>
.container {
  width: 60%;
  margin: 0 auto;
  padding: 50px 0;
  .quill-editor {
    min-height: 200px;
    max-height: 400px;
    overflow-y: auto;
  }
}
</style>