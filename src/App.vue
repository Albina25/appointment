<template>
  <div class="app">
    <aside class="left-block">
      <a href="" class="logo">
        <img class="logo-img" src="./assets/images/logo.svg" alt="logo">
      </a>
    </aside>
    <div class="main-block">
      <header class="header">
        <div class="align-row margin_48">
          <div class="header__contract-data col-md-8">
            <a>
              <img src="./assets/icons/arrow-left.svg" alt="arrow">
            </a>
            <span class="contract-data margin-left_m">1205-20 от 01.01.2020</span>
          </div>
          <div class="header__company col-md-2">
            <img src="./assets/images/logo-technonikol.svg" alt="logo-technonikol">
            <span class="margin-left_s">ООО "Технониколь"</span>
          </div>
          <div class="header__trolley col-md-2">
            <a href="" class="">
              <img src="./assets/icons/trolley.svg" alt="trolley">
            </a>
            <div class="count-in-trolley">
              {{ countInTrolley }}
            </div>
          </div>
        </div>
      </header>
      <main>
        <div class="inner-page">
          <div class="align-row m80">
            <div class="block-calendar">
              <div class="block-calendar_wrapper">
                <h4 class="block-calendar__title">Предварительный медицинский осмотр лиц, поступающих в учебные заведения гражданской авиации (ГА)</h4>
                <block-calendar></block-calendar>
              </div>
            </div>
            <block-employees class="employees"></block-employees>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
import BlockCalendar from "./components/BlockCalendar.vue";
import BlockEmployees from "./components/BlockEmployees";

export default {
  name: "App",
  components: {
    BlockEmployees,
    BlockCalendar,
  },
  data() {
    return {
      countInTrolley: 0,
      // contractSpaces: 100,
      // contractBusySpaces: 0,
      // contractFreeSpaces: 0,
    }
  },
};
</script>

<style lang="scss">
.app {
  display: flex;
  flex-direction: row;
  height: 100%;
  width: 100%;

  .left-block {
    width: 96px;
    height: 100vh;
    border-right: 1px solid var(--gray-60white);
    background-color: var(--background-color);
    box-shadow: -5px -1px 10px -10px rgba(34, 60, 80, 0.6) inset;
  }

  .logo-img {
    margin: 40px 20px;
  }

  .main-block {
    width: 100%;
  }

  .header {
    padding: 40px 0 0;
    align-items: center;

    &__contract-data {
      line-height: 30px;
    }

    &__company {
    }

    &__trolley {
      display: flex;
      flex-direction: row;
      flex-wrap: nowrap;
    }
  }

  .contract-data {
    color: var(--gray-20white);
    font-size: 16px;
    font-weight: 500;
    line-height: 22px;
  }

  .count-in-trolley {
    display: flex;
    margin-left: 8px;
    width: 24px;
    height: 24px;
    background-color: var(--red);
    border-radius: 50%;
    color: var(--white);
    justify-content: center;
    align-items: center;
  }

  .block-calendar__title {
    font-size: 24px;
    line-height: 30px;
    margin-top: 0;
    margin-bottom: 24px;
  }

  .block-calendar {
    max-width: 619px;
    margin-right: 35px;

    &__months {
      display: flex;
      flex-direction: row;
      flex-wrap: nowrap;
    }
  }

  .block-calendar_wrapper {
    display: flex;
    flex-direction: column;
  }

  .employees {
    max-width: 33%;
  }
}
</style>
