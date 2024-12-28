<template>
  <q-layout view="lHh Lpr lFf" class="bg-grey-5">
    <q-img src="bg2.jpg" class="fit absolute" />
    
    <q-page-container>
      <!-- Header Section -->
      <q-header elevated class="bg-primary text-white">
        <q-toolbar>
          <!-- Title Section -->
          <q-toolbar-title class="text-h5">
            <q-icon name="school" size="lg" class="q-mr-sm" />
            Student Management System
          </q-toolbar-title>
          
          <!-- Profile Icon Section (Aligned to Right) -->
          <div class="q-ml-auto">
            <q-btn flat icon="account_circle" size="lg" class="text-white" @click="toggleProfileCard">
              <!-- The profile card will show below this button -->
            </q-btn>
            
            <!-- Profile Card -->
            <div v-if="showProfileCard" class=" absolute z-index-10" style="top: 50px; right: 10px;">
              <q-card style="border: 2px solid black;">
                <q-card-section class="text-black">
                  <div class="rounded-full overflow-hidden" style="width: 150px; height: 150px;">
      <q-img src="demo1.jpg" class="fit cover" />
    </div>
                  <div class="text-h6">Profile Information</div>
                  <div><strong>Name:</strong> John Doe</div>
                  <div><strong>Email:</strong> johndoe@example.com</div>
                  <div><strong>Role:</strong> Student</div>
                </q-card-section>
                <q-card-actions>
                  <q-btn flat label="Close" color="negative" @click="closeProfileCard" />
                </q-card-actions>
              </q-card>
            </div>
          </div>
        </q-toolbar>
      </q-header>

      <!-- Main Content / Router View -->
      <RouterView />
    </q-page-container>
  </q-layout>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const showProfileCard = ref(false);

// Toggle the profile card visibility
const toggleProfileCard = () => {
  showProfileCard.value = !showProfileCard.value;
};

// Close the profile card
const closeProfileCard = () => {
  showProfileCard.value = false;
};
</script>
