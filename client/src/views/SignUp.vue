<template>
  <div class="container">
    <div class="columns">
      <div class="column is-4 is-offset-4">
        <h1 class="title">Sign up</h1>
        <!-- Sign Up Form -->
        <form @submit.prevent="formSubmit">
          <!-- Email Input -->
          <div class="field">
            <label>Email</label>
            <div class="control">
              <input
                type="email"
                name="email"
                class="input"
                v-model="username"
              />
            </div>
          </div>

          <!-- Password Input -->
          <div class="field">
            <label>Password</label>
            <div class="control">
              <input
                type="password"
                name="password1"
                class="input"
                v-model="password1"
              />
            </div>
          </div>
          <!-- Confirm Password Input -->
          <div class="field">
            <label>Repeat Password</label>
            <div class="control">
              <input
                type="password"
                name="password2"
                class="input"
                v-model="password2"
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
import { toast } from "bulma-toast";

export default {
  name: "SignUp",
  data() {
    return {
      username: "",
      password1: "",
      password2: "",
      errors: [],
    };
  },
  methods: {
    async formSubmit() {
      this.errors = [];

      if (this.username === "") {
        this.errors.push("the username is missing.");
      }

      if (this.password1 === "") {
        this.errors.push("the password is too short.");
      }

      if (this.password1 !== this.password2) {
        this.errors.push("the passwords are not mathching.");
      }

      if (!this.errors.length) {
        this.$store.commit("setIsLoading", true);
        const formData = {
          username: this.username,
          password: this.password1,
        };

        await axios
          .post("api/v1/users/", formData)
          .then(() => {
            toast({
              message: "Account was created, please log in",
              type: "is-success",
              dismissible: true,
              pauseOnHover: true,
              duration: 2000,
              position: "bottom-right",
            });

            this.$router.push("/log-in");
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

        this.$store.commit("setIsLoading", false);
      }
    },
  },
};
</script>
