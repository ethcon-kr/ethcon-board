// Todo change Message showing up not push button.
<template>
  <div id="ethboard-body">
    <!--<slot name="ethboard-body"></slot>-->
    <div>
      <h3>{{ postedMessages[0].message }}</h3>
    </div>
    <div>
      <h2>{{ postedMessages[1].message }}</h2>
    </div>
    <div>
      <h1>{{ postedMessages[2].message }}</h1>
    </div>
  </div>
</template>

<script>
export default {
    props: ['postedMessages']
}
</script>

<style>
#ethboard-body {
  margin-left: 6em;
  margin-right: 6em;
  margin-bottom: 5em;
}

</style>
