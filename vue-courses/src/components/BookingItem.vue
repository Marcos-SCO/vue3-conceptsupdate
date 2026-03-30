<script setup>
import RoundedCard from '@/components/RoundedCard.vue';
import RoundedButton from '@/components/RoundedButton.vue';

defineProps({
  title: String,
});
</script>

<template>
  <RoundedCard>
    <div class="flex justify-between items-center p-4">
      <div>{{ title }}</div>
      <RoundedButton @click="console.log('danger booking')" variant="danger">Report</RoundedButton>
    </div>
  </RoundedCard>
</template>
