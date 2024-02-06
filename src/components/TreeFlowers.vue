<template>
    <div>
      <button v-for="flower in flowers" :key="flower" class="submit-button" @click="toggleAnimals">{{ flower }}</button>
      <TreeAnimals class="animals-view" v-if="animalsVisible" />
    </div>
  </template>
  
  <script>
  import { ref } from 'vue';
  import TreeAnimals from '@/components/TreeAnimals.vue';
  
  export default {
    components: {
      TreeAnimals,
    },
    setup() {
      const flowers = ref(['Rose', 'Sunflower', 'Tulip', 'Daisy', 'Lily', 'Daffodil']);
      const animalsVisible = ref(false);
  
      const toggleAnimals = () => {
        animalsVisible.value = !animalsVisible.value;
      };
  
      return {
        flowers,
        animalsVisible,
        toggleAnimals,
      };
    },
  };
  </script>
  
  <style scoped>
  .animals-view {
    margin-left: 30px;
  }

  .submit-button {
    background-color: #ad3493;
    color: #fff;
    padding: 10px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  </style>
  