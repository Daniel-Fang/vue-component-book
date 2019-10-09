<template>
  <button :class="'i-button-size' + size" :disabled="disabled">
      <slot>按钮</slot>
  </button>
</template>

<script>
  function oneOf(value, vaildList) {
      for(let i = 0; i < vaildList.length; i++) {
          if(value === vaildList[i]) {
              return true;
          }
      }
      return false;
  }
  export default {
    inheritAttrs: false, //注意 inheritAttrs: false 选项不会影响 style 和 class 的绑定。
    props:{
        size: {
            validator(value) {
                return oneOf(value, ['small', 'large', 'default']);
            },
            default: 'default'
        },
        disabled: {
            type: Boolean,
            default: false
        }
    }
  }
</script>

<style scoped>

</style>