<script setup>
import BookingItem from './components/BookingItem.vue';
import CourseItem from './components/CourseItem.vue';
</script>

<template>
  <div class="p-4 space-y-8">
    <h1 class="txt-4x font-medium">Course Hub</h1>

    <h2 class="txt-2x font-medium">All Courses</h2>

    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
      <CourseItem
        v-for="i in 1"
        :key="i"
        title="Vue Course"
        price="9.99"
        description="Learn Vue.js from scratch."
        @click="console.log('RAWWW')"
      />
    </div>

    <h3 class="txt-2x font-medium">Your Courses</h3>

    <div class="grid grid-cols-1 gap-4">
      <BookingItem v-for="i in 2" :key="i"></BookingItem>
    </div>

  </div>
</template>
