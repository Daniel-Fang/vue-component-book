<template>
    <i-input v-model="name"></i-input>
    <!-- <i-input :value="name" @input="name = arguments[0]"></i-input> -->
</template>

<script>
import iForm from './form.vue';
import iFormItem from './form-item.vue';
import iInput from './input.vue';

  export default {
    components: {
        iForm,
        iFormItem,
        iInput
    },  
    props:{},
    data () {
      return {
          name: '',
          formValidate: {
              name: '',
              mail: ''
          },
          rules: {
              name: [
                  {
                      required: true,
                      message: '用户名不能为空',
                      trigger: 'blur'
                  }
              ],
              mail: [
                  {
                      required: true,
                      message: '邮箱不能为空',
                      trigger: 'blur'
                  },
                  {
                      type: 'email',
                      message: '邮箱格式不正确',
                      trigger: 'blur'
                  }
              ]
          }
      };
    },
    watch: {
        name() {
            console.log(this.name);
        }
    }
  }
</script>

<style scoped>

</style>