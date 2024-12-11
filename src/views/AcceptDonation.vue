<template>
  <div>
    <div v-if="isVerified">
      <el-input v-model="donorID" placeholder="Enter Donor ID"></el-input>
      <el-button @click="checkDonor">Verify Donor</el-button>
    </div>
    <div v-if="donorVerified">
      <el-input v-model="itemName" placeholder="Enter Item Name"></el-input>
      <el-input v-model="color" placeholder="Color"></el-input>
      <el-switch v-model="hasPieces" active-text="Has Pieces" inactive-text="No Pieces" />
<el-switch v-model="isNew" active-text="New" inactive-text="Not New" />
      <el-input v-model="material" placeholder="Material"></el-input>
      <el-input v-model="mainCategory" placeholder="Main Category"></el-input>
      <el-input v-model="subCategory" placeholder="Sub Category"></el-input>
      <el-button @click="acceptDonation">Accept Donation</el-button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import { ElMessage } from 'element-plus';
import DOMPurify from 'dompurify';

const staffID = localStorage.getItem('userName');
const donorID = ref('');
const itemName = ref('');
const isVerified = ref(false);
const donorVerified = ref(false);
const color=ref('');
const isNew =ref(false);
const   hasPieces= ref(false);
const material=ref('');
const mainCategory=ref('');
const subCategory=ref('');

const checkStaff = async () => {
  try {
    const response = await axios.get('http://localhost:8080/donations/isStaff', {
      params: {
        userName: staffID, // 不使用 staffID.value
      },
    });
    if (response.data.code > 0) {
      isVerified.value = true;
      ElMessage.success('Staff verified successfully!');
    } else {
      ElMessage.error('You are not a staff!');
    }
  } catch (error) {
    ElMessage.error('Failed to verify staff: ' + (error.response?.data || 'Unknown error'));
  }
};

const checkDonor = async () => {
  try {
    const response = await axios.get('http://localhost:8080/donations/isDonor', {
      params: {
        userName: donorID.value,
      },
    });
    if (response.data.code > 0) {
      donorVerified.value = true;
      ElMessage.success('Donor verified successfully!');
    } else {
      ElMessage.error('Donor not registered!');
    }
  } catch (error) {
    ElMessage.error('Failed to verify donor: ' + (error.response?.data || 'Unknown error'));
  }
};

const acceptDonation = async () => {
  try {
// 当用户点击提交（acceptDonation）时，对文本类输入进行清理
const sanitizedItemName = DOMPurify.sanitize(itemName.value);
const sanitizedColor = DOMPurify.sanitize(color.value);
const sanitizedMaterial = DOMPurify.sanitize(material.value);
const sanitizedMainCategory = DOMPurify.sanitize(mainCategory.value);
const sanitizedSubCategory = DOMPurify.sanitize(subCategory.value);

    const response = await axios.post('http://localhost:8080/donations/acceptDonations', {
  userName: donorID.value,
  iDescription: sanitizedItemName,
  color: sanitizedColor,
  isNew: isNew.value,
  hasPieces: hasPieces.value,
  material: sanitizedMaterial,
  mainCategory: sanitizedMainCategory,
  subCategory: sanitizedSubCategory,
});

    
    if (response.data.code > 0) {
      ElMessage.success('Donation accepted successfully!');
    } else {
      ElMessage.error('Failed to accept donation!');
    }
  } catch (error) {
    ElMessage.error('Failed to accept donation: ' + (error.response?.data || 'Unknown error'));
  }
};

onMounted(() => {
  checkStaff();
});
</script>
