<template>
    <div class="module-container">
      <div class="echart-wrap" ref="echarts"></div>
    </div>
</template>
<style lang="sass" scoped>
  .echart-wrap
    width: 200px
    height: 100px
</style>
<script>
    

    export default {
        props: ['data'],

        data() {
            return {
            }
        }
    }
</script>

