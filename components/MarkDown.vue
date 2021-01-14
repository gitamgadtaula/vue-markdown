<template>
  <div class="quill-editor">
    <slot name="toolbar"></slot>
    <div ref="editor"></div>
  </div>
</template>

<script>
import _Quill from "quill";
import { ImageResize } from "quill-image-resize-vue";
import atPeople from "@/components/mention-people";
const Quill = window.Quill || _Quill;
Quill.register("modules/atPeople", atPeople);
import hljs from "highlight.js";
import "highlight.js/styles/darcula.css";
hljs.configure({
  languages: [
    "java",
    "dart",
    "javascript",
    "ruby",
    "python",
    "rust",
    "css",
    "php",
    "html",
  ],
});

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
  props: {
    content: String,
    value: String,
    atPeople: {
      type: Array,
    },
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
  data() {
    return {
      _options: {},
      _content: "",
      defaultOptions: {
        theme: "snow",
        boundary: document.body,
        modules: {
          syntax: {
            highlight: (text) => hljs.highlightAuto(text).value,
          },
          imageResize: {
            displaySize: true,
          },
          atPeople: {
            list: this.atPeople,
            atOneMemberAction: this.atOneMemberAction,
          },
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
        placeholder: "...",
        readOnly: false,
      },
    };
  },

  mounted() {
    this.initialize();
  },
  beforeDestroy() {
    this.quill = null;
    delete this.quill;
  },
  methods: {
    atOneMemberAction(item) {
      this.$emit("at-people", item);
    },
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
    atPeople(value) {
      // console.log(value);
    },
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
  computed: {
    newDefaultOptions() {
      this.defaultOptions.modules.atPeople.list = this.atPeople;
      return this.defaultOptions;
    },
  },
};
</script>

 <style>
.quill-editor {
  box-shadow: 0px 2px 4px -1px rgba(0, 0, 0, 0.2),
    0px 4px 5px 0px rgba(0, 0, 0, 0.14), 0px 1px 10px 0px rgba(0, 0, 0, 0.12);
}
.ql-container {
  font-size: 20px;
  border: 0 !important;
  background-color: #1e1e1e;
}
.ql-toolbar {
  border: 0 !important;
  background-color: #272727;
}

.ql-active {
  background-color: #001 !important;
}
.ql-picker-options {
  background-color: #000;
}

.ql-toolbar.ql-snow .ql-picker.ql-expanded .ql-picker-options {
  border-color: #ccc;
  background-color: #242222;
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

.quill-at-member > ul {
  color: #020202;
}

.quill-at-member {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 9999;
  min-width: 120px;
  display: none;
}
.quill-at-member > ul {
  position: relative;
  padding: 0;
  margin: 0;
  border: 1px solid #eee;
  max-height: 200px;
  overflow-y: auto;
}
.quill-at-member > ul > li {
  position: relative;
  padding: 10px 0;
  margin: 0;
  list-style: none;
  background-color: #fff;
  cursor: pointer;
}

.quill-at-member > ul > li.selected {
  background: #eee;
}
.quill-at-member > ul > li:hover {
  background: #eee;
}
</style>
