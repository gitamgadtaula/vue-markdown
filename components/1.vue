<template>
  <div class="quill-editor">
    <slot name="toolbar"></slot>
    <div ref="editor"></div>
  </div>
</template>

<script>
// require sources
import _Quill from "quill/core";
import { ImageResize } from "quill-image-resize-vue";
// import atPeople from "quill-mention-people";
import { Mention, QuillMentionBlot } from "quill-mention";
const Quill = window.Quill || _Quill;
import mention from "quill-mention";
// Quill.register(QuillMentionBlot);
// Quill.register("modules/atPeople", atPeople);
Quill.register("modules/mention", mention);
import "quill-mention"

const atValues = [
  { id: 1, value: "Fredrik Sundqvist" },
  { id: 2, value: "Patrik Sjölin" },
];
const defaultOptions = {
  theme: "snow",
  boundary: document.body,
  modules: {
    imageResize: {
      displaySize: true,
    },
    mention: {
      blotName: "mention",
      allowedChars: /^[A-Za-z\sÅÄÖåäö]*$/,
      mentionDenotationChars: ["@", "#"],
      source: function (searchTerm, renderItem, mentionChar) {
        let values;
        if (mentionChar === "@" || mentionChar === "#") {
          values = atValues;
        }
        if (searchTerm.length === 0) {
          renderItem(values, searchTerm);
        } else {
          const matches = [];
          for (let i = 0; i < values.length; i++)
            if (values[i].value.toLowerCase().indexOf(searchTerm.toLowerCase()))
              matches.push(values[i]);
          renderItem(matches, searchTerm);
        }
      },
    },
    // atPeople: {
    //   list: [
    //     { id: 1, name: "lmr" },
    //     { id: 2, name: "merry" },
    //     { id: 3, name: "box" },
    //     { id: 4, name: "Carry" },
    //     { id: 5, name: "Jony" },
    //     { id: 6, name: "merry" },
    //     { id: 7, name: "lala" },
    //     { id: 8, name: "xiaoxiong" },
    //     { id: 9, name: "herry" },
    //     { id: 10, name: "jerry" },
    //     { id: 11, name: "jackson" },
    //   ],

    //   // atOneMemberAction:this.atOneMemberAction
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
  placeholder: "Insert text here ...",
  readOnly: false,
  // formats:['mention']
};
// pollfill
if (typeof Object.assign != "function") {
  Object.defineProperty(Object, "assign", {
    value(target, varArgs) {
      if (target == null) {
        throw new TypeError("Cannot convert undefined or null to object");
      }
      const to = Object(target);
      for (let index = 1; index < arguments.length; index++) {
        const nextSource = arguments[index];
        if (nextSource != null) {
          for (const nextKey in nextSource) {
            if (Object.prototype.hasOwnProperty.call(nextSource, nextKey)) {
              to[nextKey] = nextSource[nextKey];
            }
          }
        }
      }
      return to;
    },
    writable: true,
    configurable: true,
  });
}
// export
export default {
  name: "quill-editor",
  data() {
    return {
      _options: {},
      _content: "",
      defaultOptions,
    };
  },
  props: {
    content: String,
    value: String,
    disabled: {
      type: Boolean,
      default: false,
    },
    options: {
      type: Object,
      required: false,
      default: () => ({}),
    },
    globalOptions: {
      type: Object,
      required: false,
      default: () => ({}),
    },
  },
  mounted() {
    this.initialize();
  },
  beforeDestroy() {
    this.quill = null;
    delete this.quill;
  },
  methods: {
    // Init Quill instance
    initialize() {
      if (this.$el) {
        // Options
        this._options = Object.assign(
          {},
          this.defaultOptions,
          this.globalOptions,
          this.options
        );
        // Instance
        this.quill = new Quill(this.$refs.editor, this._options);

        this.quill.enable(false);
        // Set editor content
        if (this.value || this.content) {
          this.quill.pasteHTML(this.value || this.content);
        }
        // Disabled editor
        if (!this.disabled) {
          this.quill.enable(true);
        }
        // Mark model as touched if editor lost focus
        this.quill.on("selection-change", (range) => {
          if (!range) {
            this.$emit("blur", this.quill);
          } else {
            this.$emit("focus", this.quill);
          }
        });
        // Update model if text changes
        this.quill.on("text-change", (delta, oldDelta, source) => {
          let html = this.$refs.editor.children[0].innerHTML;
          const quill = this.quill;
          const text = this.quill.getText();
          if (html === "<p><br></p>") html = "";
          this._content = html;
          this.$emit("input", this._content);
          this.$emit("change", { html, text, quill });
        });
        // Emit ready event
        this.$emit("ready", this.quill);
      }
    },
  },
  watch: {
    // Watch content change
    content(newVal, oldVal) {
      if (this.quill) {
        if (newVal && newVal !== this._content) {
          this._content = newVal;
          this.quill.pasteHTML(newVal);
        } else if (!newVal) {
          this.quill.setText("");
        }
      }
    },
    // Watch content change
    value(newVal, oldVal) {
      if (this.quill) {
        if (newVal && newVal !== this._content) {
          this._content = newVal;
          this.quill.pasteHTML(newVal);
        } else if (!newVal) {
          this.quill.setText("");
        }
      }
    },
    // Watch disabled change
    disabled(newVal, oldVal) {
      if (this.quill) {
        this.quill.enable(!newVal);
      }
    },
  },
};
</script>

 <style>
.ql-mention-list-container {
  width: 270px;
  border: 1px solid #f0f0f0;
  border-radius: 4px;
  /* background-color: #ffffff; */
  box-shadow: 0 2px 12px 0 rgba(30, 30, 30, 0.08);
  z-index: 9001;
  background-color: #484242;
  overflow: auto;
}
.v-application ul,
.v-application ol {
  padding-left: 0px;
}
.ql-mention-loading {
  line-height: 44px;
  padding: 0 20px;
  vertical-align: middle;
  font-size: 16px;
}

.ql-mention-list {
  list-style: none;
  margin: 0;
  padding: 0;
  overflow: hidden;
}

.ql-mention-list-item {
  cursor: pointer;
  line-height: 44px;
  font-size: 16px;
  padding: 0 20px;
  vertical-align: middle;
}

.ql-mention-list-item.disabled {
  cursor: auto;
}

.ql-mention-list-item.selected {
  /* background-color: #d3e1eb; */
  border: 1px solid #f0f0f0;

  background-color: #484242;
  text-decoration: none;
}

.mention {
  height: 24px;
  width: 65px;
  border-radius: 6px;
  background-color: #d3e1eb;
  padding: 3px 0;
  margin-right: 2px;
  user-select: all;
}

.mention > span {
  margin: 0 3px;
}
</style>
