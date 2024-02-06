<template>
    <div>
      <div>
        <label for="animalLimit">Animal Limit:</label>
        <input id="animalLimit" v-model="animalLimitInput" type="number" min="1" @input="updateAnimalLimit" />
        <button @click="confirmLimit">Confirm</button>
      </div>
      <div v-if="limitConfirmed && !animalLimitReached">
        <input v-model="newAnimal" placeholder="Enter an animal name" @keyup.enter="addAnimal" :disabled="animalLimitReached" />
        <button @click="addAnimal" :disabled="animalLimitReached">Add Animal</button>
      </div>
      <div>
        <button v-for="animal in animals" :key="animal" class="submit-button" @click="selectAnimal(animal)">{{ animal }}</button>
      </div>
    </div>
  </template>
  
  <script>
  import { ref } from 'vue';
  
  export default {
    setup() {
      const animals = ref([]);
      const newAnimal = ref('');
      const animalLimitInput = ref(1);
      const animalCounter = ref(0);
      const limitConfirmed = ref(false);
      const animalLimitReached = ref(false);
  
      const selectAnimal = (animal) => {
        console.log(`Selected animal: ${animal}`);
      };
  
      const addAnimal = () => {
        if (newAnimal.value.trim() !== '' && animalCounter.value < animalLimitInput.value) {
          animals.value.push(newAnimal.value.trim());
          newAnimal.value = '';
          animalCounter.value += 1; 
  
          if (animalCounter.value >= animalLimitInput.value) {
            animalLimitReached.value = true;
          }
        }
      };
  
      const updateAnimalLimit = () => {
        animalCounter.value = 0;
        animalLimitReached.value = false;
        limitConfirmed.value = false;
      };
  
      const confirmLimit = () => {
        limitConfirmed.value = true;
      };
  
      return {
        animals,
        newAnimal,
        selectAnimal,
        addAnimal,
        animalLimitInput,
        updateAnimalLimit,
        animalLimitReached,
        confirmLimit,
        limitConfirmed,
      };
    },
  };
  </script>
  
<style>

.submit-button {
    background-color: #f99500;
    color: #fff;
    padding: 10px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }



</style>