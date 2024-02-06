<template>
  <div v-show="isVisible" class="add-item-container">
    <h2>Add Item</h2>

    <!-- Form -->
    <form @submit.prevent="submitForm">
      <!-- Your form inputs go here -->
    </form>


    <!-- Name Input -->
    <div class="form-group">
      <label for="name">Name:</label>
      <input v-model="formData.name" id="name" type="text" class="input-field">
      <span v-if="!formData.name" class="error-message">Name is required</span>
    </div>

    <!-- ID Input -->
    <div class="form-group">
      <label for="id">ID:</label>
      <input v-model="formData.id" id="id" type="number" class="input-field">
      <span v-if="!formData.id" class="error-message">ID is required</span>
    </div>

    <!-- Parent Checkbox -->
    <div class="form-group">
      <label>Parent:</label>
      <input v-model="formData.isParent" type="checkbox" class="checkbox" @change="handleParentChange">
    </div>

    <!-- Child Checkbox -->
    <div class="form-group">
      <label>Child:</label>
      <input v-model="formData.isChild" type="checkbox" class="checkbox" @change="handleChildChange">
      <span v-if="formErrors.firstInputError" class="error-message">{{ formErrors.firstInputError }}</span>
    </div>
    
    <!-- Parent Name Dropdown (if isChild is true) -->
    <div v-if="formData.isChild" class="form-group">
      <label for="parentName">Parent Name:</label>
      <select v-model="formData.parentName" id="parentName" class="select-field">
        <option v-for="name in allNames" :key="name" :value="name">{{ name }}</option>
      </select>
    </div>
    
    <!-- Submit Button -->
    <button :disabled="!canSubmit" @click="submitForm" class="submit-button">Submit</button>

    
  </div>
</template>

<script>
import { ref, watch, onMounted } from 'vue';

export default {
  props: ['isVisible', 'parentItems'],
  setup(props, { emit }) {
    const formData = ref({
      name: '',
      id: '',
      isParent: false,
      isChild: false,
      parentName: '',
    });

    const allNames = ref([]);
    const formErrors = ref({});
    const canSubmit = ref(true);

    const watchParentName = () => {
      // Watch for changes in formData.parentName and update adminButtons accordingly
      watch(() => formData.value.parentName, (newParentName) => {
        if (newParentName === 'Root') {
          // Assuming formData.name is the input for admin buttons
          adminButtons.value = [formData.value.name];
        }
      });
    };

    onMounted(() => {
      // Call the watch function on component mount
      watchParentName();
    });


    const handleParentChange = () => {
      // If the item is chosen as a parent after an error, reset the error and enable form submission
      if (formErrors.value.firstInputError) {
        formErrors.value.firstInputError = '';
        canSubmit.value = true;
      }

      // If the item is chosen as a parent, disable the child checkbox
      formData.value.isChild = !formData.value.isParent;
    };

    const handleChildChange = () => {
      // If the item is chosen as a child and it's the first input, set canSubmit to false
      if (allNames.value.length === 0 && formData.value.isChild) {
        formErrors.value.firstInputError = 'First input must be a parent';
        formData.value.isChild = false; // Uncheck the "Child" checkbox
        canSubmit.value = false;
        return;
      }

      // If the item is chosen as a child, disable the parent checkbox
      formData.value.isParent = !formData.value.isChild;

      // If the user clicks the child checkbox after an error, reset the error and enable form submission
      if (formErrors.value.firstInputError) {
        formErrors.value.firstInputError = '';
        canSubmit.value = true;
      }

      // Dynamically update the admin buttons based on the form input
      if (formData.value.isChild && formData.value.parentName === 'Root') {
        const adminButtonIndex = adminButtons.findIndex((button) => button === formData.value.name);

        if (adminButtonIndex !== -1) {
          // If the name already exists, replace it
          adminButtons[adminButtonIndex] = formData.value.name;
        } else {
          // If it's a new name, add it to the adminButtons array
          adminButtons.push(formData.value.name);
        }

        console.log('Admin buttons updated:', adminButtons);
      }

    };

    const submitForm = async (event) => {
      event.preventDefault();
      try {
        // Validate and submit the form data
        // ...
        // Check if the form can be submitted
        if (!canSubmit.value) {
          console.error('Form cannot be submitted. Please correct errors.');
          return;
        }
        // Validate form inputs
        formErrors.value = {};

        if (!formData.value.name) {
          formErrors.value.name = 'Name is required';
        }

        if (!formData.value.id) {
          formErrors.value.id = 'ID is required';
        }

        // Check if there are any validation errors
        if (Object.keys(formErrors.value).length > 0) {
          console.error('Form validation failed:', formErrors.value);
          return;
        }

        // Save the name to the list of allNames
        allNames.value.push(formData.value.name);

        // Save the new item to JSON Server
        const newItem = {
          name: formData.value.name,
          id: formData.value.id,
          isParent: formData.value.isParent,
          isChild: formData.value.isChild,
          parentName: formData.value.parentName,
        };

        const response = await fetch('http://localhost:3000/items', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(newItem),
        });

        if (!response.ok) {
          console.error('Failed to save item:', response.statusText);
        }

        // Emit the form data to the parent component
        emit('submit', { ...formData.value });
        clearFormData();
      } catch (error) {
        console.error('Error saving item:', error.message);
      }
    };

    const clearFormData = () => {
      formData.value.name = '';
      formData.value.id = '';
      formData.value.isParent = false;
      formData.value.isChild = false;
      formData.value.parentName = '';
    };

    // Watch for changes in parentItems and update allNames accordingly
    watch(() => props.parentItems, (newParentItems) => {
      if (newParentItems.length > 0) {
        // Extract names from newParentItems and add to allNames
        const newNames = newParentItems.map((item) => item.name);
        allNames.value = Array.from(new Set(allNames.value.concat(newNames)));
      }
    });

    // Load allNames from JSON Server on component mount
    onMounted(async () => {
      try {
        const response = await fetch('http://localhost:3000/items');
        if (response.ok) {
          const items = await response.json();
          allNames.value = items.map((item) => item.name);
        } else {
          console.error('Failed to fetch data from JSON Server:', response.statusText);
        }
      } catch (error) {
        console.error('Error fetching data from JSON Server:', error.message);
      }
    });

    return {
      formData,
      allNames,
      formErrors,
      canSubmit,
      submitForm,
      handleParentChange,
      handleChildChange,
      watchParentName,
    };
  },
};
</script>

<style scoped>
.add-item-container {
  max-width: 400px;
  margin: auto;
  padding: 20px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  border: 1px solid #ddd;
}

h2 {
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

.input-field,
.select-field {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.checkbox {
  width: 20px;
  height: 20px;
  margin-right: 5px;
}

.submit-button {
  background-color: #35495e;
  color: #fff;
  padding: 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.error-message {
  color: red;
  font-size: 0.875rem;
  margin-top: 5px;
}
</style>
