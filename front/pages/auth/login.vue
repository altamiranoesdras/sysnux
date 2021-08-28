<template>
  <v-container class="fill-height" fluid>

    <v-row align="center" justify="center" class="mt-5" >
      <v-col cols="12" sm="8" md="4">

        <v-form @submit.prevent="login">
          <v-card class="elevation-12">

            <v-toolbar color="primary" dark flat>
              <v-toolbar-title v-text="name" class="font-weight-bold"></v-toolbar-title>
              <v-spacer />
            </v-toolbar>

            <v-card-text>

                <v-text-field label="Login"  prepend-icon="mdi-account" type="text" v-model="form.email"/>

                <v-text-field label="Password" prepend-icon="mdi-lock" type="password" v-model="form.password"/>

            </v-card-text>
            <v-card-actions>
              <v-spacer />
              <v-btn :color="loading ? 'success' : 'primary'" type="submit">
                <span v-text="loading ? 'Ingresando...' : 'Ingresar'" ></span>   <v-icon v-show="loading">mdi-loading mdi-spin</v-icon>
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-form>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>

    // Import the functions you need from the SDKs you need
    import { initializeApp } from "firebase/app";
    import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword } from "firebase/auth";


    // Your web app's Firebase configuration
    const firebaseConfig = {
      apiKey: "AIzaSyCOEaSOVSlgL0dVkP-XSOqQSi1OQXqfsAM",
      authDomain: "test-c4643.firebaseapp.com",
      projectId: "test-c4643",
      storageBucket: "test-c4643.appspot.com",
      messagingSenderId: "321183731663",
      appId: "1:321183731663:web:fe53c9dee1c404a8bb4cba"
    };

    // Initialize Firebase
    const app = initializeApp(firebaseConfig);





    import pkg from '../../package'

    export default {
        layout: 'clean',
        name: "login",
        data(){
            return {
                name: pkg.name.toUpperCase(),
                loading: false,
                form: {
                    email:'',
                    password:'',
                }
            }
        },
        methods: {
            async login(){

                this.loading = true

                const auth = getAuth(app);

              try{

                let userCredential = await signInWithEmailAndPassword(auth, this.form.email, this.form.password);


                this.loading = false;
                console.log('login');
                // Signed in
                const user = userCredential.user;

                let data = {
                  data: {
                    grant_type: "password",
                    client_id: process.env.PASSPORT_PASSWORD_GRANT_ID,
                    client_secret: process.env.PASSPORT_PASSWORD_GRANT_SECRET,
                    scope: "*",
                    username: this.form.email,
                    password: this.form.password
                  }
                };


                try {
                  let login = await this.$auth.loginWith("password_grant", data)

                  this.$router.replace("/");

                  this.notifySuccess('Listo','Ingreso al sistema.');

                  this.$store.dispatch('menu/load')


                }catch (e) {

                  console.log(e.response);

                  var error = e.response.data.message;

                  if(typeof error !== 'undefined'){

                    this.notifyError(error);
                  }

                  this.loading = false
                }


              }catch (error){

                const errorCode = error.code;
                const errorMessage = error.message;
                console.log(errorCode,errorMessage);

                this.notifyError(errorMessage);
                this.loading = false;

              }







            }
        }


    }
</script>

<style scoped>

</style>
