<template>
  <div class="container mt-5">
    <div class="card">
      <div class="card-header">
        <h4>Edit User</h4>
      </div>
      <div class="card-body">
        <div class="mb-3">
          <label for="">First Name</label>
          <input type="text" v-model="model.userDetail.firstName" class="form-control" >
        </div>
        <div class="mb-3">
          <label for="">Last Name</label>
          <input type="text" v-model="model.userDetail.lastName" class="form-control">
        </div>
        <div class="mb-3">
          <label for="birthdayInput">Birthday</label>
          <input type="text" id="birthdayInput" v-model="formattedBirthday">
        </div>
        <div class="mb-3">
          <label for="">Username</label>
          <input type="text" v-model="model.userDetail.user.username" class="form-control">
        </div>
        <div class="mb-3">
          <label for="">Email</label>
          <input type="text" v-model="model.userDetail.user.email" class="form-control">
        </div>
        <div class="mb-3">
          <label for="">Password</label>
          <div class="input-group">
            <input :type="showPassword ? 'text' : 'password'" v-model="model.userDetail.user.password" class="form-control"/>
            <button class="btn btn-outline-secondary" type="button" @click="togglePasswordVisibility">
              {{ showPassword ? "Hide" : "Show" }}
            </button>
          </div>
        </div>
        <div class="mb-3">
          <button type="button" @click="editUser" class="btn btn-primary">
            Edit
          </button>&nbsp;
          <RouterLink :to="userEndpoint()" class="btn btn-primary">
            Back
          </RouterLink>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import flatpickr from "flatpickr";

export default {
  name: "UserEditView",
  data() {
    return {
      userId: "",
      formattedBirthday: "",
      model: {
        userDetail: {
          firstName: "",
          lastName: "",
          birthday: "",
          user: {
            id: "",
            username: "",
            password: "",
            email: "",
          },
        },
      },
      showPassword: false,
    };
  },

  methods: {
    userEndpoint() {
      return '/userDetail';
    },
    getUser(userId) {
      axios.get(`http://localhost:8081/v1/userDetail/${userId}`).then((res) => {
        this.model.userDetail = res.data;
        this.formattedBirthday = this.formatDate(res.data.birthday);
      }).catch(function (error) {
        // handle error on UI site
      });
    },
    editUser() {
        axios
            .put(`http://localhost:8081/v1/userDetail/${this.userId}`, this.model.userDetail)
            .then((res) => {
              alert("User was Edited successfully");
            })
            .catch(function (error) {
              console.error("Error editing user:", error);
              alert("Error editing user. Please check the form and try again.");
            });
    },
    formatDate(timestamp) {
      const date = new Date(timestamp);
      return date.toISOString().split("T")[0];
    },
    togglePasswordVisibility() {
      this.showPassword = !this.showPassword;
    },
  },
  mounted() {
    this.userId = this.$route.params.id;
    this.getUser(this.userId);

    flatpickr("#birthdayInput", {
      dateFormat: "Y-m-d",
      defaultDate: this.model.userDetail.birthday
          ? [new Date(this.model.userDetail.birthday)]
          : [],
      onClose: (selectedDates) => {
        this.model.userDetail.birthday = selectedDates[0] ? selectedDates[0].getTime() : "";
        this.formattedBirthday = this.formatDate(this.model.userDetail.birthday);
      },
    });
  },
};
</script>
