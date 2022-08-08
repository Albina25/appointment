<template>
  <div :class="['cell', {'cell--free': freeSpaces > 0 && isCurrentMonth}]"
       @drop="onDrop($event, day)"
       @dragover.prevent
       @dragenter.prevent
  >
    <div class="cell__date">{{ date }}</div>
    <div v-if="isCurrentMonth && freeSpaces > 0" :class="['cell__info', {'text-yellow': freeSpaces <= 10}, {'text-green': freeSpaces > 10}]">
      {{ freeSpaces }}&nbsp;из&nbsp;50
    </div>
  </div>
</template>

<script>
import {departaments} from "../data/departaments";

export default {
  name: "CalendarCell",
  props: {
    day: Object,
    currentMonth: Number,
  },
  data() {
    return {
      freeSpaces: 50,
      freeSpaces1: 50,
      departaments: departaments,
    }
  },
  // watch: {
  //   departaments: {
  //     deep: true,
  //     handler(newdata, oldData) {
  //       // console.log('newdata', newdata[0].employees[0].appointment)
  //       // console.log('oldData', oldData[0].employees[0].appointment)
  //       // let count = 0;
  //       // for (let i = 0; i < data.length; i++) {
  //       //   for (let j = 0; j < data[i].employees.length; j++) {
  //       //     if (data[i].employees[j].appointment) count++;
  //       //   }
  //       // }
  //       // this.registeredEmployees = count;
  //     }
  //   }
  // },
  watch: {
    fff1(data) {
      // handler(data) {
        console.log('new', data);
        // this.freeSpaces1 = 50 - data.id.length;
      // },
      // deep: true,
      // immediate: true,
    },
  },
  computed: {
    fff1() {
      console.log('fff1', this.day.id.length)
      return 50 - this.day.id.length;
    },
    date() {
      return new Date(this.day.dayDate).getDate();
    },
    isCurrentMonth() {
      return this.currentMonth === new Date(this.day.dayDate).getMonth();
    },
  },
  methods: {
    fff() {
      console.log('111', this.day.id.length)
      return 50 - this.day.id.length;
    },
    formatMonth(month) {
      return (month+1 < 10 ? '0' : '') + (month+1);
    },
    countAllFreeSpaces(appointmentDate) {
      this.freeSpaces--;
      if (appointmentDate) {
        return;
      } else {
        this.$emit('countAllFreeSpaces');
      }
    },
    onDrop(evt, day) {
      const employeeID = evt.dataTransfer.getData('employeeID');
      const departamentID = evt.dataTransfer.getData('departamentID');

      const date = new Date(this.day.dayDate).getDate();
      const month = this.formatMonth(new Date(this.day.dayDate).getMonth());
      const year = new Date(this.day.dayDate).getFullYear();
      const appointmentDate = `${date}.${month}.${year}`;

      const indexDepartament = departaments.findIndex(dep => dep.id == departamentID);
      const indexEmployee = departaments[indexDepartament].employees.findIndex(i => i.id == employeeID);
      this.countAllFreeSpaces(departaments[indexDepartament].employees[employeeID-1].appointment);
      departaments[indexDepartament].employees[indexEmployee].appointment = appointmentDate;
      this.$emit('addEmployeeIdInCalendar', employeeID, day);
    },
  }
}
</script>

<style lang="scss" scoped>
.cell {
  background-color: var(--gray-80white);
  transition: all 0.1s ease-in;
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 100%;
  justify-content: flex-start;
  align-items: flex-start;
  padding: 8px;

  &--free {
    background-color: var(--white);
  }

  &__date {
    margin-bottom: 8px;
    color: var(--black-light);
  }

  &__info {
    font-weight: 400;
    font-size: 12px;
    line-height: 16px;
    letter-spacing: 0.02em;
  }
}
</style>