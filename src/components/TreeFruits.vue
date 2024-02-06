<template>
  <div>
    <button v-for="fruit in fruits" :key="fruit" class="submit-button" @click="selectFruit(fruit)">{{ fruit }}</button>
    <TreeVegetables class="vegetables-view" v-if="vegetablesVisible" />
  </div>
</template>

<script>
import { ref } from 'vue';
import TreeVegetables from '@/components/TreeVegetables.vue';

export default {
  components: {
    TreeVegetables,
  },
  setup() {
    const fruits = ref(['Apple', 'Banana', 'Orange', 'Grapes', 'Watermelon', 'Mango']);
    const vegetablesVisible = ref(false);

    const selectFruit = (fruit) => {
      console.log(`Selected fruit: ${fruit}`);
      toggleVegetables();
    };

    const toggleVegetables = () => {
      vegetablesVisible.value = !vegetablesVisible.value;
    };

    return {
      fruits,
      vegetablesVisible,
      selectFruit,
    };
  },
};
</script>

<style scoped>
.vegetables-view {
  margin-left: 30px;
}
.submit-button {
    background-color: #fffb25;
    color: #fff;
    padding: 10px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
</style>
