<template v-show="errors">
  <ul class="error-message">
    <li v-for="(value, index) in error" :key="index">
      <span>{{index}}</span>
      <span v-for="item in value" :key="item">{{item}}</span>
    </li>
  </ul>
</template>

<script>
export default {
  name:"ListError",
  props:{
    error:{type:Object}
  }
}
</script>