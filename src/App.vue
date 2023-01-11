<template>
  <div id="app">
    <rich-text-editor v-model="value" @change="value = $event" />
    ---
    <rich-text-viewer :value="value" />
  </div>
</template>

<script>
import RichTextEditor from './components/RichTextEditor.vue'
import RichTextViewer from './components/RichTextViewer.vue'

export default {
  name: 'App',
  components: {
    RichTextEditor,
    RichTextViewer
  },
  data() {
    return {
      value: ``,
      value2:   `<div>etst</div>`
    };
  }
}
</script>
