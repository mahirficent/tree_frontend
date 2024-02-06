<template>
  <div>
    <button v-for="vegetable in vegetables" class="submit-button" :key="vegetable" @click="toggleFlowers">{{ vegetable }}</button>
    <TreeFlowers class="flowers-view" v-if="flowersVisible" />
  </div>
</template>

<script>
import { ref } from 'vue';
import TreeFlowers from '@/components/TreeFlowers.vue';

export default {
  components: {
    TreeFlowers,
  },
  setup() {
    const vegetables = ref(['Carrot', 'Broccoli', 'Spinach', 'Tomato', 'Cucumber', 'Pepper']);
    const flowersVisible = ref(false);

    const toggleFlowers = () => {
      flowersVisible.value = !flowersVisible.value;
    };

    return {
      vegetables,
      flowersVisible,
      toggleFlowers,
    };
  },
};
</script>

<style scoped>
.flowers-view {
  margin-left: 40px;
}
.submit-button {
    background-color: #1f90bb;
    color: #fff;
    padding: 10px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
</style>
