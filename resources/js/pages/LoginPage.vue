<template >
  <div class="centered-container">
    <md-content class="md-elevation-3">

      <div class="title">
        <img src="https://vuematerial.io/assets/logo-color.png">
        <div class="md-title">Vue Material</div>
        <div class="md-body-1">Build beautiful apps with Material Design and Vue.js</div>
      </div>

      <div class="form">
        <md-field>
          <label>Username</label>
          <md-input v-model="name" @keyup.enter="submit"  autofocus></md-input>
        </md-field>

        <md-field md-has-password>
          <label>Password</label>
          <md-input v-model="password" type="password" @keyup.enter="submit"></md-input>
        </md-field>
      </div>

      <div class="actions md-layout md-alignment-center-space-between">
        <a href="/resetpassword">Reset password</a>
        <md-button class="md-raised md-primary" @click="submit">Log in</md-button>
      </div>

      <div class="loading-overlay" v-if="loading">
        <md-progress-spinner md-mode="indeterminate" :md-stroke="2"></md-progress-spinner>
      </div>

    </md-content>
    <div class="background" />
  </div>
</template>

<script>
import rf from '../requests/RequestFactory';
import AuthenticationUtils from '../common/AuthenticationUtils';

export default {
    name: 'LoginPage',
    data () {
      return {
        name: '',
        password: '',
        isLoading: false,
        loading: false
      };
    },
    mounted () {
      this.name = this.$route.query.name;
    },
    methods: {
      submit () {
          const params = {
            name: this.name,
            password: this.password,
          };
          this.login(params);
      },
      login (params) {
        return rf.getRequest('UserRequest').login(params).then(async (response) => {
          this.loading = true;
          await AuthenticationUtils.login(response);
          rf.getRequest('UserRequest').getCurrentUser().then((user)=>{
            if(user.role == 2) {
              this.$router.push({ path: '/admin' });
            } else {
              this.$router.push({ path: '/' });
            }
          });
        }).catch(async (error) => {
          console.log(error);
        }).finally(() => {
          this.loading = false;
        });
      },
    },
  };
</script>

<style lang="scss">
.centered-container {
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  height: 100vh;
  .title {
    text-align: center;
    margin-bottom: 30px;
    img {
      margin-bottom: 16px;
      max-width: 80px;
    }
  }
  .actions {
    .md-button {
      margin: 0;
    }
  }
  .form {
    margin-bottom: 60px;
  }
  .background {
    position: absolute;
    height: 100%;
    width: 100%;
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    z-index: 0;
  }
  .md-content {
    z-index: 1;
    padding: 40px;
    width: 100%;
    max-width: 400px;
    position: relative;
  }
  .loading-overlay {
    z-index: 10;
    top: 0;
    left: 0;
    right: 0;
    position: absolute;
    width: 100%;
    height: 100%;
    background: rgba(255, 255, 255, 0.9);
    display: flex;
    align-items: center;
    justify-content: center;
  }
}
</style>
