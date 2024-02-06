<template>
  <div>
    <button v-for="admin in admins" :key="admin" class="submit-button" @click="selectAdmin(admin)">
      {{ admin }}
    </button>
    <TreeFruits class="fruits-view" v-if="fruitsVisible" />
  </div>
</template>

<script>
import { ref, watch } from 'vue';
import TreeFruits from '@/components/TreeFruits.vue';

export default {
  components: {
    TreeFruits,
  },
  props: {
    adminButtons: Array,
    updateAdminButtons: Function, // Assuming you have a prop to pass the update method
  },
  setup(props) {
    const admins = ref(['Admin1', 'Admin2', 'Admin3', 'Admin4', 'Admin5', 'Admin6']);
    const fruitsVisible = ref(false);

    const selectAdmin = (admin) => {
      console.log(`Selected admin: ${admin}`);
      toggleFruits();
    };

    const toggleFruits = () => {
      fruitsVisible.value = !fruitsVisible.value;
    };

    watch(
      () => props.adminButtons,
      (newAdminButtons) => {
        console.log('Admin buttons updated:', newAdminButtons);
        updateTreeAdminButtons(newAdminButtons);
      },
    );

    const updateTreeAdminButtons = (newButtonLabel) => {
      // Create a new array with updated labels
      const updatedButtons = props.adminButtons.map(() => newButtonLabel);

      // Emit the updated array to the parent component
      props.updateAdminButtons(updatedButtons);
    };

    return {
      admins,
      fruitsVisible,
      selectAdmin,
    };
  },
};
</script>

<style scoped>
.fruits-view {
  margin-left: 20px;
}

.submit-button {
  background-color: #ff0021;
  color: #fff;
  padding: 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>
