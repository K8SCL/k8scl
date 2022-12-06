<template>
  <div>
    <a style="font-size: 12px" @click="saveFile">download this drawio </a>
    <div>
      <div
        v-if="loading"
        style="
          width: 100%;
          height: 100px;
          display: flex;
          justify-content: center;
          align-content: center;
        "
      >
        <img width="50" height="50" src="/assets/img/loading.gif" />
      </div>
      <div v-else v-html="drawioHtml"></div>
    </div>
  </div>
</template>

<script>
import FileSaver from "file-saver";
let markdown = window.markdownit().use(MarkdownItDrawioViewer);
export default {
  props: {
    src: "",
  },
  data() {
    return {
      loading: true,
      drawioHtml: "",
    };
  },
  async mounted() {
    await this.renderDrawio();
  },
  methods: {
    async renderDrawio() {
      const pre_md = await fetch(this.src).then((res) => res.text());
      let md =
        "```drawio\n" +
        `<?xml version="1.0" encoding="UTF-8"?>` +
        pre_md +
        "\n```";
      this.drawioHtml = markdown.render(md);
      await this.setDrawViewScript();
      this.loading = false;
    },

    async setDrawViewScript() {
      const script = document.createElement("script");
      script.src = "https://www.draw.io/js/viewer.min.js";
      script.type = "text/javascript";
      document.querySelector("body").appendChild(script);
    },

    async saveFile() {
      const srcArr = this.src.split("/");
      const file = await fetch(this.src).then((res) => res.blob());
      FileSaver.saveAs(file, srcArr[srcArr.length - 1]);
    },
  },
};
</script>

<style>
</style>