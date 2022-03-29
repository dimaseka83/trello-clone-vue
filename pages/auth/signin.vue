<template>
  <v-row align="center" justify="center">
    <v-col cols="12" sm="8" md="4" align="center">
      <v-card class="elevation-4 text-left" >
        <v-card-title class="fancy-title align-center justify-center">Jello</v-card-title><v-card-text>
          <v-form ref="form">
            <v-text-field
              label="Email"
              name="login"
              prepend-icon="mdi-account"
              type="text"
              v-model="auth.email"
              :rules="[rules.required, rules.email]"
              required
            ></v-text-field>

            <v-text-field
              label="Password"
              name="password"
              prepend-icon="mdi-lock"
              type="password"
              v-model="auth.password"
              required
              :rules="[rules.required]"
            ></v-text-field>
          </v-form>
        </v-card-text>
        <v-card-actions class="text-center">
          <v-btn
            class="login-button"
            @click="signup"
            depressed
            large
            :loading="loading1"
            >Register</v-btn
          >
          <!-- <nuxt-link to="/auth/signup" depressed class="login-button">Sign up</nuxt-link> -->
          <v-spacer></v-spacer>
          <v-btn
            class="login-button"
            @click="login"
            depressed
            large
            :loading="loading"
            >Login</v-btn
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
export default {
  layout: 'signin',
  data() {
    return {
      snackbar: false,
      loading: false,
      loading1: false,
      snackbarText: 'No error message',
      auth: {
        email: '',
        password: ''
      },
      rules: {
        required: value => !!value || 'Required.',
        email: value => {
          const pattern = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
          return pattern.test(value) || 'Invalid e-mail.'
        }
      }
    }
  },
  methods: {
    login() {
      let that = this
      if (this.$refs.form.validate()) {
        this.loading = true
        this.$fire.auth.signInWithEmailAndPassword(this.auth.email, this.auth.password)
        .catch(function (error){
          that.snackbarText = error.message
          that.snackbar = true
        }).then((user) => {
          //we are signed in
          $nuxt.$router.push('/')
          this.loading = false
        })
      }
    },
    signup(){
             let that = this
        if (this.$refs.form.validate()) {
          this.loading1 = true
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
        this.loading1 = false
        }
      
    }
  }
}
</script>
