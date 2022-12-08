<template>
  <div id="app">
    <rich-text-editor v-model="value" @change="value = $event" />
    {{ value }}
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
      value: ""
    };
  }
}
</script>
