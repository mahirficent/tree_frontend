<template>
  <div>
    <button @click="toggleAdmin" class="submit-button">{{ buttonLabel }}</button>
    <TreeAdmin class="admin-view" v-if="adminVisible" :adminButtons="adminButtons" />
  </div>
</template>

<script>
import { ref, watch } from 'vue';
import TreeAdmin from '@/components/TreeAdmin.vue';

export default {
  components: {
    TreeAdmin,
  },
  props: {
    buttonLabel: String,
    adminButtons: Array,
  },
  setup(props) {
    const adminVisible = ref(false);

    const toggleAdmin = () => {
      adminVisible.value = !adminVisible.value;
    };

    const handleButtonLabelChange = (newButtonLabel) => {
      if (!props.adminButtons.includes(newButtonLabel)) {
        props.adminButtons.push(newButtonLabel);
        console.log('Admin buttons updated:', props.adminButtons);
      }
    };

    watch(() => props.buttonLabel, handleButtonLabelChange);

    return {
      adminVisible,
      toggleAdmin,
    };
  },
};
</script>

<style scoped>
.admin-view {
  margin-left: 10px;
}

.submit-button {
    background-color: #42b883;
    color: #fff;
    padding: 10px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}
</style>
