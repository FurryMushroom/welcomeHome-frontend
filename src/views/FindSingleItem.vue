<template>
  <div>
    <!-- 输入框和按钮 -->
    <el-input v-model="itemID" placeholder="Enter Item ID"></el-input>
    <el-button @click="findItem">Find Item</el-button>

    <!-- 查询结果展示 -->
    <div v-if="hasQueried">
      <!-- 有 location 数据时 -->
      <div v-if="locations && locations.length">
        <h3>Locations</h3>
        <ul>
          <li v-for="(location, index) in locations" :key="index">
            {{ location }}
          </li>
        </ul>
      </div>
      <!-- 无 location 数据时 -->
      <div v-else>
        <el-alert title="No location information found for this item." type="warning" show-icon></el-alert>
      </div>
    </div>
  </div>
</template>



<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { ElMessage } from 'element-plus';

const itemID = ref(''); // 用户输入的 Item ID
const locations = ref(null); // 初始值为 null，表示未查询
const hasQueried = ref(false); // 是否进行了查询

const findItem = async () => {
  const token = localStorage.getItem('token');
  if (!token) {
    ElMessage.error('Please log in first!');
    return;
  }

  if (!itemID.value) {
    ElMessage.error('Please enter an Item ID!');
    return;
  }

  try {
    const response = await axios.get(`http://localhost:8080/items/find/${itemID.value}`, {
      headers: { token },
    });

    // 标记查询已完成
    hasQueried.value = true;

    // 处理 locations 数据
    if (response.data.locations) {
      locations.value = response.data.locations; // 有 location 信息
      ElMessage.success('Item found successfully!');
    } else {
      locations.value = null; // 没有 location 信息
      ElMessage.warning('Item found but no location information is available.');
    }
  } catch (error) {
    hasQueried.value = true; // 查询失败也标记为查询完成
    ElMessage.error('Failed to find item: ' + (error.response?.data || 'Unknown error'));
    locations.value = null; // 查询失败，重置数据
  }
};


</script>
