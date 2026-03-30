<script setup>
import CardSections from '@/components/CardSections.vue';
import RoundedButton from '@/components/RoundedButton.vue';

defineProps({
  title: String,
  price: Number,
  description: String,
});

defineEmits(['click']);
</script>

<template>
  <CardSections>
    <template #header>{{ title }}</template>

    <template #default>{{ parseFloat(price).toFixed(2) }}</template>

    <template #footer>
      <p>{{ description }}</p>

      <div class="flex justify-end p-4">
        <RoundedButton @click="$emit('click')">Purchase</RoundedButton>
      </div>
    </template>
  </CardSections>
</template>
