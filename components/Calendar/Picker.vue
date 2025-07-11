<script setup>
  const toggleDatePicker = ref(false);

  function handleToggle() {
    toggleDatePicker.value = !toggleDatePicker.value;
  }
</script>

<template>
  <div>
    <!--Date Picker-->
    <div>
      <button @click="handleToggle" class="border rounded-lg bg-gray-200 cursor-pointer w-16 p-2 relative">
        <img src="/assests/svg/calendar-svgrepo-com.svg" alt="" 
          class="w-8 h-8 m-auto filter hover:grayscale-0 transition duration-300"
          :class="[toggleDatePicker ? 'grayscale-0' : 'grayscale']"
          >  
      </button>
      <LazyCalendarForm 
        v-show="toggleDatePicker" 
        :bookedDates="{loading: true}"
        :hydrateOnVisible="true"
         />
    </div>
    <!--Star ReviewsW-->
  </div>
</template>


