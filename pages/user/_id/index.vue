<template lang="">
  <div>
    <div class="title">Edit Information</div>
    <div class="container form2">
      <b-form @submit="submitSignUp" class="form">
        <div class="mb-4 mx-a">
                <b-avatar variant="info" v-bind:src="SignUp.link_image !== null ?  SignUp.link_image : ''"></b-avatar>
            </div>
            <b-form-group id="input-group-1" label="Avatar :" label-for="input-1">
                <b-form-input id="input-1" v-model="SignUp.link_image" type="text" placeholder="Enter "></b-form-input>
            </b-form-group>
        <b-form-group id="input-group-1" label="FullName :" label-for="input-1">
          <b-form-input
            id="input-1"
            v-model="SignUp.fullName"
            type="text"
            placeholder="Enter "
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group id="input-group-1" label="BirthDay :" label-for="input-1">
          <b-form-input
            id="input-1"
            v-model="SignUp.birthDay"
            type="date"
            placeholder="Enter "
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group id="input-group-1" label="Address :" label-for="input-1">
          <b-form-input
            id="input-1"
            v-model="SignUp.address"
            type="text"
            placeholder="Enter "
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group
          id="input-group-1"
          label="PhoneNumber :"
          label-for="input-1"
        >
          <b-form-input
            id="input-1"
            v-model="SignUp.phoneNumber"
            type="number"
            placeholder="Enter "
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group id="input-group-1" label="Email :" label-for="input-1">
          <b-form-input
            id="input-1"
            v-model="SignUp.email"
            type="email"
            placeholder="Enter "
            required 
            readonly
          ></b-form-input>
        </b-form-group>
        <b-form-group id="input-group-1" label="Password :" label-for="input-1">
          <b-form-input id="input-1" v-model="password" type="password" placeholder="Enter "></b-form-input>
        </b-form-group>
        <b-form-group id="input-group-1" label="Role :" label-for="input-1">
                <a-select v-model="role">
                    <a-select-option value="1"> User </a-select-option>
                    <a-select-option value="2"> Admin </a-select-option>
                </a-select>
            </b-form-group>
            <b-form-group id="input-group-1" label="Head Quater" style="width: 100%">
                <a-select v-model="headQuarterId">
                    <a-select-option value="1"> Yokohama </a-select-option>
                    <a-select-option value="2"> Tokyo </a-select-option>
                    <a-select-option value="3">Saporo</a-select-option>
                    <a-select-option value="4">Miyagi</a-select-option>
                </a-select>
            </b-form-group>
            <b-form-group id="input-group-1" label="Sex :" label-for="input-1">
                <a-select v-model="sex">
                    <a-select-option value="0"> Girl </a-select-option>
                    <a-select-option value="1"> Man </a-select-option>
                    <a-select-option value="2">Unknown</a-select-option>
                </a-select>
            </b-form-group>
        <b-form-group
          id="input-group-1"
          label="Description :"
          label-for="input-1"
        >
          <b-form-input
            id="input-1"
            v-model="SignUp.description"
            type="text"
            placeholder="Enter "
            required
          ></b-form-input>
        </b-form-group>
        
        <b-button type="submit" class="mt-5">Submit</b-button>
      </b-form>
    </div>
  </div>
</template>
<script>
import PersonService from "~/services/api/personService.js";

export default {
  data() {
    return {
      SignUp: {
        address: "",
        birthDay: "",
        description: "",
        email: "",
        fullName: "",
        password: "",
        phoneNumber: "",
        role: 1,
        sex: 0,
        image: "",
        headQuarterId: null,
      },
      headQuarter: [],
      headQuarterId: "",
      sex: "",
      role: "",
      password: ""
    };
  },
  fetch() {
    Promise.all([this.getValueId(), this.getHeadQuaters()]);
  },
  mounted() {
    this.getValueId();
  },
  methods: {
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
    async getValueId() {
      const id = this.$route.params.id;
      const url = `admin/user-manager/detail/${id}`;
      try {
        const res = await PersonService.get(url);
        if (res) {
          this.SignUp = res.data.data;
          // console.log(this.SignUp);
          if (this.SignUp.sex == 1) {
            this.sex = "Man";
          } else if (this.SignUp.sex == 0) {
            this.sex = "Girl";
          } else {
            this.sex = "Unknown";
          }

          if (this.SignUp.role == 1) {
            this.role = "User";
          } else if (this.SignUp.role == 2) {
            this.role = "Admin";
          }

          if (this.SignUp.headQuarterId == 1) {
            this.headQuarterId = "Yokohama";
          } else if (this.SignUp.headQuarterId == 2) {
            this.headQuarterId = "Tokyo";
          } else if (this.SignUp.headQuarterId == 3) {
            this.headQuarterId = "Saporo";
          } else {
            this.headQuarterId = "Miyagi";
          }
        }
      } catch (error) {
        console.log(error);
      }
    },
    formattedDate(param) {
      const parts = param.split("-");
      const formatted = `${parts[2]}/${parts[1]}/${parts[0]}`;
      return formatted;
    },
    async submitSignUp(event) {
      event.preventDefault();

      const id = this.$route.params.id;
      const url = `info/update`;
      this.SignUp.birthDay = this.formattedDate(this.SignUp.birthDay);
      if (this.password !== "") {
        this.SignUp.password = this.password;
      } else {
        this.SignUp.password = "";
      }
      let headQuarterId;
      if(parseInt(this.headQuarterId)) {
        headQuarterId = this.headQuarter.find((item) => {
          return item.id == this.headQuarterId
        });
      }else {
        headQuarterId = this.headQuarter.find((item) => {
          return item.name == this.headQuarterId
        });
      }
      // console.log(this.headQuarterId);
      this.SignUp.headQuarterId = headQuarterId.id
      try {
        const res = await PersonService.post(url, this.SignUp);
        if (res) {
          this.$message.success("sửa thành công");
          this.getValueId();
        }
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>
<style scoped>
.form2 {
  width: 20%;
  margin-top: 1%;
  display: flex;
  justify-content: center;
}

.form {
  width: 100%;
}

.title {
  margin-top: 5%;
  text-align: center;
  font-size: 30px;
  font-weight: 700;
}
</style>
