<template>
  <div class="wrapper renderer">
    <div class="" v-html="truncatedText"  :class="truncate ? 'truncated-text' : ''" />
  </div>
</template>

<script>
export default {
  name: 'RichTextViewer',
  props: ['value', 'truncate'],
  computed: {
    truncatedText: function() {
      return this.value;
    }
  }
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
.truncated-text {
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
}
</style>