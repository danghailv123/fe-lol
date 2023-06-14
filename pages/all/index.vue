<template>
  <div class="all-box">
    <div class="sidebar">
      <CalendarSidebar :search="handleSearch" v-model.sync="searchData" />
    </div>
    <div class="calendar">
      <CalendarCustom />
    </div>
  </div>
</template>

<script>
import CalendarCustom from '~/components/CalendarCustom'
import CalendarSidebar from '~/components/CalendarSidebar'
export default {
  name: 'all',
  components: {
    CalendarCustom,
    CalendarSidebar
  },
  data() {
    return {
      searchData: "rgewg",
    }
  },
  methods: {
    handleSearch(){
      console.log();
    }
  },
  watch: {
    searchData: function (val) {
    },
  },
}
</script>

<style lang="scss" scoped>
.all-box {
  width: 100%;
  height: 100%;
  display: grid;
  grid-template-columns: 1fr 3fr;
  .sidebar{
  }

  .calendar {
    margin: 4px;
  }

}
</style>
