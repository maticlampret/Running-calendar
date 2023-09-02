<template>
    <div class="calendar">
        <div class="header">
            <button @click="prevMonth">&lt;</button>
            <h2>{{ currentMonth }}</h2>
            <button @click="nextMonth">&gt;</button>
        </div>
        <table class="calendar-table">
            <thead>
            <tr>
                <th v-for="day in daysOfWeek" :key="day">{{ day }}</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="week in calendar" :key="new Date(week[0]).getDate()">
                <td v-for="date in week" :key="new Date(date).getDate()" :class="{ 'today': isToday(date) }">
                    {{ new Date(date).getDate() }}
                    <div class="events">
                        <div v-for="event in getEventsForDate(date)" :key="event.id" class="event">{{ event.title }}</div>
                    </div>
                </td>
            </tr>
            </tbody>
        </table>
    </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const currentMonth = ref(new Date())
const daysOfWeek = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']

const events = ref([
    { id: 1, title: 'Event 1', date: '2023-09-10' },
    { id: 2, title: 'Event 2', date: '2023-09-15' },
    // Add more events here
])

const nextMonth = () => {
    const nextDate = new Date(currentMonth.value);
    nextDate.setMonth(nextDate.getMonth() + 1);
    currentMonth.value = nextDate
}

const prevMonth = () => {
    const nextDate = new Date(currentMonth.value);
    nextDate.setMonth(nextDate.getMonth() - 1);
    currentMonth.value.setMonth(currentMonth.value.getMonth() - 1)
    currentMonth.value = nextDate
}

const isToday = (date) => {
    const today = new Date()
    const dateTo = new Date(date)
    return dateTo.toDateString() === today.toDateString()
}

const getEventsForDate = (date) => {
    const dateTo = new Date(date)
    return events.value.filter((event) => event.date === dateTo.toISOString().split('T')[0])
}

const calendar = computed(() => {
    const year = currentMonth.value.getFullYear()
    const month = currentMonth.value.getMonth()
    console.log(month)
    const firstDay = new Date(year, month, 1)
    const lastDay = new Date(year, month + 1, 0)
    const daysInMonth = lastDay.getDate()
    const calendar = []
    let week = []

    for (let i = 0; i < firstDay.getDay(); i++) {
        week.push(null)
    }

    for (let day = 1; day <= daysInMonth; day++) {
        week.push(new Date(year, month, day))
        if (week.length === 7) {
            calendar.push(week)
            week = []
        }
    }

    if (week.length > 0) {
        while (week.length < 7) {
            week.push(null)
        }
        calendar.push(week)
    }

    return calendar
})
</script>

<style scoped>
.calendar {
    text-align: center;
    margin: 20px;
    font-family: Arial, sans-serif;
    width: 100%;
}

.header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
}

table {
    width: 100%;
    border-collapse: collapse;
}

table th,
table td {
    border: 1px solid #ccc;
    padding: 10px;
    text-align: center;
}

th {
    background-color: #474747;
}

td.today {
    background-color: #767676;
}

.events {
    display: flex;
    flex-direction: column;
}

.event {
    margin-top: 4px;
    background-color: #007bff;
    color: white;
    padding: 2px 4px;
    border-radius: 2px;
    font-size: 12px;
}

.calendar-table {
    height: 500px;
}
</style>
