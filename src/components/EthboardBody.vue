// Todo change Message showing up not push button.
<template>
  <div id="ethboard-body">
    <slot name="ethboard-body"></slot>
  </div>
</template>

<script>
export default {
  data() {
    return {
      postedMessages: null
    }
  }
}
</script>

<style>
#ethboard-body {
  margin-left: 6em;
  margin-right: 6em;
  margin-bottom: 5em;
}
</style>
