<template>
  <div>
    <el-input v-model="staffID" placeholder="Enter Staff ID"></el-input>
    <el-button @click="checkStaff">Verify Staff</el-button>
    <div v-if="isVerified">
      <el-input v-model="donorID" placeholder="Enter Donor ID"></el-input>
      <el-button @click="checkDonor">Verify Donor</el-button>
    </div>
    <div v-if="donorVerified">
      <el-input v-model="itemName" placeholder="Enter Item Name"></el-input>
      <el-input v-model="itemLocation" placeholder="Enter Item Location"></el-input>
      <el-button @click="acceptDonation">Accept Donation</el-button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { ElMessage } from 'element-plus';

const staffID = ref('');
const donorID = ref('');
const itemName = ref('');
const itemLocation = ref('');
const isVerified = ref(false);
const donorVerified = ref(false);

const checkStaff = async () => {
  try {
    const response = await axios.get(`http://localhost:8080/donations/isStaff`,{
      params:{
userName: staffID.value,
      },
    });
    if (response.data.code>0) {
      isVerified.value = true;
      ElMessage.success('Staff verified successfully!');
    } else {
      ElMessage.error('Invalid staff ID!');
    }
  } catch (error) {
    ElMessage.error('Failed to verify staff: ' + (error.response?.data || 'Unknown error'));
  }
};

const checkDonor = async () => {
  try {
    const response = await axios.get(`http://localhost:8080/donations/isDonor`,{
      params:{
userName: donorID.value,
      },
    });
    if (response.data.code>0) {
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
    await axios.post('http://localhost:8080/donations/acceptDonations', {
      donorID: donorID.value,
      itemName: itemName.value,
      itemLocation: itemLocation.value,
    });
    ElMessage.success('Donation accepted successfully!');
  } catch (error) {
    ElMessage.error('Failed to accept donation: ' + (error.response?.data || 'Unknown error'));
  }
};
</script>
