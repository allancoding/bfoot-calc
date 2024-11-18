<template>
    <div class="modal-overlay" v-if="visible">
      <div class="modal">
        <Icon class= "close" name="material-symbols:close-rounded" @click="close" />
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
    background: #000;
    padding: 20px;
    border-radius: 5px;
    min-width: 25%;
    min-height: 25%;
    color: #fff;
    box-shadow: 0 0 30px rgba(255, 255, 255, 0.5);
    border-radius: 10px;
    border: 1px solid white;
    position: relative;
  }

  .close {
    position: absolute;
    top: 10px;
    right: 10px;
    cursor: pointer;
  }
</style>