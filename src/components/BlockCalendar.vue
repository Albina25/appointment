<template>
  <div class="calendar">
    <div class="align-row m0 mb15">
      <div v-for="month of months" :key="month" class="calendar__months" :class="{'calendar__months--active': activeMonth(month)}" @click="selectMonth(month)">{{ month }}</div>
    </div>
    <div class="align-row sum-space-month">
      <div class="calendar__current-month">{{ allMonths[currentMonth] }}</div>
      <div class="free-space">Свободных мест {{ allFreeSpaces }} из 1000</div>
    </div>
    <table class="calendar__table">
      <thead class="calendar__header">
        <tr>
          <td v-for="dayWeek in daysWeek" :key="dayWeek" class="calendar__day-week">{{ dayWeek }}</td>
        </tr>
      </thead>
      <tbody class="calendar__body">
        <tr v-for="(week, index) in days" :key="`week_${index}`">
          <td v-for="(day, index) in week" :key="`day_${index}`" class="calendar__td">
            <calendar-cell
              :day="day"
              :currentMonth="currentMonth"
              :employeesIdForDayAppointment="getEmployeesIdForDayAppointment(day.dayDate)"
              @countAllFreeSpaces="countSpaces"
              @addEmployeeIdInCalendar="changeFreeSpacesInCalendar"
            >
            </calendar-cell>
          </td>
        </tr>
      </tbody>
    </table>
    <div class="align-row">
      <div class="align-column contract-spaces">
        <span class="contract-spaces__text">Мест по договору</span>
        <span class="contract-spaces__count text-black">{{ contractSpaces }}</span>
      </div>
      <div class="align-column contract-spaces">
        <span class="contract-spaces__text">Записанных</span>
        <span class="contract-spaces__count text-blue">{{ contractBusySpaces }}</span>
      </div>
      <div class="align-column contract-spaces">
        <span class="contract-spaces__text">Свободных</span>
        <span class="contract-spaces__count text-green">{{ contractFreeSpaces }}</span>
      </div>
      <button class="button button-blue">Распределить автоматически 32</button>
      <button class="button button-red">Записать</button>
    </div>
  </div>
</template>

<script>
import CalendarCell from "./CalendarCell.vue";
import {departaments} from "../data/departaments";

