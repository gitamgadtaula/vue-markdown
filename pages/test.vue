<template>
  <div style="background-color: ">
    <quill-editor
      ref="quillEditor"
      class="editor"
      v-model="content"
      :options="editorOption"
      @blur="onEditorBlur($event)"
      @focus="onEditorFocus($event)"
      @ready="onEditorReady($event)"
    />

    <!-- <div class="content ql-editor" v-html="content"></div> -->
  </div>
</template>
<script>
import QuillEditor from "@/components/MarkDown";
export default {
  components: {
    QuillEditor,
  },
  data() {
    return {
      tab: null,
      inputText: "",
      editorOption: {
        theme: "snow",
      },
      content: "<h2>I'm vue-quill-editor example</h2>",
    };
  },
  methods: {
    handleChange(value) {
      this.inputText = value;
    },
    onEditorBlur(quill) {
      console.log("editor blur!", quill);
    },
    onEditorFocus(quill) {
      console.log("editor focus!", quill);
    },
    onEditorReady(quill) {
      console.log("editor ready!", quill);
    },
  },

  computed: {
    editor() {
      return this.$refs.quillEditor.quill;
    },
    compiledMarkdown() {
      return marked(this.inputText, { sanitize: true });
    },
  },
  head() {},
};
</script>

<style>
.quill-editor {
  /* box-shadow: 0 0 0 0 rgba(0, 0, 0, 0.2), 0 0 0 0 rgba(0, 0, 0, 0.14),
    0 0 0 0 rgba(0, 0, 0, 0.12); */
  /* border: 1px solid red; */
  box-shadow: 0px 2px 4px -1px rgba(0, 0, 0, 0.2),
    0px 4px 5px 0px rgba(0, 0, 0, 0.14), 0px 1px 10px 0px rgba(0, 0, 0, 0.12);
}
.ql-active {
  background-color: #001 !important;
}
.ql-picker-options {
  background-color: #000;
}
.ql-toolbar .ql-stroke {
  fill: none;
  stroke: #fff;
  box-shadow: 0 0 0 0 rgba(0, 0, 0, 0.2), 0 0 0 0 rgba(0, 0, 0, 0.14),
    0 0 0 0 rgba(0, 0, 0, 0.12);
}

.ql-toolbar .ql-fill {
  fill: #fff;
  stroke: none;
}

.ql-toolbar .ql-picker {
  color: #fff;
}
</style>