<script setup>
import RoundedCard from '@/components/RoundedCard.vue';
import RoundedButton from '@/components/RoundedButton.vue';
</script>

<template>
  <RoundedCard>
    <div class="flex justify-between items-center">
      <div class="p-4">Vue Course</div>
      <RoundedButton @click="console.log('danger booking')" variant="danger">Report</RoundedButton>
    </div>
  </RoundedCard>
</template>
