<script setup lang="ts">
import CalendarPicker from './components/CalendarPicker/CalendarPicker.vue';

</script>

<template>
  <div>
    <NuxtRouteAnnouncer />
    <NuxtWelcome />
    <CalendarPicker :bookedDates="{loading: true}"/>
  </div>
</template>
