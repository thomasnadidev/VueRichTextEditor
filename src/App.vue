<template>
  <div id="app">
    <rich-text-editor v-model="value" @change="value = $event" />
  </div>
</template>

<script>
import RichTextEditor from './components/RichTextEditor.vue'

export default {
  name: 'App',
  components: {
    RichTextEditor
  },
  data() {
    return {
      value: ``,
      value2:   `<div>etst</div>`
    };
  }
}
</script>
