<template>
  <div>
    <v-card elevation="2">
      <v-tabs v-model="tab">
        <v-tab>B</v-tab>
        <v-tab>I</v-tab>
        <v-tab>Image</v-tab>
      </v-tabs>
      <v-textarea
        name="input-7-1"
        label=""
        value=""
        hint=""
        @change="handleChange"
      ></v-textarea>
    </v-card>
    {{ tab }}
    <div v-html="compiledMarkdown"></div>
  </div>
</template>

<script>
import marked from "marked";
export default {
  data() {
    return {
      tab: null,
      inputText: "",
    };
  },
  methods: {
    handleChange(value) {
      this.inputText = value;
    },
  },

  computed: {
    compiledMarkdown() {
      return marked(this.inputText, { sanitize: true });
    },
  },
};
</script>

<style>
</style>