export default {
  name: "BlockCalendar",
  components: {
    CalendarCell,
  },
  data() {
    return {
      departaments: departaments,
      countInTrolley: 0,
      days: [],
      appointments: [],
      allFreeSpaces: 1000,
      contractSpaces: 100,
      contractBusySpaces: 0,
      contractFreeSpaces: 100,
      date: new Date(),
      currentMonth: new Date().getMonth(),
      year: new Date().getFullYear(),
      daysWeek: ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"],
      allMonths: ["Январь", "Февраль", "Март", "Арпель", "Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь"],
      months: ["Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь"],
      lastDay: null,
    }
  },
  created() {
    this.showCalendar();
  },
  watch: {
    departaments: {
      deep: true,
      handler(data) {
        let count = 0;
        for (let i = 0; i < data.length; i++) {
          for (let j = 0; j < data[i].employees.length; j++) {
            if (data[i].employees[j].appointment) count++;
          }
        }
        this.contractBusySpaces = count;
        this.contractFreeSpaces = 100 - this.contractBusySpaces;
        this.allFreeSpaces = 1000 - this.contractBusySpaces;
      }
    }
  },
  methods: {
    getEmployeesIdForDayAppointment(day) {
      const dayAppointment = this.appointments.find(app => app.day === day);
      if (dayAppointment) return dayAppointment;
      else return null;
    },
    changeFreeSpacesInCalendar(employeeId, day) {
      this.addEmployeeIdInNewDay(employeeId, day);
      this.removeEmployeeIdInBeforeDay(employeeId, day);
    },
    addEmployeeIdInNewDay(employeeId, newDay) {
      if (this.appointments.length === 0) {
        this.appointments.push({day: newDay, id: [employeeId]});
      } else {
          const index = this.appointments.findIndex(app => app.day === newDay);
          if(index >= 0) {
            if (this.appointments[index].id.includes(employeeId))  return;
            this.appointments[index].id.push(employeeId);
          } else {
            this.appointments.push({day: newDay, id: [employeeId]});
          }
      }
    },
    removeEmployeeIdInBeforeDay(employeeId, newDay) {
      this.appointments.map(app => {
        if (app.id.includes(employeeId)) {
          if (app.day === newDay) return;

          const index = app.id.indexOf(employeeId);
          app.id.splice(index, 1);
        }
      });
    },
    activeMonth(month) {
      let index = this.allMonths.indexOf(month, 0);
      if (index === this.currentMonth) return true;
    },
    // countSpaces() {
    //   this.allFreeSpaces--;
    //   this.contractFreeSpaces--;
    //   this.contractBusySpaces++;
    // },
    showCalendar() {
      this.days = [];
      let week = 0;
      this.days[week] = [];
      let lastDay = new Date(this.year, this.currentMonth + 1, 0).getDate();
      this.lastDay = lastDay;
      for (let i = 1; i <= lastDay; i++) {
        if (new Date(this.year, this.currentMonth, i).getDay() !== 1) {
          const dayDate = {dayDate: new Date (this.year, this.currentMonth, i)};
          // const dayDate = {date: i, month: this.currentMonth, year: this.year};
          this.days[week].push(dayDate);
        } else {
          week++;
          this.days[week] = [];
          const dayDate = {dayDate: new Date (this.year, this.currentMonth, i)};
          // const dayDate = {date: i, month: this.currentMonth};
          this.days[week].push(dayDate);
        }
      }
      if (this.days[0].length > 0) {
        let lastDayBeforeMonth = new Date(this.year, this.currentMonth, 0).getDate();
        for (let i = this.days[0].length; i < 7; i++) {
          const dayDate = {dayDate: new Date (this.year, this.currentMonth-1, lastDayBeforeMonth)};
          // const dayDate = {date: lastDayBeforeMonth, month: this.currentMonth-1, year: this.year};
          this.days[0].unshift(dayDate);
          lastDayBeforeMonth--;
        }
      }

      if (this.days[week].length < 7) {
        let dateNextMonth = 1;
        for (let i = this.days[week].length; i < 7; i++) {
          const dayDate = {dayDate: new Date (this.year, this.currentMonth+1, dateNextMonth), id: []};
          dateNextMonth++;
          this.days[week].push(dayDate);
        }
      }
      return this.days;
    },
    selectMonth(month) {
      let index = this.allMonths.indexOf(month, 0);
      this.currentMonth = index;
      this.showCalendar();
    },
  },
}
</script>

<style scoped lang="scss">
.calendar {
  width: 100%;
  border-color: 1px solid var(--gray-60white);
  text-align: center;
  font-size: 14px;
  line-height: 21px;
  font-weight: 400;

  &__months:not(:last-child) {
    margin-right: 1.7em;
  }
  &__months {
    color: var(--gray-20white);
    cursor: pointer;

    &--active {
      color: var(--blue);
    }
  }
  &__current-month {
    font-weight: 500;
    font-size: 20px;
    line-height: 28px;
    color: var(--red);
  }
  &__table {
    margin-bottom: 40px;
  }
  &__day-week {
    justify-content: flex-start;
    text-align: left;
    width: calc(100% / 7);
  }
  &__td {
    width: 88px;
    height: 64px;
    border: 1px solid var(--gray-60white);
    margin-top: 8px;
    margin-left: 8px;
  }
}

.free-space {
  color: var(--green);
  font-size: 16px;
  line-height: 22px;
}

.inner-page {
  margin: 0 80px;
}

.sum-space-month {
  margin-bottom: 15px;
  justify-content: space-between;
}

.contract-spaces {
  margin-right: 20px;

  &:last-child {
    margin-right: 25px;
  }

  &__text {
    color: var(--gray-20white);
    font-size: 12px;
    line-height: 16px;
    letter-spacing: 0.02em;
  }

  &__count {
    font-weight: 500;
    font-size: 20px;
    line-height: 28px;
    text-align: left;
  }
}
</style>