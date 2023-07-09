<template>
  <v-row class="fill-height">
    <v-col cols="3" class="inf_all">
      <div>
        <div>
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
          <a-button type="primary" @click="showModal"> Create Event </a-button>
          <a-modal width="600px" v-model="visible" title="Create Event" :footer="null">
            <a-form class="form-create" :form="form" @submit="handleSubmit">
              <a-form-item label="Title">
                <a-input v-decorator="[
                  'title',
                  {
                    rules: [
                      {
                        required: true,
                        message: 'Please input your Title!',
                      },
                    ],
                  },
                ]" />
              </a-form-item>
              <a-form-item label="Description">
                <a-input v-decorator="['description']" />
              </a-form-item>
              <a-form-item label="Work Date">
                <a-date-picker v-decorator="['workDate', config]" />
              </a-form-item>
              <a-form-item label="Start Time">
                <a-time-picker v-decorator="['startTime', config]" format="HH:mm" />
              </a-form-item>
              <a-form-item label="End Time">
                <a-time-picker v-decorator="['endTime', config]" format="HH:mm" />
              </a-form-item>
              <a-form-item label="Shift">
                <a-select v-decorator="[
                  'shiftId',
                  {
                    rules: [
                      {
                        required: true,
                        message: 'Please select shift!',
                      },
                    ],
                  },
                ]">
                  <a-select-option v-for="option in shiftOptions" :key="option.id" :value="option.id">{{ option.name
                  }}</a-select-option>
                </a-select>
              </a-form-item>
              <a-form-item label="Work Type">
                <a-select @change="handleChangeWorkType" v-decorator="[
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
              <a-form-item label="Head Quarter">
                <a-input style="pointer-events: none;" readOnly v-model='headQuarterIdUser' />
              </a-form-item>
              <a-form-item label="Work Place">
                <a-select v-bind:class="(disableWorkPlace) ? 'disable' : ''" v-decorator="[
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
              <a-form-item>
                <a-button type="primary" html-type="submit"> Create </a-button>
              </a-form-item>
            </a-form>
          </a-modal>
          <a-card style="margin-top: 30px;">
            <div class="mb-4 mx-a">
              <b-avatar variant="info" v-bind:src="data.link_image !== null ? data.link_image : ''"></b-avatar>
            </div>
            <p>Fullname: {{ data.fullName }}</p>
            <p>Birthday: {{ data.birthDay }}</p>
            <p>Address: {{ data.address }}</p>
            <p>Phone number: {{ data.phoneNumber }}</p>
            <p>Role: {{ role }}</p>
            <p>Head Quarter: {{ headQuarterIdUser }}</p>
            <p>Sex: {{ sex }}</p>
            <p>Description: {{ data.description }}</p>
          </a-card>
        </div>
      </div>
    </v-col>
    <v-col cols="9">
      <CalendarCustom />
    </v-col>
  </v-row>
</template>

