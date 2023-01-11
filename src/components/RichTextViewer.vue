<template>
  <div class="wrapper renderer">
    <div class="" v-html="value" />
  </div>
</template>

<script>
export default {
  name: 'RichTextViewer',
  props: ['value']
}
</script>

<style>
.renderer ul {
  @apply list-inside list-disc
}
.renderer ol {
  @apply list-inside list-decimal
}
.renderer a {
  @apply text-blue-500 underline
}
</style>