<script setup>
import BookingItem from './components/BookingItem.vue';
import CourseItem from './components/CourseItem.vue';
import SkeletonCourseItem from '@/components/SkeletonCourseItem.vue';
import { ref, onMounted } from 'vue';
import SkeletonBookingItem from './components/SkeletonBookingItem.vue';

const courses = ref([]);
const bookings = ref([]);
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
  try {
    if (bookings.value.some((booking) => booking.id == course.id)) {
      alert('You have already registered for this course');
      return;
    }

    const newCourses = {
      id: course.id,
      title: course.title,
      price: course.price,
      description: course.description,
      status: 'pending',
    };

    const response = await fetch('http://localhost:3001/bookings', {
      method: 'POST',
      headers: { 'content-type': 'application/json' },
      body: JSON.stringify({
        ...newCourses,
        status: 'confirmed',
      }),
    });

    if (response.ok) {
      const index = bookings.value.findIndex((booking) => booking.id === course.id);
      bookings.value[index] = await response.json();
    }

  } catch (error) {
    console.error(error);
  }
};

const fetchBookings = async () => {
  loading.value = true;
  try {
    const response = await fetch('http://localhost:3001/bookings');
    bookings.value = await response.json();
  } catch (error) {
    console.error(error);
  } finally {
    loading.value = false;
  }
};

onMounted(() => {
  fetchCourses();
  fetchBookings();

  console.log(bookings.value.length);
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
    <div v-if="loading"><SkeletonBookingItem v-for="i in 2" :key="i"></SkeletonBookingItem></div>
    <div v-else class="grid grid-cols-1 gap-4">
      <BookingItem
        v-for="booking in bookings"
        :key="booking.id"
        :title="booking.title"
      ></BookingItem>
    </div>
  </div>
</template>
