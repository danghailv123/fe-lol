<template>
  <div class="sidebar">
    <div class="user"></div>
    <div class="workday">
      <span class="workday-title">Works </span>
      <div class="workday-box">
        <div class="status">
          <span class="workday-name"> Work At Company</span>
          <span class="workday-color Company"></span>
        </div>
        <div class="status">
          <span class="workday-name">Work at Home</span>
          <span class="workday-color Home"></span>
        </div>
        <div class="status">
          <span class="workday-name">Go On Bussiness</span>
          <span class="workday-color Bussiness"></span>
        </div>
        <div class="status">
          <span class="workday-name">Leave</span>
          <span class="workday-color Leave"></span>
        </div>
      </div>
    </div>
    <form class="filter">
      <a-form-item label="Head Quarter" style="width: 100%">
        <a-select style="width: 100%;"
          v-model="headQuarterId"
        v-decorator="[
          'headQuarterId',
          {
            rules: [
              {
                required: true,
                message: 'Please select head quarter!',
              },
            ],
          },
        ]">
          <a-select-option v-for="option in headQuarter" :key="option.id" :value="option.id">{{ option.name
          }}</a-select-option>
        </a-select>
      </a-form-item>
      <a-form-item label="Work Place" style="width: 100%">
        <a-select style="width: 100%;"
        v-model="workPlaceId"
        v-decorator="[
          'workPlaceId',
          {
            rules: [
              {
                required: true,
                message: 'Please select work place!',
              },
            ],
          },
        ]">
          <a-select-option v-for="option in workPlace" :key="option.id" :value="option.id">{{ option.name
          }}</a-select-option>
        </a-select>
      </a-form-item>
      <a-form-item label="Work Type" style="width: 100%">
        <a-select style="width: 100%;"
        v-model="workTypeId"
        v-decorator="[
          'workTypeId',
          {
            rules: [
              {
                required: true,
                message: 'Please select work type!',
              },
            ],
          },
        ]">
          <a-select-option v-for="option in workType" :key="option.id" :value="option.id">{{ option.name
          }}</a-select-option>
        </a-select>
      </a-form-item>
      <div class="search">
        <a-input-search placeholder="Enter Employee Name..." enter-button="Search"  @search="onSearch"/>
      </div>
    </form>
  </div>
</template>

<script>
import CustomSelect from "~/components/CustomSelect";
import PersonService from "../../services/api/personService";

export default {
  name: "CalendarSidebar",
  comments: [CustomSelect],
  data: () => ({
    userOptions: ["Nhân Viên A", "Nhân Viên D", "Nhân Viên C", "Nhân Viên D"],
    loading: false,
    headQuarterId: "All",
    workPlaceId: "All",
    workTypeId: "All",
    headQuarter: [],
    workPlace: [],
    workType: [],
  }),
  fetch() {
    Promise.all([
      this.getHeadQuaters(),
      this.getWorkPlaces(),
      this.getWorkTypes(),
    ]);
  },
  methods: {
    onSearch(value) {
      const workPlaceId = this.workPlace.find((item) => item.id == this.workPlaceId)
      const headQuarterId = this.headQuarter.find((item) => item.id == this.headQuarterId)
      const workTypeId = this.workType.find((item) => item.id == this.workTypeId)
      const dataSearch = value+';'+(workPlaceId !== null ? workPlaceId?.name : 'all') + ';'+(headQuarterId !== null ? headQuarterId?.name : 'all')+ ';'+(workTypeId !== null ? workTypeId?.name : 'all');
      this.$emit("searchValue", dataSearch);
    },
    updateValue(event){
      this.$emit('update', event);
    },
    async getHeadQuaters(){
      try{
        const res = await PersonService.get(
          "user/working-schedule/get-headquarters"
        );
        const listHeadQuater = res.data.data;
        listHeadQuater.unshift({
          id: listHeadQuater.length + 1,
          name: "All"
        })
        this.headQuarter = listHeadQuater;
        localStorage.setItem("headQuarter",JSON.stringify(this.headQuarter));
      }catch(error){
        console.log(error);
      }
    },
    async getWorkPlaces(){
      try{
        const res = await PersonService.get(
          "user/working-schedule/get-workplaces"
        );
        const listWorkPlace = res.data.data;
        listWorkPlace.unshift({
          id: listWorkPlace.length + 1,
          name: "All"
        })
        this.headQuarter = listWorkPlace;
        this.workPlace = res.data.data;
        localStorage.setItem("workPlace",JSON.stringify(this.workPlace));
      }catch(error){
        console.log(error);
      }
    },
    async getWorkTypes() {
      try {
        const res = await PersonService.get(
          "user/working-schedule/get-worktypes"
        );
        const listWorkType = res.data.data;
        listWorkType.unshift({
          id: listWorkType.length + 1,
          name: "All"
        })
        this.workType = listWorkType;
        localStorage.setItem("workType", JSON.stringify(this.workType));
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>
<style lang="scss" scoped>
.sidebar {
  padding: 10px 12px;

  .Company {
    background-color: Red;
  }

  .Home {
    background-color: Green;
  }

  .Bussiness {
    background-color: #2196F3;
  }

  .Leave {
    background-color: Purple;
  }

  .user {}

  .workday {
    .workday-title {
      font-size: 24px;
      margin-bottom: 8px;
    }

    .workday-box {
      margin-left: 16px;

      .status {
        display: flex;
        gap: 6px;
        align-items: center;

        .workday-name {
          font-size: 16px;
        }

        .workday-color {
          width: 8px;
          height: 8px;
          border-radius: 50%;
        }
      }
    }
  }

  .search {
    .search-btn {
      background-color: rgb(59, 152, 240);
    }
  }
}
</style>
