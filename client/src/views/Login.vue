<template>
  <div class="container">
    <div class="columns">
      <div class="column is-4 is-offset-4">
        <h1 class="title">Log in</h1>
        <!-- Log In Form -->
        <form @submit.prevent="formSubmit">
          <!-- Email Input -->
          <div class="field">
            <label>Email</label>
            <div class="control">
              <input
                type="email"
                name="email"
                class="input"
                v-model="usename"
              />
            </div>
          </div>

          <!-- Password Input -->
          <div class="field">
            <label>Password</label>
            <div class="control">
              <input
                type="password"
                name="password"
                class="input"
                v-model="password"
              />
            </div>
          </div>

          <!-- Errors -->
          <div class="notification is-danger" v-if="errors.length">
            <p v-for="error in errors" v-bind:key="error">- {{ error }}</p>
          </div>

          <!-- Submit Button -->
          <div class="field">
            <div class="control">
              <button class="button is-success">Submit</button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Login",

  data() {
    return {
      username: "",
      password: "",
      errors: [],
    };
  },
  methods: {
    formSubmit() {
      axios.defaults.headers.common["Authorization"] = "";
      localStorage.removeItem("token");

      const formData = {
        username: this.usename,
        password: this.password,
      };

      axios
        .post("api/v1/token/login/", formData)
        .then((response) => {
          const token = response.data.auth_token;

          this.$store.commit("setToken", token);

          axios.defaults.headers.common["Authorization"] = `Token ${token}`;

          localStorage.setItem("token", token);

          this.$router.push("/dashboard/my-account");
        })
        .catch((err) => {
          if (err.response) {
            for (const property in err.response.data) {
              this.errors.push(`${property}: ${err.response.data[property]}`);
            }
          } else if (err.message) {
            this.errors.push("Something went wrong, please try again!");
          }
        });
    },
  },
};
</script>