<script>
import CalendarCustom from "~/components/CalendarCustom";
import PersonService from "../services/api/personService";
import today from "@/utils/getDaysBetween.js";
export default {
  components: {
    CalendarCustom,
  },

  data: () => ({
    visible: false,
    config: {
      rules: [
        { type: "object", required: true, message: "Please select time!" },
      ],
    },
    shiftOptions: [],
    workType: [],
    headQuarter: [],
    workPlace: [],
    dataCreate: {},
    visible: false,
    data: {},
    sex: "",
    headQuarterIdUser: "",
    role: "",
    disableWorkPlace: true
  }),
  beforeCreate() {
    this.form = this.$form.createForm(this, { name: "register" });
  },
  mounted() {
    this.shiftOptions = JSON.parse(localStorage.getItem("shiftOptions"));
    this.workType = JSON.parse(localStorage.getItem("workType"));
    this.headQuarter = JSON.parse(localStorage.getItem("headQuarter"));
    this.workPlace = JSON.parse(localStorage.getItem("workPlace"));
    const getUser = async (param) => {
      const idUser = localStorage.getItem('idUser')
      const url = `info/detail/${idUser}`;
      try {
        const res = await PersonService.get(url);
        if (res) {
          this.data = res.data.data;
          if (this.data.sex == 1) {
            this.sex = "Man";
          } else if (this.data.sex == 0) {
            this.sex = "Girl";
          } else {
            this.sex = "Unknown";
          }
          if (this.data.role == 1) {
            this.role = "User";
          } else if (this.data.role == 2) {
            this.role = "Admin";
          }
          if (this.data.headQuarterId == 1) {
            this.headQuarterIdUser = "Yokohama";
          } else if (this.data.headQuarterId == 2) {
            this.headQuarterIdUser = "Tokyo";
          } else if (this.data.headQuarterId == 3) {
            this.headQuarterIdUser = "Sapporo";
          } else {
            this.headQuarterIdUser = "Miyagi";
          }
          this.form.getFieldDecorator('workPlaceId', { initialValue: this.headQuarterIdUser });
        }
      } catch (error) {
        console.log(error);
      }
    }
    getUser();
  },
  fetch() {
    Promise.all([
      this.getHeadQuaters(),
    ]);
  },
  methods: {
    showModal() {
      this.visible = true;
    },
    async handleSubmit(e) {
      e.preventDefault();
      this.form.validateFieldsAndScroll((err, values) => {
        if (!err) {
          const fromCreate = {
            ...values,
            workDate: this.$moment(values.workDate).format("YYYY-MM-DD"),
            startTime: this.$moment(values.startTime).format("HH:mm"),
            endTime: this.$moment(values.endTime).format("HH:mm"),
          };
          this.dataCreate = fromCreate;
        }
      });
      let headQuarterId
      if (parseInt(this.headQuarterIdUser)) {
        headQuarterId = this.headQuarter.find((item) => {
          return item.id == this.headQuarterIdUser
        });
      } else {
        console.log(this.headQuarter);
        console.log(this.headQuarterIdUser);
        headQuarterId = this.headQuarter.find((item) => {
          return item.name == this.headQuarterIdUser
        });
      }
      let workPlaceId
      if (parseInt(this.dataCreate.workPlaceId)) {
        workPlaceId = this.workPlace.find((item) => {
          return item.id == this.dataCreate.workPlaceId
        });
      } else {
        workPlaceId = this.workPlace.find((item) => {
          return item.name == this.dataCreate.workPlaceId
        });
      }
      console.log(workPlaceId);
      this.dataCreate.headQuarterId = headQuarterId.id
      this.dataCreate.workPlaceId = workPlaceId.id
      try {
        if (this.dataCreate.workDate >= today()) {
          const res = await PersonService.post(
            "user/working-schedule/add",
            this.dataCreate
          );
          if (res.data.message === "Successfully") {
            this.form.resetFields();
            alert(`Add ${res.data.message}`);
            this.visible = false;
            this.$router.go(0);
          } else {
            alert(res.data.message);
          }
        } else {
          alert("Not scheduled in the past");
        }
      } catch (error) {
        console.log(error);
      }
    },
    async getHeadQuaters() {
      try {
        const res = await PersonService.get(
          "user/working-schedule/get-headquarters"
        );
        this.headQuarter = res.data.data;
        localStorage.setItem("headQuarter", JSON.stringify(this.headQuarter));
      } catch (error) {
        console.log(error);
      }
    },
    handleChangeWorkType(e) {
      if (e == 3) {
        this.disableWorkPlace = false;
      } else {
        this.disableWorkPlace = true;
        this.form.setFieldsValue({
          ['workPlaceId']: this.headQuarterIdUser
        });
      }
    }
  },
};
</script>
<style lang="scss" scoped>
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

.form-create {
  padding: 0 20px;
}

.mr-20 {
  margin-right: 10px;
}

.inf_all {
  margin-top: 20px;
}

.Name {
  margin: 5px 0;
  font-size: 18px;
  font-weight: 700;
}

.network {
  margin: 10px 0;
}

.fill-height {
  margin-top: 20px;
  margin-left: 20px;
}

.v-sheet {
  margin-bottom: 5px;
}

.v-toolbar__title {
  margin-left: 10px;
}

.disable {
  pointer-events: none;
}
</style>
