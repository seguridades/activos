<template>
  <div v-if="show" class="position-fixed bottom-0 end-0 p-3" style="z-index: 1050">
    <div
      :class="`toast show bg-${type} text-white`"
      role="alert"
      aria-live="assertive"
      aria-atomic="true"
    >
      <div class="toast-header">
        <strong class="me-auto">{{ title }}</strong>
        <button
          type="button"
          class="btn-close btn-close-white ms-2 mb-1"
          @click="close"
          aria-label="Close"
        ></button>
      </div>
      <div class="toast-body">
        {{ message }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    title: { type: String, required: true },
    message: { type: String, required: true },
    type: {
      type: String,
      default: 'success',
      validator: (v) => ['success', 'danger', 'info', 'warning'].includes(v),
    },
  },
  data() {
    return {
      show: true,
    }
  },
  mounted() {
    setTimeout(() => {
      this.show = false
    }, 3000)
  },
  methods: {
    close() {
      this.show = false
    },
  },
}
</script>
