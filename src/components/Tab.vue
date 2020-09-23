<template>
  <div v-show="isActive">
    <slot></slot>
  </div>
</template>

<script>
export default {
  props: {
    name: { required: true },
    selected: { default: false }
  },
  data() {
    return {
      isActive: false
    }
  },
  mounted() {
    // Sync isActive status with selected status when template is rendered
    this.isActive = this.selected;
  }
}
</script>

<style scoped>

</style>