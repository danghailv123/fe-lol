<template>
    <div class="container">
        <b-form @submit="submitSignUp" class="formSignUp">
            <b-form-group id="input-group-1" label="FullName :" label-for="input-1">
                <b-form-input id="input-1" v-model="data.fullName" type="text" placeholder="Enter " required></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-1" label="BirthDay :" label-for="input-1">
                <b-form-input id="input-1" v-model="data.birthDay" type="date" placeholder="Enter " required></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-1" label="Address :" label-for="input-1">
                <b-form-input id="input-1" v-model="data.address" type="text" placeholder="Enter " required></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-1" label="PhoneNumber :" label-for="input-1">
                <b-form-input id="input-1" v-model="data.phoneNumber" type="number" placeholder="Enter "
                    required></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-1" label="Email :" label-for="input-1">
                <b-form-input id="input-1" v-model="data.email" type="email" placeholder="Enter " required></b-form-input>
            </b-form-group>
            <b-form-group id="input-group-1" label="Password :" label-for="input-1">
                <b-form-input readonly id="input-1" v-model="pass" type="password" placeholder="Enter "
                    required></b-form-input>
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
            <b-form-group id="input-group-1" label="Description :" label-for="input-1">
                <b-form-input id="input-1" v-model="data.description" type="text" placeholder="Enter "
                    required></b-form-input>
            </b-form-group>
            <b-button type="submit" class="mt-5">Update</b-button>
        </b-form>
    </div>
</template>
  
<script>
import TableCustom from "~/components/TableCustom";
import PersonService from "../../services/api/personService";

import CalendarSidebar from "~/components/CalendarSidebar";
export default {
    name: "table",
    components: {
        TableCustom,
        CalendarSidebar,
    },
    data() {
        return {
            userName: "",
            total: "",
            isEdit: false,
            parts: [],
            parts2: [],
            pass: "123456",
            data: {},
            search: { employeeName: "", laborCode: "", groupId: "" },
            userName: "",
            headQuarter: [],
            sex: "",
            headQuarterId: "",
            role: "",
            form: {}
        };
    },
    beforeMount() {
        this.form = this.$form.createForm(this, { name: "account" });
    },
    fetch() {
        Promise.all([
            this.getAllUser(),
            this.getHeadQuaters(),
        ]);
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
        formattedDate(param) {
            const parts = param.split("-");
            const formatted = `${parts[2]}/${parts[1]}/${parts[0]}`;
            return formatted;
        },
        async submitSignUp(event) {
            event.preventDefault();
            try {
                this.data.birthDay = this.formattedDate(this.data.birthDay);
                this.data.headQuarterId = this.data.headQuarterId;
                console.log(this.data);
                const res = await PersonService.post(
                    "info/update/",
                    this.data
                );
                if (res) {
                    this.$message.success("success");
                }
            } catch (error) {
                this.$message.error("Invalid Phone Number");
            }
        },
        async getAllUser(param) {
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
                    if(this.data.headQuarterId == 1) {
                        this.headQuarterId = "Yokohama";
                    } else if(this.data.sex == 2) {
                        this.headQuarterId = "Tokyo";
                    } else if(this.data.sex == 3) {
                        this.headQuarterId = "Saporo";
                    }else {
                        this.headQuarterId = "Miyagi";
                    }
                }
            } catch (error) {
                console.log(error);
            }
        },
        searchName() {
            this.getAllUser(this.userName);
        },
        confirm(id) {
            this.deletes(id);
        },

        onShowSizeChange(current, pageSize) {
            this.page.current = current;
            this.page.pageSize = pageSize;
            this.getvalue();
        },
        async edit(id) {
            this.$router.push("/user/" + id);
        },
        async save(id, password) {
            const res = await updateAcc(id, password);
            if (res.code === 200 && res) {
                message.success("success ");
            }
            this.isEdit = !this.isEdit;
        },
        async deletes(id) {
            const url = `admin/user-manager/detele/${id}`;
            try {
                const res = await PersonService.delete(url);

                if (res) {
                    this.$message.success("delete success");
                }
            } catch (error) {
                this.$message.error("delete error: " + error);
            }
        },
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

td,
th {
    border: 1px solid #dddddd;
    text-align: left;
    padding: 8px;
}

.with100 {
    width: 104%;
}

.save {
    background-color: #069d72;
    margin-right: 5px;
    padding: 2px 10px;
    border: none;
    border-radius: 20px;
}

.edit {
    background-color: #f2c34a;
    margin: 2px 0;
    margin-right: 5px;
    padding: 2px 8px;
    border: none;
    border-radius: 20px;
}

.inputLogin {
    border-radius: 50px;
    height: 32px;
}

.delete {
    background-color: #eb6e6e;
    padding: 2px 10px;
    border-radius: 20px;
    border: none;
}

.add-btn {
    background-color: #045396;
    border: none;
}

.title {
    color: #045396;
}

.bttAdd {
    margin-bottom: -13px;
}

.borderRadius {
    border-radius: 50px;
}

.wFull {
    width: 100% !important;
}

.title2 {
    font-size: 20px;
}

.table {
    display: flex;
    justify-content: center;
}

.listBtn {
    display: flex;
    justify-content: center;
    align-items: center;
}

.formSignUp {
    width: 20%;
    margin: auto;
}
</style>
  