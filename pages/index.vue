<template>
  <v-card
    class="mx-auto ma-7 grey darken-3"
    max-width="600"
  >
    <v-img
      height="400"
      width="600"
      class="white--text align-end"
      src="https://cdn.dribbble.com/users/1200964/screenshots/3123306/icon_fffff.gif"
    >
      <v-card-title class="blue--text text--darken-3">Soroosh messaging</v-card-title>
    </v-img>

    <v-card-subtitle class="pb-0">
      wellcome!
    </v-card-subtitle>

    <v-card-text class="text--primary">
      <div>create a account and connect to your friends safe & easily</div>
    </v-card-text>




    <v-card-actions >
      <v-btn
        class="ml-3 grey--text text--darken-3"
        color="blue darken-1"
        @click="login"
      >
        Login
      </v-btn>

      <v-row class="ml-2">
        <v-dialog
          v-model="dialog"
          persistent
          max-width="600px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="blue darken-1"
              text
              dark
              v-bind="attrs"
              v-on="on"
            >
              Signup
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">Signup form</span>
            </v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      class="blue--text"
                      label="username *"
                      v-model="username"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      class="blue--text"
                      label="first name"
                      v-model="first_name"

                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      class="blue--text"
                      label="last name"
                      v-model="last_name"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field
                      class="blue--text"
                      label="Phone number"
                      v-model="phone"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field
                      class="blue--text"
                      label="Password *"
                      type="password"
                      v-model="password"
                      required
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12">
                    <v-text-field
                      class="blue--text"
                      label="repeat Password *"
                      type="password"
                      v-model="password1"
                      required
                    ></v-text-field>
                  </v-col>

                </v-row>
              </v-container>
              <small>*indicates required field</small>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue darken-1"
                text
                @click="dialog = false"
              >
                Close
              </v-btn>
              <v-btn
                color="green darken-1"
                text
                @click="signup"
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-row>


    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  data() {
    return {
      dialog: false,
      password1: '',
      phone: '',
      data1: null,
      username: '',
      first_name: '',
      last_name: '',
      show1: false,
      password: '',
      data: null,
      rules: {
        required: value => !!value || 'Required.',
        min: v => v.length >= 5 || 'Min 5 characters',
      },



    }
  },
  methods: {
    login() {
      window.location.href = "http://127.0.0.1:8000/api/log"

    },
    signup() {
      const formData = new FormData()
      formData.append("username", this.username);
      formData.append("password", this.password);
      formData.append("password1", this.password1);
      formData.append("first_name", this.first_name);
      formData.append("last_name", this.last_name);
      formData.append("phone", this.phone);
      this.data1 = formData
      this.$axios.$post('http://127.0.0.1:8000/api/sign', this.data1)
        .then(response => {
          console.log(response)
          window.alert('signing up ... wait a moment')
          window.location.href = "/"


        })
    }


  },


}
</script>
