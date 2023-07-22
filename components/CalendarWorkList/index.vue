<template>
  <v-row class="fill-height">
    <v-col>
      <v-sheet height="64">
        <v-toolbar flat>
          <v-btn outlined class="mr-4" color="grey darken-2" @click="setToday">
            Today
          </v-btn>
          <v-btn fab text small color="grey darken-2" @click="prev">
            <v-icon small> mdi-chevron-left </v-icon>
          </v-btn>
          <v-btn fab text small color="grey darken-2" @click="next">
            <v-icon small> mdi-chevron-right </v-icon>
          </v-btn>
          <v-toolbar-title v-if="$refs.calendar">
            {{ $refs.calendar.title }}
          </v-toolbar-title>
          <v-spacer></v-spacer>
          <v-menu bottom right>
            <template v-slot:activator="{ on, attrs }">
              <v-btn outlined color="grey darken-2" v-bind="attrs" v-on="on">
                <span>{{ typeToLabel[type] }}</span>
                <v-icon right> mdi-menu-down </v-icon>
              </v-btn>
            </template>
            <v-list>
              <v-list-item @click="type = 'day'">
                <v-list-item-title>Day</v-list-item-title>
              </v-list-item>
              <v-list-item @click="type = 'month'">
                <v-list-item-title>Month</v-list-item-title>
              </v-list-item>
              <v-list-item @click="type = 'week'">
                <v-list-item-title>Week</v-list-item-title>
              </v-list-item>
            </v-list>
          </v-menu>
        </v-toolbar>
      </v-sheet>
      <v-sheet class="calendar-main">
        <v-calendar ref="calendar" v-model="focus" color="primary" :events="events" :weekdays="weekdays"
          :weekday-format="getDay" :event-color="getEventColor" :type="type" :locale-first-day-of-year="1"
          @click:event="showEvent" @click:date="viewDay" @click:more="viewDay" :event-height="50">
          <template v-slot:event="{ event }">
            <b-badge v-b-tooltip.hover.topLeft style="text-align: left;">
              {{ event.nameUser + " " + formatAMPM(event.start) + " - " + formatAMPM(event.end) }} <br />
              Headquater: {{ event.headQuarter }} <br />
              Work Type: {{ event.workType }} <br />
              Work Place: {{ event.workPlace }}
            </b-badge>
          </template>
        </v-calendar>
        <!-- modal events -->
        <a-modal :visible="selectedOpen" :footer="null" :closable="false">
          <v-card color="grey lighten-4" flat>
            <v-toolbar :color="selectedEvent?.color?.toLowerCase()" dark>
              <!--eslint-disable vue/no-v-text-v-html-on-component-->
              <v-toolbar-title v-html="selectedEvent.name"></v-toolbar-title>
              <v-spacer></v-spacer>
              <v-btn icon @click="handleDeleteEvent">
                <v-icon>mdi-delete</v-icon>
              </v-btn>
            </v-toolbar>
            <v-card-text>
              <span v-html="selectedEvent.details"></span>
              <div class="form-events">
                <a-form :form="form" @submit="handleUpdateEvent">
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
                    <a-textarea v-decorator="['description']" />
                  </a-form-item>

                  <div style="display: flex; gap: 4px">
                    <a-form-item label="Work Date">
                      <a-input v-decorator="['workDate']" />
                    </a-form-item>
                    <a-form-item label="Start Time">
                      <a-input v-decorator="['startTime']" format="HH:mm" />
                    </a-form-item>
                    <a-form-item label="End Time">
                      <a-input v-decorator="['endTime']" format="HH:mm" />
                    </a-form-item>
                  </div>
                  <div style="display: flex; gap: 4px">
                    <a-form-item label="Shift" style="width: 100%">
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
                    <a-form-item label="Work Type" style="width: 100%">
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
                    <a-form-item label="Head Quarter" style="width: 100%">
                      <a-select style="pointer-events: none;" v-decorator="[
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
                  </div>
                  <a-form-item>
                    <a-button type="primary" html-type="submit">
                      Save
                    </a-button>
                    <v-btn text color="secondary" @click="selectedOpen = false">
                      Cancel
                    </v-btn>
                  </a-form-item>
                </a-form>
              </div>
            </v-card-text>
          </v-card>
        </a-modal>
        <!-- </v-menu> -->
      </v-sheet>
    </v-col>
  </v-row>
</template>

<script>
import PersonService from "../../services/api/personService";
import AdminService from "../../services/api/adminService";

