<template>
  <v-row align="center" justify="center">
    <v-col cols="12" sm="8" md="4" align="center">
      <v-card class="elevation-4 text-left" >
        <v-card-title class="fancy-title align-center justify-center">Jello</v-card-title><v-card-text>
          <v-form>
            <v-text-field
              label="Login"
              name="login"
              prepend-icon="mdi-account"
              type="text"
              v-model="auth.email"
            ></v-text-field>

            <v-text-field
              label="Password"
              name="password"
              prepend-icon="mdi-lock"
              type="password"
              v-model="auth.password"
            ></v-text-field>
          </v-form>
        </v-card-text>
        <v-card-actions class="text-center">
          <v-btn
            class="login-button"
            @click="signup"
            depressed
            large
            >Sign-up</v-btn
          >
        </v-card-actions>
      </v-card>
      <v-snackbar
        :timeout="4000"
        v-model="snackbar"
        absolute
        bottom
        center
      >
        {{ snackbarText }}
      </v-snackbar>
    </v-col>
  </v-row>
</template>
<script>
import {v4 as uuidv4} from 'uuid';
export default {
    layout: 'signup',
     data() {
    return {
      snackbar: false,
      snackbarText: 'No error message',
      auth: {
        email: '',
        password: ''
      }
    }
  },
  methods: {
      signup(){
        let that = this
        this.$fire.auth.createUserWithEmailAndPassword(this.auth.email, this.auth.password, function(error, user){
          if(error){
            that.snackbarText = error.message
            that.snackbar = true
          }else{
            that.$fire.db.collection('users').doc(user.uid).set({
              uid: user.uid,
              email: user.email,
              name: user.displayName,
              photoURL: user.photoURL,
              createdAt: new Date().toISOString(),
              updatedAt: new Date().toISOString()
            })
          }
        }).then((user) => {
          $nuxt.$router.push('/')
        }).catch(function (error){
          that.snackbarText = error.message
          that.snackbar = true
        })
      }
  }
}
</script>