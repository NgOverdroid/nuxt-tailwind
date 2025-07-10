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
            const blankDatesEnd = new Array(35 - (totalDays + blankDatesStart.length));

            const days = Array.from({ length: totalDays }, (_, day) =>
                firstDay.add(day, 'day')
            );

            months[i] = {
                year: firstDay.year(),
                month: firstDay.month() + 1, // dayjs month is 0-based
                label: firstDay.format('MMMM YYYY'),
                blankDatesStart,
                blankDatesEnd,
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
    <form class="flex flex-row">
        <input type="text" class="hidden" v-model="startDate">
        <input type="text" class="hidden" v-model="endDate">
        <div id="left-calendar">
            <!--Navigation bar-->
            <div>
                <button type="button" @click="toPrevPage"></button>
                <p>{{ `${months[currentPage].month} ${months[currentPage].year}` }}</p>
            </div>
            <!--Days in a week-->
            <div class="flex flex-row no-wrap">
                <div>SUN</div>
                <div>MON</div>
                <div>TUE</div>
                <div>WED</div>
                <div>THU</div>
                <div>FRI</div>
                <div>SAT</div>
            </div>
            <!--Days in a month-->
            <div @click="selectDate($event)">
                <div v-for="_ in months[currentPage].blankDatesStart"></div>
                <div v-for="day in months[currentPage].days" :key="day.toISOString()">
                    {{ day.getDate() }}
                </div> 
                <div v-for="_ in months[currentPage].blankDatesEnd"></div>           
            </div>
        </div>
        <div id="right-calendar">
            <!--Navigation bar-->
            <div>
                <p>{{ `${months[currentPage + 1].month} ${months[currentPage + 1].year}` }}</p>
                <button type="button" @click="toNextPage"></button>
            </div>
            <!--Days in a week-->
            <div class="flex flex-row no-wrap">
                <div>SUN</div>
                <div>MON</div>
                <div>TUE</div>
                <div>WED</div>
                <div>THU</div>
                <div>FRI</div>
                <div>SAT</div>
            </div>
            <!--Days in a month-->
            <div @click="selectDate($event)">
                <div v-for="_ in months[currentPage + 1].blankDatesStart"></div>
                <div v-for="day in months[currentPage + 1].days" :key="day.toISOString()">
                    {{ day.getDate() }}
                </div> 
                <div v-for="_ in months[currentPage + 1].blankDatesEnd"></div>           
            </div>
        </div>
    </form>
</template>