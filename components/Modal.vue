<template>
  <div class="modal-overlay" @click="$emit('close-modal')">
    <div class="modal" @click.stop>
        <div class="close" @click="$emit('close-modal')">
          <Icon class="close" name="material-symbols:close-rounded" />
        </div>
        <slot></slot>
    </div>
  </div>
</template>
  
<script>
  export default {};
</script>
  
<style scoped>
  .showModal {
    opacity: 1;
    visibility: visible;
  }

  .hideModal {
    opacity: 0;
    visibility: hidden;
  }

  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.75);
    display: flex;
    justify-content: center;
    align-items: center;
    transition: opacity 0.5s, visibility 0.5s;
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
    text-align: center;
    max-width: 80%;
  }

  .close {
    position: absolute;
    top: 10px;
    right: 10px;
    cursor: pointer;
    color: white;
  }

  .close:hover {
    color: red;
  }
</style>