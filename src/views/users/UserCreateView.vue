<template>
  <div class="container mt-5">
    <div class="card">
      <div class="card-header">
        <h4>Add Users</h4>
      </div>
      <div class="card-body">
        <div class="mb-3">
          <label for="">First Name</label>
          <input type="text" v-model="model.userDetail.firstName" class="form-control">
        </div>
        <div class="mb-3">
          <label for="">Last Name</label>
          <input type="text" v-model="model.userDetail.lastName" class="form-control" >
        </div>
        <div class="mb-3">
          <label for="birthdayInput">Birthday</label>
          <input type="text" id="birthdayInput">
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
          <button type="button" @click="saveUser" class="btn btn-primary">
            Save
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
  name: "UserCreateView",
  data() {
    return {
      model: {
        userDetail: {
          firstName: '',
          lastName: '',
          birthday: '',
          user: {
            username: '',
            password: '',
            email: ''
          }
        }
      },
      showPassword: false,
    }
  },

  methods: {
    userEndpoint() {
      return '/userDetail';
    },
    saveUser() {
      axios.post('http://localhost:8081/v1/userDetail?createUser=true', this.model.userDetail)
          .then(res => {
            alert('User was saved successful');
            this.model.userDetail = {
              firstName: '',
              lastName: '',
              birthday: '',
              user: {
                username: '',
                password: '',
                email: ''
              }
            }
          }).catch(function (error) {
        console.error("Error saving user:", error);
        alert('Error saving user. Please check the form and try again.');
      })
    },
    togglePasswordVisibility() {
      this.showPassword = !this.showPassword;
    },
  },
  mounted() {
    flatpickr('#birthdayInput', {
      dateFormat: 'Y-m-d',
      onClose: (selectedDates) => {
        this.model.userDetail.birthday = selectedDates[0] ? selectedDates[0].toISOString().split('T')[0] : '';
      },
    });
  }
}
</script>
