<template>
  <div id="ethboard-post">
    <slot name="ethboard-post"></slot>
  </div>
</template>

<script>
export default {
  data() {
    return {
      input_text: null
    }
  }
}
</script>

<style>
#ethboard-post {
  margin-left: 6em;
  margin-right: 6em;
  margin-bottom: 5em;
}
</style>
