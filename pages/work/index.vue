<template>
  <div class="all-box">
    <div class="sidebar">
      <CalendarSidebar @searchValue="onSearch" />
    </div>
    <div class="calendar">
      <CalendarWorkList :searchValue="searchKey" />
      <!-- <TableCustom :searchValue="searchKey" /> -->
    </div>
  </div>
</template>

<script>
import TableCustom from "~/components/TableCustom";
import CalendarWorkList from "~/components/CalendarWorkList";
import CalendarSidebar from "~/components/CalendarSidebar";

export default {
  name: "table",
  components: {
    TableCustom,
    CalendarSidebar,
  },
  data() {
    return {
      searchKey: "",
    };
  },
  watch: {
    searchKey: {
      handler(value) {
      },
    },
  },
  methods: {
    onSearch(data) {
      this.searchKey = data;
    },
    dates(current) {
      var week = new Array();
      // Starting Monday not Sunday
      current.setDate((current.getDate() - current.getDay() + 1));
      for (var i = 0; i < 7; i++) {
        week.push(
          new Date(current)
        );
        current.setDate(current.getDate() + 1);
      }
      return week;
    }
  },
};
</script>

<style lang="scss" scoped>
.all-box {
  width: 100%;
  height: 100%;
  display: grid;
  grid-template-columns: 1fr 3fr;

  .sidebar {}

  .calendar {
    margin: 4px;
  }
}
</style>
