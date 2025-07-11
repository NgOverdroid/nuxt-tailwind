<script setup>
  import dayjs from 'dayjs';

  const bookedDates = defineProps({});
  const currentPage = ref(0);
  const startDate = ref('');
  const endDate = ref('');
  const months = computed(() => {
    if (bookedDates?.loading)
      return;
    
    const months = new Array(24);
    const today = dayjs();

    for (let i = 0; i < 24; i++) {
      const firstDay = today.add(i, 'month').startOf('month');
      const totalDays = firstDay.daysInMonth();
      const blankDatesStart = new Array(firstDay.day());

      const days = Array.from({ length: totalDays }, (_, day) =>
        firstDay.add(day, 'day')
      );

      months[i] = {
        year: firstDay.year(),
        month: firstDay.month() + 1, // dayjs month is 0-based
        label: firstDay.format('MMMM YYYY'),
        blankDatesStart,
        days
      };
    }

    return months;
  });

  function formatDate(day) {
    return day.format('YYYY-MM-DD')
  };

  function inSelectedRange(day) {
    if (!startDate.value || !endDate.value) return '';

    const start = dayjs(startDate.value);
    const end = dayjs(endDate.value);
    const current = dayjs(day);

    return current.isAfter(start) && current.isBefore(end) ? 'bg-red-200' : '';
  };

  function isSelected(day){
    const dateStr = formatDate(day);
    return dateStr === startDate.value || dateStr === endDate.value ? 'bg-red-400' : '';
  };

  function toPrevPage() {
    currentPage.value > 0 && currentPage.value--;
  }

  function toNextPage(){
    currentPage.value < 23 && currentPage.value++;
  }

  function selectDate(e) {
    const button = e.target.closest('button[data-date]');
    if (!button) return;

    const selectedDate = button.getAttribute('data-date');

    if (!startDate.value) 
      startDate.value = selectedDate;
    else {
      const startDateObj = new Date(startDate.value);
      const endDateObj = new Date(selectedDate);

      if (startDateObj < endDateObj)
        endDate.value = selectedDate;
    }
  }

  function clearDate() {
    startDate.value = '';
    endDate.value = '';
  }
</script>
<template>
  <form class="min-h-[380px] flex flex-col justify-between">
    <input type="text" name="startDate" class="hidden" :value="startDate">
    <input type="text" name="endDate" class="hidden" :value="endDate">

    <div class="flex flex-row gap-5">
    <!-- Left Calendar -->
      <div id="left-calendar" class="w-[400px]">
      <!-- Header -->
      <div class="relative flex items-center justify-center mb-2">
        <button
          v-show="currentPage > 0"
          type="button"
          @click="toPrevPage"
          class="absolute left-0"
        >
          <img src="/assests/svg/arrow-left-circle-svgrepo-com.svg" class="w-6 h-6 cursor-pointer" />
        </button>
        <p class="text-lg font-semibold text-center">{{ months[currentPage].label }}</p>
      </div>

      <!-- Week Days -->
      <div class="grid grid-cols-7 text-center font-bold mb-1">
        <div>SUN</div>
        <div>MON</div>
        <div>TUE</div>
        <div>WED</div>
        <div>THU</div>
        <div>FRI</div>
        <div>SAT</div>
      </div>

      <!-- Days Grid -->
      <div class="grid grid-cols-7 gap-1" @click="selectDate($event)">
        <div v-for="_ in months[currentPage].blankDatesStart" class="w-10 h-10"></div>
        <button
          v-for="day in months[currentPage].days"
          :key="day.toISOString()"
          :data-date="formatDate(day)"
          :class="[
            isSelected(day),
            inSelectedRange(day),
            'w-10 h-10 flex items-center justify-center rounded-full border-2 border-white hover:border-red-500 transition duration-200 cursor-pointer'
          ]"          
          type="button"
          class="w-10 h-10 flex items-center justify-center rounded-full border-2 border-white hover:border-red-500 transition duration-200 cursor-pointer"
        >
          {{ day.date() }}
        </button>
      </div>
      </div>

    <!-- Right Calendar -->
      <div id="right-calendar" class="w-[400px]">
      <!-- Header -->
      <div class="relative flex items-center justify-center mb-2">
        <p class="text-lg font-semibold text-center">{{ months[currentPage + 1].label }}</p>
        <button
          v-show="currentPage < 23"
          type="button"
          @click="toNextPage"
          class="absolute right-0"
        >
          <img src="/assests/svg/arrow-right-svgrepo-com (1).svg" class="w-6 h-6 cursor-pointer"/>
        </button>
      </div>

      <!-- Week Days -->
      <div class="grid grid-cols-7 text-center font-bold mb-1">
        <div>SUN</div>
        <div>MON</div>
        <div>TUE</div>
        <div>WED</div>
        <div>THU</div>
        <div>FRI</div>
        <div>SAT</div>
      </div>

      <!-- Days Grid -->
      <div class="grid grid-cols-7 gap-1" @click="selectDate($event)">
        <div v-for="_ in months[currentPage + 1].blankDatesStart" class="w-10 h-10"></div>
        <button
          v-for="day in months[currentPage + 1].days"
          :key="day.toISOString()"
          :data-date="formatDate(day)"
          :class="[
            isSelected(day),
            inSelectedRange(day),
            'w-10 h-10 flex items-center justify-center rounded-full border-2 border-white hover:border-red-500 transition duration-200 cursor-pointer'
          ]"
          type="button"
          class="w-10 h-10 flex items-center justify-center rounded-full border-2 border-white hover:border-red-500 transition duration-200 cursor-pointer"
        >
          {{ day.date() }}
        </button>
      </div>
      </div>
    </div>
    <button @click="clearDate" class="border-2 rounded-sm bg-red-400 text-white cursor-pointer w-16 p-2" type="button">
      Clear
    </button>
  </form>
</template>


