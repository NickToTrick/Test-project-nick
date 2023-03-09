<template>
  <div class="login-container">
    <v-card class="login-holder">
      <v-card-title>Login</v-card-title>
      <v-card-text>
        <v-form>
          <v-text-field
            v-model="state.email"
            :error-messages="v$.email.$errors.map(e => e.$message)"
            label="Email"
            type="email"
            @input="v$.email.$touch"
            @blur="v$.email.$touch"
            required
          />
          <v-text-field
            v-model="state.password"
            :error-messages="v$.password.$errors.map(e => e.$message)"
            label="Password"
            type="password"
            @input="v$.password.$touch"
            @blur="v$.password.$touch"
            required
          />
          <v-btn @click="login" :disabled="v$.$invalid">Login</v-btn>
        </v-form>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
import {reactive, ref} from 'vue'
import router from "@/router";
import {useVuelidate} from '@vuelidate/core';
import {email, minLength, required} from '@vuelidate/validators'

export default {
  setup() {
    const initialState = {
      email: '',
      password: '',
    }
    const state = reactive({
      ...initialState,
    })

    const rules = {
      email: [required, email],
      password: [required, minLength(6)],
    }

    const v$ = useVuelidate(rules, state)

    const login = () => {
      v$.value.$touch()

      if (!v$.value.$invalid) {
        router.push('/game')
      }
    }

    return {
      state,
      v$,
      login,
    }
  }
}
</script>

<style scoped>
 .login-container {
   display: flex;
   align-items: center;
   justify-content: center;
   height: 100%;
 }
 .login-holder {
   max-width: 500px;
   width: 100%;
 }
</style>
