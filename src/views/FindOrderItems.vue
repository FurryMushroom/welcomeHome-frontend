<template>
  <div>
    <el-input v-model="orderID" placeholder="Enter Order ID"></el-input>
    <el-button @click="findOrder">Find Order</el-button>
    <div v-if="items">
      <h3>Order Items:</h3>
      <!-- 物品列表 -->
    <div v-if="items.length > 0" style="margin-top: 20px;">
      <el-table :data="items" style="width: 100%">
        <el-table-column prop="itemID" label="Item ID" />
        <el-table-column prop="idescription" label="Description" width="170"  />
        <el-table-column prop="roomNum" label="Room Number" />
        <el-table-column prop="shelfNum" label="Shelf Number" />
        <el-table-column prop="shelfDescription" label="Shelf Description" width="150"/>
        <el-table-column prop="pDescription" label="Piece Description" width="150"  />
        <el-table-column prop="pieceNum" label="Number of Pieces" />
      </el-table>
    </div>
      
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { ElMessage } from 'element-plus';
import DOMpurify from 'dompurify';
const orderID = ref('');
const items = ref([]);

const findOrder = async () => {
  const token = localStorage.getItem('token');
  if (!token) {
    ElMessage.error('Please log in first!');
    return;
  }

  if (!orderID.value) {
    ElMessage.error('Please enter an Order ID!');
    return;
  }
  try {
    const response = await axios.get(`http://localhost:8080/items/order/${DOMpurify.sanitize(orderID.value)}`, {
      headers: { token },
    });
    if(response.data.code>0)
    {
    items.value = response.data.data;
    ElMessage.success('Order found successfully!');}
    else {
      ElMessage.warning('No item found!');
    }
  } catch (error) {
    ElMessage.error('Failed to find order: ' + (error.response?.data || 'Unknown error'));
  }
};
</script>
