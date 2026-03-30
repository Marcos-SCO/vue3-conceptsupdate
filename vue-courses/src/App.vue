<script setup>
import BookingItem from './components/BookingItem.vue';
import CourseItem from './components/CourseItem.vue';
import { ref, onMounted } from 'vue';

const courses = ref([]);
const loading = ref(false);

const fetchCourses = async () => {
  try {
    loading.value = true;
    const response = await fetch('http://localhost:3001/courses');
    courses.value = await response.json();
  } finally {
    loading.value = false;
  }
};

onMounted(() => {
  fetchCourses();
});
</script>

<template>
  <div class="p-4 space-y-8">
    <h1 class="txt-4x font-medium">Course Hub</h1>

    <h2 class="txt-2x font-medium">All Courses</h2>

    <div v-if="loading">Courses are loading...</div>
    <div v-else class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
      <CourseItem
        v-for="course in courses"
        :key="course.id"
        :title="course.title"
        :price="course.price"
        :description="course.description"
        @click="console.log('RAWWW')"
      />
    </div>

    <h3 class="txt-2x font-medium">Your Courses</h3>

    <div class="grid grid-cols-1 gap-4">
      <BookingItem v-for="i in 2" :key="i"></BookingItem>
    </div>
  </div>
</template>
