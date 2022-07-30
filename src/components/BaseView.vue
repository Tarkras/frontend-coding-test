<template>
  <div>
    <h1 class="title">{{ title }}</h1>
    <div>
      <slot />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      required: false
    }
  }
}
</script>
<style lang="scss" scoped>
.title{
  margin-top: 40px;
  margin-bottom: 25px;

  @media (min-width: 650px) {
    margin-top: 20px;
    margin-bottom: 60px;
  }
}
</style>
