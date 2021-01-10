<template>
  <div style="background-color: ">
    <client-only>
      <quill-editor
        ref="quillEditor"
        class="editor"
        v-model="content"
        :options="editorOption"
        :atPeople="atPeople"
        @at-people="atPeopleSelect"
        @blur="onEditorBlur($event)"
        @focus="onEditorFocus($event)"
        @ready="onEditorReady($event)"
      />

      <!-- <div class="content ql-editor" v-html="content">
      Use this to display the content wherever you want
    </div> -->
    </client-only>
  </div>
</template>
<script>
const atPeople = [
  { id: 1, name: "gitam" },
  { id: 2, name: "merry" },
  { id: 3, name: "box" },
  { id: 4, name: "Carry" },
  { id: 5, name: "Jony" },
  { id: 6, name: "merry" },
  { id: 7, name: "lala" },
  { id: 8, name: "xiaoxiong" },
  { id: 9, name: "herry" },
  { id: 10, name: "jerry" },
  { id: 11, name: "jackson" },
];
// import QuillEditor from "@/components/MarkDown";
export default {
  name: "",
  components: {
    // QuillEditor,
    QuillEditor: () => import("@/components/MarkDown"),
  },
  data() {
    return {
      atPeople,
      tab: null,
      inputText: "",
      editorOption: {
        theme: "snow",
      },
      content: "",
    };
  },
  methods: {
    handleChange(value) {
      this.inputText = value;
    },
    atPeopleSelect(item) {
      console.log("selected people ");
      console.log(item);
    },
    onEditorBlur(quill) {
      // console.log("editor blur!", quill);
    },
    onEditorFocus(quill) {
      // console.log("editor focus!", quill);
    },
    onEditorReady(quill) {
      // console.log("editor ready!", quill);
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
</style>