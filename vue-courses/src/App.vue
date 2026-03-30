<script setup>
import BookingItem from './components/BookingItem.vue';
import CourseItem from './components/CourseItem.vue';
import { ref, onMounted } from 'vue';
import SkeletonCourseItem from '@/components/SkeletonCourseItem.vue';

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

const registerCourse = async (course) => {
  const newCourses = {
    id: Date.now().toString(),
    title: course.title,
    price: course.price,
    description: course.description,
    status: 'pending',
  };

  await fetch('http://localhost:3001/courses', {
    method: 'POST',
    headers: { 'content-type': 'application/json' },
    body: JSON.stringify({
      ...newCourses,
      status: 'pending',
    }),
  });
};

onMounted(() => {
  fetchCourses();
});
</script>

<template>
  <div class="p-4 space-y-8">
    <h1 class="txt-4x font-medium">Course Hub</h1>

    <h2 class="txt-2x font-medium">All Courses</h2>

    <div v-if="loading" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
      <SkeletonCourseItem v-for="i in 4" :key="i"></SkeletonCourseItem>
    </div>
    <div v-else class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
      <CourseItem
        v-for="course in courses"
        :key="course.id"
        :title="course.title"
        :price="course.price"
        :description="course.description"
        @click="registerCourse(course)"
      />
    </div>

    <h3 class="txt-2x font-medium">Your Courses</h3>
    <div class="grid grid-cols-1 gap-4">
      <BookingItem v-for="i in 2" :key="i"></BookingItem>
    </div>
  </div>
</template>