export default {
  name: "CalendarWorkList",
  props: {
    searchValue: {
      type: String,
      default: "",
    },
  },
  data: () => ({
    focus: "",
    type: "month",
    typeToLabel: {
      month: "Month",
      day: "Day",
      week: "Week",
    },
    selectedEvent: {},
    selectedElement: null,
    selectedOpen: false,
    events: [],
    weekdays: [1, 2, 3, 4, 5, 6, 0],
    daysOfWeek: [
      "Sunday",
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday",
    ],
    shiftOptions: [],
    workType: [],
    headQuarter: [],
    workPlace: [],
    dateRange: {
      startDate: null,
      endDate: null,
    },
    currentMonth: new Date().getMonth(),
    form: {},
    disableWorkPlace: true,
    headQuarterIdUser: "",
    typeEdit: "",
  }),
  beforeMount() {
    this.getFirstDateAndLastDate();
    this.form = this.$form.createForm(this, { name: "register" });
    if(window.location.href.includes('work')) {
      this.typeEdit = 2;
    }else {
      this.typeEdit = 1;
    }
  },
  mounted() {
    const getUser = async (param) => {
      const idUser = localStorage.getItem('idUser')
      const url = `info/detail/${idUser}`;
      try {
        const res = await PersonService.get(url);
        if (res) {
          this.data = res.data.data;
          if (this.data.headQuarterId == 1) {
            this.headQuarterIdUser = "Yokohama";
          } else if (this.data.headQuarterId == 2) {
            this.headQuarterIdUser = "Tokyo";
          } else if (this.data.headQuarterId == 3) {
            this.headQuarterIdUser = "Sapporo";
          } else {
            this.headQuarterIdUser = "Miyagi";
          }
        }
      } catch (error) {
        console.log(error);
      }
    }
    getUser();
  },
  fetch() {
    Promise.all([
      this.getListShifts(),
      this.getWorkTypes(),
      this.getHeadQuaters(),
      this.getWorkPlaces(),
      this.getListEvents(),
    ]);
  },
  watch: {
    searchValue: {
      handler() {
        this.getListEvents();
      },
    },
    keyword: {
      handler(val, oldVal) {
        // do your stuff
      }
    },
    currentMonth: {
      handler() {
        this.getFirstDateAndLastDate();
      },
    },
    dateRange: {
      handler() {
        this.getListEvents();
      },
      deep: true,
    },
  },
  methods: {
    async handleUpdateEvent(e) {
      e.preventDefault();
      let payload = {};

      this.form.validateFieldsAndScroll((err, values) => {
        if (!err) {
          payload = {
            ...values,
            scheduleId: this.selectedEvent.scheduleId,
          };
        }
      });
      const workPlaceId = this.workPlace.find((item) => {
        return item.name == payload.workPlaceId
      });
      if(workPlaceId) {
        payload.workPlaceId = workPlaceId.id
      }
      // console.log(payload);
      try {
        const res = await PersonService.put(
          `user/working-schedule/update/${this.typeEdit}`,
          payload
        );
        if (res.data.code === 200) {
          alert("Update Successful!");
          this.selectedOpen = false;
          this.getListEvents();
        } else {
          alert(message);
        }
      } catch (error) {
        alert(error.message);
      }
    },
    getFirstDateAndLastDate() {
      const date = new Date();
      const startDate = new Date(date.getFullYear(), this.currentMonth, 1);
      const endDate = new Date(date.getFullYear(), this.currentMonth + 1, 0);

      this.dateRange = {
        startDate: this.$moment(startDate).format("YYYY-MM-DD"),
        endDate: this.$moment(endDate).format("YYYY-MM-DD"),
      };
    },
    async handleDeleteEvent() {
      const res = await PersonService.delete(
        `user/working-schedule/delete/${this.typeEdit}?scheduleId=${this.selectedEvent.scheduleId}`
      );
      if (res.data.code === 200) {
        alert("Delete was successfully!");
        this.selectedOpen = false;
        this.getListEvents();
      } else {
        alert(`Error ${res.data.message}`);
      }
    },

    async getListEvents() {
      let url = `working-schedule/?endDate=${this.dateRange.endDate}&startDate=${this.dateRange.startDate}`;
      // const urlUser = `user/working-schedule/?endDate=${this.dateRange.endDate}&startDate=${this.dateRange.startDate}`;
      const listSearchData = this.searchValue.split(';');
      const employeeName = listSearchData[0];
      const workPlaceId = listSearchData[1];
      const headQuarterId = listSearchData[2];
      const workTypeId = listSearchData[3];
      if (employeeName != "") {
        url += `&employeeName=${employeeName}`;
      }
      if (workPlaceId && workPlaceId !== "undefined" && workPlaceId !== "All") {
        url += `&workplace=${workPlaceId}`;
      }
      if (headQuarterId && headQuarterId !== "undefined" && headQuarterId !== "All") {
        url += `&headquarter=${headQuarterId}`;
      }
      if (workTypeId && workTypeId !== "undefined" && workTypeId !== "All") {
        url += `&workTypeId=${workTypeId}`;
      }
      try {
        const res = await AdminService.getUserSchedule(url);
        // const resUser = await PersonService.get(urlUser);
        const listAdmin = res.data.data.content;
        // const listUser = resUser.data.data;
        // resUser.data.data
        const listEvent = [];
        listAdmin.forEach((item)=> {
          for (const key in item) {
            if(key.includes('day') && item[key].length > 0){
              item[key].forEach((eventOnDay)=> {
                let colorEvent = "Green";
                if (eventOnDay.workTypeName === "Work At Company") {
                  colorEvent = "Red";
                }
                if (eventOnDay.workTypeName === "Go On Bussiness") {
                  colorEvent = "#2196F3";
                }
                if (eventOnDay.workTypeName === "Work at Home") {
                  colorEvent = "Green";
                }
                if (eventOnDay.workTypeName === "Leave") {
                  colorEvent = "Purple";
                }
                listEvent.push({
                  title: eventOnDay.title,
                  scheduleId: eventOnDay.id,
                  name: eventOnDay.title,
                  start: eventOnDay.startTime,
                  end: eventOnDay.endTime,
                  color: colorEvent,
                  timed: true,
                  isCanEdit: false,
                  workType: eventOnDay.workTypeName,
                  headQuarter: eventOnDay.headQuarterName,
                  workPlace: eventOnDay.workPlaceName,
                  nameUser: item.name
                })
              })
            }
          }
        });
        this.events = listEvent
      } catch (error) {
        console.log(error);
      }
    },
    async getListShifts() {
      try {
        const res = await PersonService.get("user/working-schedule/get-shifts");
        this.shiftOptions = res.data.data;
        localStorage.setItem("shiftOptions", JSON.stringify(this.shiftOptions));
      } catch (error) {
        console.log(error);
      }
    },
    async getWorkTypes() {
      try {
        const res = await PersonService.get(
          "user/working-schedule/get-worktypes"
        );
        this.workType = res.data.data;
        localStorage.setItem("workType", JSON.stringify(this.workType));
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
    async getWorkPlaces() {
      try {
        const res = await PersonService.get(
          "user/working-schedule/get-workplaces"
        );
        this.workPlace = res.data.data;
        localStorage.setItem("workPlace", JSON.stringify(this.workPlace));
      } catch (error) {
        console.log(error);
      }
    },
    onChange(date, dateString) {
    },
    getDay(date) {
      const day = new Date(date.date).getDay();
      return this.daysOfWeek[day];
    },
    viewDay({ date }) {
      this.focus = date;
      this.type = "day";
    },
    getEventColor(event) {
      const color = event?.color.toLowerCase();
      return color;
    },
    setToday() {
      const thisMonth = new Date().getMonth();
      if( this.currentMonth !== thisMonth) {
        this.currentMonth = thisMonth;
      }
      let date = new Date().toJSON().slice(0, 10);
      this.focus = date;
      this.type = "day";
    },
    prev() {
      this.$refs.calendar.prev();
      if(this.type == "month") {
        this.currentMonth--;
      }
    },
    next() {
      this.$refs.calendar.next();
      if(this.type == "month") {
        this.currentMonth++;
      }
    },
    showEvent({ nativeEvent, event }) {
      this.selectedEvent = event;
      const open = async () => {
        try {
          const res = await PersonService.get(
            `/user/working-schedule/detail/${this.typeEdit}?scheduleId=${event.scheduleId}`
          );

          if (res.status === 200) {
            const data = res.data.data;
            this.form.setFieldsValue({
              description: data.description,
              endTime: data.endTime,
              scheduleId: data.scheduleId,
              shiftId: data.shiftId,
              startTime: data.startTime,
              title: data.title,
              workDate: data.workDate,
              workTypeId: data.workTypeId,
              headQuarterId: data.headQuarterId,
              workPlaceId: data.workPlaceId,
            });
            if (data.workTypeId == 3) {
              this.disableWorkPlace = false;
            } else {
              this.disableWorkPlace = true;
              const headQuarterId = this.headQuarter.find((item) => {
                return item.name == this.headQuarterIdUser
              });
              this.form.getFieldDecorator('workPlaceId', { initialValue: headQuarterId.name });
            }
          }
        } catch (error) {
          console.log(error);
        }

        this.selectedElement = nativeEvent.target;
        requestAnimationFrame(() =>
          requestAnimationFrame(() => (this.selectedOpen = true))
        );
      };

      if (this.selectedOpen) {
        // this.selectedOpen = false;
        requestAnimationFrame(() => requestAnimationFrame(() => open()));
      } else {
        open();
      }

      nativeEvent.stopPropagation();
    },
    formatAMPM(dateString) {
      const date = new Date(dateString);
      let hours = date.getHours();
      let minutes = date.getMinutes();
      let ampm = hours >= 12 ? 'pm' : 'am';
      hours = hours % 12;
      hours = hours ? hours : 12; // the hour '0' should be '12'
      minutes = minutes < 10 ? '0' + minutes : minutes;
      const strTime = hours + ':' + minutes + ' ' + ampm;
      return strTime;
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
<style>
.calendar-main {
  height: calc(100% - 64px);
}

.select-type {
  display: flex;
  gap: 4px;
}

.popup-box {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.popup-input {
  border: 1px solid black;
  border-radius: 4px;
  width: 100%;
  padding: 4px 8px;
}

.ant-modal-body {
  padding: 0 !important;
}

.disable {
  pointer-events: none;
}
</style>
