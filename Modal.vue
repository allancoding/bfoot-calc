<template>
  <div class="modal-overlay" :class="{ show: isShown }">
    <div class="modal">
      <Icon class="close" name="material-symbols:close-rounded" @click="closeModal()" />
      <slot></slot>
    </div>
  </div>
</template>
  
<script>
  export default {
    props: {
      isShown: {
        type: Boolean,
        default: false,
      },
    },
    data() {
      return {
        localIsShown: this.isShown,
      };
    },
    watch: {
      isShown(newVal) {
        this.localIsShown = newVal;
      },
    },
    methods: {
      closeModal() {
        this.localIsShown = false;
        this.$emit('update:isShown', false);
      },
      openModal() {
        this.localIsShown = true;
        this.$emit('update:isShown', true);
      },
    },
  };
</script>
  
<style scoped>
  @keyframes zoomIn {
    from {
      transform: scale(0);
    }
    to {
      transform: scale(1);
    }
  }

  @keyframes fadeInOut {
    0% {
      opacity: 0;
      visibility: hidden;
    }
    100% {
      opacity: 1;
      visibility: visible;
    }
  }

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
    opacity: 0;
    visibility: hidden;
    transition: opacity 0.5s ease, visibility 0s 0.5s;
  }

  .modal-overlay.show {
    opacity: 1;
    visibility: visible;
    transition: opacity 0.5s ease;
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
    animation: zoomIn ease 0.5s;
    animation-iteration-count: 1;
    animation-fill-mode: both;
  }

  .close {
    position: absolute;
    top: 10px;
    right: 10px;
    cursor: pointer;
  }
</style>