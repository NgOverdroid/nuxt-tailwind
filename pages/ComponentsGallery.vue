<script setup>
  const toggleDatePicker = ref(false);

  window.addEventListener('click', () => {
    toggleDatePicker.value = !toggleDatePicker.value;
  })

  function handleToggle() {
    toggleDatePicker.value = !toggleDatePicker.value;
  }

  onUnmounted(() => {
    window.removeEventListener('click', handleToggle);
  })
</script>

<template>
  <div>
    <!--Date Picker-->
    <div>
      <button @click="handleToggle" class="border rounded-lg bg-gray-200 cursor-pointer w-16 p-2">
        <img src="/assests/svg/calendar-svgrepo-com.svg" alt="" class="w-8 h-8 m-auto">
      </button>
      <CalendarPicker v-show="toggleDatePicker" :bookedDates="{loading: true}" />
    </div>
  </div>
</template>


