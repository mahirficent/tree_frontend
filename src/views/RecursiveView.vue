<template>
  <div class="recursive-view-container">
    <h2>Recursive View</h2>

    <!-- Display button to toggle AddItem component -->
    <button @click="toggleAddItem" class="add-button">Add</button>

    <!-- Display CreateForm component -->
    <AddItem v-if="showAddItem" @submit="handleAddItemSubmit" :parentItems="treeItems" :isVisible="showAddItem" />

    <!-- Display submitted items -->
    <div v-if="submittedItems.length > 0" class="submitted-items">
      <h3>Submitted Items:</h3>
      <ul>
        <li v-for="(item, index) in submittedItems" :key="index">
          Name: {{ item.name }} - ID: {{ item.id }} - Parent: {{ item.isParent }} - Child: {{ item.isChild }} - Parent Name: {{ item.parentName !== null && item.parentName !== '' ? item.parentName : 'null' }}
        </li>
      </ul>
    </div>

    <!-- Display the tree structure using TreeRoot component -->
    <TreeRoot 
        :items="treeItems" 
        :buttonLabel="submittedItems.length > 0 ? submittedItems[0].name : 'Root'" 
        :adminButtons="adminButtons" 
        class="tree-root" />
  </div>
</template>

<script>
import TreeRoot from '@/components/TreeRoot.vue';
import AddItem from '@/components/AddItem.vue';
import { ref, onMounted, reactive } from 'vue';

export default {
  components: {
    TreeRoot,
    AddItem,
  },
  setup() {
    const treeItems = ref([]); // Define the tree data structure here
    const submittedItems = ref([]);
    const showAddItem = ref(false);
    const rootButtonLabel = ref('Root'); // Default label for TreeRoot button
    const adminButtons = reactive([]); // Default labels for admin buttons


    // Load submittedItems from JSON Server on component mount
    onMounted(async () => {
      try {
        const response = await fetch('http://localhost:3000/submittedItems');
        if (response.ok) {
          submittedItems.value = await response.json();
        } else {
          console.error('Failed to fetch data from JSON Server:', response.statusText);
        }
      } catch (error) {
        console.error('Error fetching data from JSON Server:', error.message);
      }
    });

    const toggleAddItem = () => {
      showAddItem.value = !showAddItem.value;
    };

    const handleAddItemSubmit = async (data) => {
        try {
          const newItem = createNewItem(data);
          await saveNewItem(newItem);
          updateLocalState(newItem, data);
        } catch (error) {
          console.error('Error handling submitted item:', error.message);
        }
      };

      const createNewItem = (data) => {
        // Create a new item object based on the submitted data
        return {
          name: data.name,
          id: data.id,
          isParent: data.isParent,
          isChild: data.isChild,
          parentName: data.parentName !== '' ? data.parentName : null,
        };
      };

      const saveNewItem = async (newItem) => {
        // Send a POST request to JSON Server to save the new item
        const response = await fetch('http://localhost:3000/submittedItems', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(newItem),
        });

        if (!response.ok) {
          throw new Error(`Failed to save item: ${response.statusText}`);
        }

        console.log('Item saved successfully!');
      };

      const updateLocalState = (newItem, data) => {
        // Update the local submittedItems state with the new item
        submittedItems.value.push(newItem);
        // Dynamically set the root button label based on the form input
        rootButtonLabel.value = data.name;

        if (!Array.isArray(adminButtons.value)) {
          adminButtons.value = [];
        }

        // Dynamically set the admin buttons array based on the form input
        const index = adminButtons.findIndex((button) => button === data.name);
        if (index !== -1) {
          // If the name already exists, replace it
          adminButtons.value[index] = data.name;
        }
        console.log('Admin buttons updated:', adminButtons.value);
      };


    return {
      treeItems,
      submittedItems,
      showAddItem,
      rootButtonLabel,
      adminButtons,
      toggleAddItem,
      handleAddItemSubmit,
    };
  },
};
</script>


<style>
/* Basic styles for the RecursiveView component */

.recursive-view-container {
  max-width: 800px;
  margin: auto;
  padding: 20px;
}

.add-button {
  background-color: #3490dc;
  color: #fff;
  padding: 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-bottom: 10px;
}

.submitted-items {
  margin-top: 20px;
}

/* You can add more styles based on your design preferences */

</style>
