<template>
  <div>
    <el-input v-model="orderID" placeholder="Enter Order ID"></el-input>
    <el-button @click="findOrder">Find Order</el-button>
    <div v-if="items">
      <h3>Order Items:</h3>
      <!-- 物品列表 -->
    <div v-if="items.length > 0" style="margin-top: 20px;">
      <el-table :data="items" style="width: 100%">
        <el-table-column prop="idescription" label="Description" width="230"  />
        <el-table-column prop="material" label="Material" />
        <el-table-column prop="color" label="Color" />
        <el-table-column prop="new" label="Is New" />
        <el-table-column label="Actions" width="150">
          <template #default="scope">
            <el-button
              type="primary"
              size="small"
              @click="addToOrder(scope.row)"
            >
              Add to Order
            </el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
      
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
    const response = await axios.get(`http://localhost:8080/items/order`,{
params:{
  orderid:orderID.value,
}
    });
    items.value = response.data.items;
    ElMessage.success('Order found successfully!');
  } catch (error) {
    ElMessage.error('Failed to find order: ' + (error.response?.data || 'Unknown error'));
  }
};
</script>
