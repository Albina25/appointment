<template>
  <div class="employees align-column">
    <div class="align-row m45">
      <button class="button button-red">Добавить сотрудников</button>
      <button class="button button-white">
        <img class="trash" src="../assets/icons/basket-delete.svg">
        Удалить всех
      </button>
    </div>
    <div class="employees-header">
      <span class="employees-header__all-employees text-black">Список сотрудников {{ allEmployees }}</span>
      <span class="employees-header__registered-employees text-blue">Записанных {{ registeredEmployees }}</span>
    </div>
    <div class="align-row employees-table__header">
      <span class="mr21">ФИО</span>
      <span>Дата записи</span>
    </div>
    <table v-for="departament of departaments" :key="departament.id" class="mb10 draggable">
      <thead class="pb20">
        <tr>
          <th class="employees-table__departament" colspan="2">
            <img class="employees-table__icon-list-item" src="../assets/icons/list-item.svg">
            <span class="mr5">{{ departament.departamentName }}</span>
            <span class="employees-table__sumEmployees">{{ employeesInDepartament(departament) }}</span>
          </th>
          <th class="employees-table__arrow" @click="hideEmployees(departament)">
            <img src="../assets/icons/arrow-down.svg">
          </th>
        </tr>
      </thead>
      <tbody class="employees-table__body" onselectstart="return false">
        <tr
          v-for="employee of departament.employees"
          :key="employee.id"
          class="employees-table__tr"
          employee.draggable="true"
          @dragstart="startDrag($event, employee, departament)"
        >
          <td class="employees-table__fio">
            <span><img class="employees-table__icon-list-item" src="../assets/icons/list-item.svg"></span>
            <span>{{ employee.name }}</span>
            </td>
          <td class="employees-table__appointment">{{ formatAppointment(employee.appointment) }}</td>
          <td class="employees-table__trash" @click="deleteEmployee(departament.employees, employee)">
            <img src="../assets/icons/basket-delete.svg" class="test">
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import {departaments} from "../data/departaments";

export default {
  name: "BlockEmployees",
  data() {
    return {
      allEmployees: 40,
      registeredEmployees: 0,
      departaments: departaments,
    }
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
        this.registeredEmployees = count;
      }
    }
  },
  methods: {
    startDrag(evt, employee, departament) {
      evt.dataTransfer.dropEffect = 'move'
      evt.dataTransfer.effectAllowed = 'move'
      evt.dataTransfer.setData('employeeID', employee.id);
      evt.dataTransfer.setData('departamentID', departament.id);
    },
    formatAppointment(appointment) {
      if (appointment === '') return '-.--.----';
      else return appointment;
    },
    employeesInDepartament(departament) {
      return departament.employees.length;
    },
    deleteEmployee(dep, employeeId) {
      let index = dep.indexOf(employeeId);
      dep.splice(index, 1);
    },
    hideEmployees(dep) {
      const id = departaments.find(departament => departament === dep).id;
      const element = document.querySelectorAll(".employees-table__body")[id-1];
      element.classList.toggle("hidden");
    },
  },
}
</script>

<style lang="scss" scoped>
.employees {
  width: 384px;

  .employees-header {
    display: flex;
    flex-flow: row nowrap;
    justify-content: space-between;
    margin-bottom: 0.5rem;

    &__all-employees {
      font-size: 20px;
      line-height: 28px;
      font-weight: 500;
    }

    &__registered-employees {
      font-size: 16px;
      line-height: 22px;
      font-weight: 500;
    }
  }
}

.employees-table {
  height: 100px;
  overflow-x: auto;

  &__header {
    display: flex;
    align-items: center;
    font-size: 12px;
    line-height: 16px;
    letter-spacing: 0.02em;
    color: var(--gray-20white);
    padding: 8px 5px;
    border-bottom: 1px solid var(--gray-60white);
  }

  &__departament {
    font-size: 16px;
    line-height: 22px;
    font-weight: 500;
    color: var(--black-light);
    padding: 15px 0;
  }

  &__appointment {
    font-weight: 400;
    font-size: 14px;
    line-height: 21px;
    color: var(--black-light);
  }

  &__arrow {
    width: 20px;
  }

  &__sumEmployees {
    color: var(--red);
  }

  &__icon-list-item {
    margin-right: 10px;
  }

  &__tr {
    border-bottom: 1px solid var(--gray-60white);

    &:hover{
      background-color: var(--gray-25white);

      .employees-table__trash {
        opacity: 1;
      }
    }
  }

  &__fio {
    cursor: pointer;
    padding: 8px 0;
    padding-left: 16px;
    width: 60%;
  }

  &__trash {
    cursor: pointer;
    width: 20px;
    opacity: 0;
  }
}

.hidden {
  display: none;
}
</style>