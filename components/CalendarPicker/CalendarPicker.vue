<script setup>
    import dayjs from 'dayjs';
    import LeftArrow from '../Icons/LeftArrow.vue';
    import RightArrow from '../Icons/RightArrow.vue';
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

    const toPrevPage = () => {
        currentPage.value > 0 && currentPage.value--;
    }

    const toNextPage = () => {
        currentPage.value < 23 && currentPage.value++;
    }

    const selectDate = (e) => {
        if(!startDate.value)
            startDate = e.target.textContent;
        else if (!endDate.value)
            endDate.value = e.target.textContent;
    }
</script>
<template>
  <form class="flex flex-row gap-5">
    <input type="text" class="hidden" v-model="startDate">
    <input type="text" class="hidden" v-model="endDate">

    <!-- Left Calendar -->
    <div id="left-calendar" class="w-[400px]">
      <!-- Header -->
      <div class="flex justify-between items-center mb-2">
        <button v-if="currentPage > 0" type="button" @click="toPrevPage">
            <LeftArrow/>
        </button>
        <p class="text-lg font-semibold">{{ months[currentPage].label }}</p>
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
          type="button"
          class="w-10 h-10 flex items-center justify-center rounded-full border border-gray-300 hover:border-blue-500 transition duration-200"
        >
          {{ day.date() }}
        </button>
      </div>
    </div>

    <!-- Right Calendar -->
    <div id="right-calendar" class="w-[400px]">
      <!-- Header -->
      <div class="flex justify-between items-center mb-2">
        <p class="text-lg font-semibold">{{ months[currentPage + 1].label }}</p>
        <button v-if="currentPage < 23" type="button" @click="toNextPage">
            <RightArrow/>
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
          type="button"
          class="w-10 h-10 flex items-center justify-center rounded-full border border-gray-300 hover:border-blue-500 transition duration-200"
        >
          {{ day.date() }}
        </button>
      </div>
    </div>
  </form>
</template>


