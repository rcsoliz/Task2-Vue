<template>
 <div>
    <p>Count: {{ count }}</p>
    <p>Double Count: {{ doubleCount }}</p>
    <p>Base URL: {{ baseUrl }}</p>
    <button @click="increment">Increment</button>
  </div>

  <div>
    <button @click="showToast('Info message', 'info')">Show Info Toast</button>
    <button @click="showToast('Success message', 'success')">Show Success Toast</button>
    <button @click="showToast('Warning message', 'warning')">Show Warning Toast</button>
    <button @click="showToast('Danger message', 'danger')">Show Danger Toast</button>
  </div>

</template>
<script>
import { mapState, mapGetters, mapActions } from 'vuex'

export default {
  computed: {
    ...mapState(['count']),
    ...mapGetters(['doubleCount', 'getBaseUrl']),
    baseUrl() {
      return this.getBaseUrl
    }
  },
  methods: {
    ...mapActions(['increment']),
    
    showToast(message, type) {
      this.$toast.show(message, type);
    }
  }

}
</script>