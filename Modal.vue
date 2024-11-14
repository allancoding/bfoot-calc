<template>
    <div class="modal-overlay" v-if="visible">
      <div class="modal">
        <button @click="close">Close</button>
        <slot></slot>
      </div>
    </div>
</template>
  
<script>
  export default {
    props: {
      visible: {
        type: Boolean,
        default: false,
      },
    },
    methods: {
      close() {
        this.$emit('close');
      },
    },
  };
</script>
  
<style scoped>
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .modal {
    background: white;
    padding: 20px;
    border-radius: 5px;
  }
</style>