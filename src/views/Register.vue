<template>
  <v-container>
    <v-row align-content="center" justify="center" style="height:100vh">
      <v-col v-if="alert.is" cols="12" sm="8" md="6" class="py-0">
        <v-alert prominent :type="alert.type">
          {{ alert.text }}
        </v-alert>
      </v-col>
      <v-col cols="12">
        <v-card class="mx-auto pb-4" max-width="400">
          <v-card-title class="justify-center">
            <v-list class="primary--text">
              <v-list-item-avatar size="62">
                <v-img src="../assets/logo.png"></v-img>
              </v-list-item-avatar>
              <v-list-item-content class="">
                <v-list-item-title>Create Account</v-list-item-title>
              </v-list-item-content>
            </v-list>
          </v-card-title>

          <v-divider></v-divider>
          <v-card-text>
            <v-form
              @submit.prevent="submit"
              ref="form"
              v-model="valid"
              lazy-validation
            >
              <v-row no-gutters>
                <v-col cols="12" class="px-2">
                  <v-text-field
                    color="primary"
                    v-model="firstName"
                    :rules="firstNameRules"
                    type="text"
                    name="firstName"
                    label="First Name"
                    @click:append="show = !show"
                    prepend-icon="mdi-account"
                  ></v-text-field>
                </v-col>
                <v-col cols="12" class="px-2">
                  <v-text-field
                    color="primary"
                    v-model="lastName"
                    :rules="lastNameRules"
                    type="text"
                    name="lastName"
                    label="Last Name"
                    @click:append="show = !show"
                    prepend-icon="mdi-account"
                  ></v-text-field>
                </v-col>
                
                <v-col cols="12" class="px-2">
                  <v-text-field
                    color="primary"
                    v-model="email"
                    :rules="emailRules"
                    type="email"
                    name="email"
                    label="Email"
                    @click:append="show = !show"
                    prepend-icon="mdi-email"
                  ></v-text-field>
                </v-col>
                <v-col cols="12" class="px-2">
                  <v-text-field
                    color="primary"
                    v-model="password"
                    :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
                    :rules="required"
                    :type="show ? 'text' : 'password'"
                    name="password"
                    label="Password"
                    @click:append="show = !show"
                    prepend-icon="mdi-lock"
                  ></v-text-field>
                </v-col>
                <v-col cols="12" class="mb-4">
                  <v-btn text small color="primary">
                    <v-icon small class="mr-2">mdi-lock</v-icon>
                    Login Instead
                  </v-btn>
                </v-col>
                <v-col cols="12" class="px-2">
                  <v-btn type="submit" :loading="loading.register" block color="primary"
                    >Sign up</v-btn>
                </v-col>
              </v-row>
            </v-form>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
// @ is an alias to /src
import {mapGetters, mapActions} from 'vuex'
export default {
  name: "Register",
  data: () => ({
    valid: true,
    firstName: "",
    firstNameRules: [ (v) => !!v || "E-mail is required"],
    lastName: "",
    lastNameRules: [ (v) => !!v || "E-mail is required"],
    email: "",
    password: "",
    terms: null,
    show: false,
    required: [(v) => !!v || "Name is required"],
    emailRules: [
      (v) => !!v || "E-mail is required",
      (v) => /.+@.+/.test(v) || "E-mail must be valid",
    ], 

  }),
 
  computed: {
      ...mapGetters({loading:"Get_Loading", alert:"Get_Alert", authorize: "Get_Authorize",})
  },
 created(){

   if(this.authorize){
 this.$store.dispatch('authenticated',"/register")
   }
  },
  methods: {
      ...mapActions({login:"Create_User"}),
    submit() {
      if (this.$refs.form.validate()) {
        const user = {
          firstName:this.firstName,
          lastName:this.lastName,
          email: this.email,
          password: this.password,
        };
        this.login(user)    

      }
    },

    reset() {
      this.$refs.form.reset();
    },
    resetValidation() {
      this.$refs.form.resetValidation();
    },

    
  },
};
</script>
