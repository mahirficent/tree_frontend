<!-- TreeNode.vue -->

<template>
    <div class="tree-node">
      <div class="node-content">
        {{ item.name }}
      </div>
      <TreeRoot v-if="item.children && item.children.length > 0" :items="item.children" />
    </div>
  </template>
  
  <script>
  import TreeRoot from '@/components/TreeRoot.vue';
  
  export default {
    props: {
      item: {
        type: Object,
        required: true,
      },
    },
    components: {
      TreeRoot,
    },
  };
  </script>
  
  <style scoped>
  .tree-node {
    display: flex;
    flex-direction: column;
    margin: 5px;
  }
  
  .node-content {
    background-color: #42b883;
    color: #fff;
    padding: 8px;
    border-radius: 4px;
    cursor: pointer;
    margin-bottom: 5px;
  }
  
  /* Add styling for TreeRoot component if needed */
  </style>
 
  