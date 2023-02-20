<script>
import Vue from 'vue';
import TypedataSuggestionsVue from '@/typedata-suggestions-vue.vue';

export default Vue.extend({
  name: 'ServeDev',
  components: {
    TypedataSuggestionsVue
  },
  data() {
    return {
      token: "TOKEN"
    }
  },
  methods: {
    changed(data) {
      console.log(data);
    }
  }
});
</script>

<template>
  <div id="app">
    <typedata-suggestions-vue
        :token="token"
        :on-change="changed"
    />
  </div>
</template>
