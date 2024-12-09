<template>
  <div>
    <el-input v-model="orderID" placeholder="Enter Order ID"></el-input>
    <el-button @click="findOrder">Find Order</el-button>
    <div v-if="items">
      <h3>Order Items:</h3>
      <ul>
        <li v-for="item in items" :key="item.id">
          {{ item.name }} - Locations: {{ item.locations.join(', ') }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { ElMessage } from 'element-plus';

const orderID = ref('');
const items = ref(null);

const findOrder = async () => {
  if (!orderID.value) {
    ElMessage.error('Please enter an Order ID!');
    return;
  }
  try {
    const response = await axios.get(`http://localhost:8080/orders/${orderID.value}`);
    items.value = response.data.items;
    ElMessage.success('Order found successfully!');
  } catch (error) {
    ElMessage.error('Failed to find order: ' + (error.response?.data || 'Unknown error'));
  }
};
</script>
