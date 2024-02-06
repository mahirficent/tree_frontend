<template>
  <div class="create-form-container">
    <h2 class="form-title">Create Form</h2>

    <div class="form-group">
      <label for="name">Name:</label>
      <input v-model="formData.name" id="name" type="text">
    </div>

    <div class="form-group">
      <label for="id">ID:</label>
      <input v-model="formData.id" id="id" type="number">
    </div>

    <div class="form-group">
      <label>Parent:</label>
      <input v-model="formData.isParent" type="checkbox" class="checkbox">
    </div>

    <div class="form-group">
      <label>Child:</label>
      <input v-model="formData.isChild" type="checkbox" class="checkbox">
    </div>

    <div v-if="formData.isChild" class="form-group">
      <label for="parentName">Parent Name:</label>
      <select v-model="formData.parentName" id="parentName">
        <option v-for="item in parentItems" :key="item.name" :value="item.name">{{ item.name }}</option>
      </select>
    </div>

    <button @click="submitForm" class="submit-button">Submit</button>

    <!-- Display the dynamically created button -->
    <div v-if="createdItem">
      <button class="created-button" @click="handleButtonClick">{{ createdItem.name }}</button>
      <!-- Display form inputs -->
      <div>
        <p>Name: {{ createdItem.name }}</p>
        <p>ID: {{ createdItem.id }}</p>
        <p>Is Parent: {{ createdItem.isParent }}</p>
        <p>Is Child: {{ createdItem.isChild }}</p>
        <p>Parent Name: {{ createdItem.parentName }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  props: ['visible', 'parentItems'],
  setup(props, { emit }) {
    const formData = ref({
      name: '',
      id: '',
      isParent: false,
      isChild: false,
      parentName: '',
    });

    const createdItem = ref(null);

    const submitForm = () => {
      console.log('Submitting form:', createdItem.value)
      const idAsInteger = parseInt(formData.id);
      const isValidId = !isNaN(idAsInteger) && Number.isInteger(idAsInteger);

      if (typeof formData.name === 'string' && formData.name.trim() !== '' && isValidId) {
        const trimmedId = idAsInteger;

        createdItem.value = {
          name: formData.name.trim(),
          id: trimmedId,
          isParent: Boolean(formData.isParent),
          isChild: Boolean(formData.isChild),
          parentName: formData.isChild ? formData.parentName : null,
        };

        // Send the new item to JSON Server
        fetch('http://localhost:3000/items', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(createdItem.value),
        })
        .then(response => {
          if (!response.ok) {
            throw new Error('Failed to add item');
          }
          return response.json();
        })
        .then(data => {
          // Fetch the updated data from JSON Server after successful submission
          fetch('http://localhost:3000/items')
            .then(response => response.json())
            .then(updatedData => emit('submit', updatedData))
            .catch(error => console.error('Error fetching updated data:', error));
        })
        .catch(error => console.error('Error:', error));

        // Clear form fields
        formData.name = '';
        formData.id = '';
        formData.isParent = false;
        formData.isChild = false;
        formData.parentName = '';
      }
    };



    const handleButtonClick = () => {
      // Handle button click if needed
    };

    return {
      formData,
      createdItem,
      submitForm,
      handleButtonClick,
    };
  },
};
</script>

<style scoped>
.create-form-container {
  max-width: 400px;
  margin: auto;
  padding: 20px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  border: 1px solid #ddd;
}

.form-title {
  font-size: 1.5rem;
  font-weight: bold;
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 15px;
}

label {
  display: block;
  font-size: 0.875rem;
  margin-bottom: 5px;
}

input, select {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.submit-button {
  background-color: #35495e;
  color: #fff;
  padding: 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.checkbox {
  width: 20px;
  height: 20px;
  margin-right: 5px;
}

.created-button {
  margin-top: 10px;
  background-color: #3490dc;
  color: #fff;
  padding: 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>
