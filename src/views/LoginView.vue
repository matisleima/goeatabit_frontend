<template>
  <div>
    <SignupModal ref="signupModalRef" @event-user-registration-success="showSignupSuccessMessage"/>

    <div class="container  mt-5" @keydown.enter="login">
      <div class="row">

      </div>

      <div class="row">
        <div class="col col-9">
          <img src="../assets/logouus.png" height="125" width="500" style="margin-left: -260px"/>
        <div style=" font-style: italic; text-align: justify; font-size: 20px; margin-left: 90px; margin-right: 90px ">
<!--          Jaga oma kokkamiskirge ja kohtu uute inimestega! Go Eat A Bit'iga avastad võrratuid maitseid, paned end-->
<!--          proovile võõrustajana ning veedad aega parimas seltskonnas! Ühine meiega ja kliki end sööma!-->

          <p>"Enne Go Eat A Biti avastamist tõin igal lõunal Rimist pitsat ja näksisin üksi oma laua taga.
          Nüüd aga kohtun iga päev lahedate inimestega, me naudime head toitu ja mis kõige tähtsam - see on odav!"</p>
          <p style="text-align: right">- Rain, 39 </p>
        </div>
        </div>

        <div class="col-3">
          <div class="d-grid gap-3">
            <div class="col">
              <AlertDanger :alert-message="errorResponse.message"/>
              <AlertSuccess :alert-message="signupSuccessMessage"/>
            </div>
            <input v-model="email" type="text" class="form-control" id="exampleInputEmail1"
                   placeholder="Sisesta email">
            <input v-model="password" type="password" class="form-control" id="exampleInputPassword1"
                   placeholder="Sisesta parool">
            <button @click="login" type="submit" class="btn btn-secondary">Logi sisse</button>
            <button @click="openSignupModal" type="submit" class="btn btn-secondary">Loo kasutaja</button>
          </div>
        </div>
      </div>

      <div class="container mt-5">
        <div class="row" style="font-size: 20px">
          <div class="col col-7 ">

          </div>
          <div class="col">

          </div>
        </div>
      </div>

      <div class="container">
        <div class="row" style="font-size: 30px">
          <div class="col col-10 ">
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import router from "@/router";
import SignupModal from "@/components/modal/SignupModal.vue";
import AlertDanger from "@/components/alert/AlertDanger.vue";
import AlertSuccess from "@/components/alert/AlertSuccess.vue";


export default {
  name: "LoginView",
  components: {AlertSuccess, AlertDanger, SignupModal},

  data() {
    return {
      signupSuccessMessage: '',

      email: '',
      password: '',
      loginResponse: {
        userId: 0
      },
      errorResponse: {
        message: '',
        errorCode: 0
      }
    }
  },
  methods: {
    login() {
      this.resetSuccessMessage()
      this.$http.get("/login", {
            params: {
              email: this.email,
              password: this.password
            }
          }
      ).then(response => {
        // Siit saame kätte JSONi  ↓↓↓↓↓↓↓↓
        this.loginResponse.userId = response.data.userId
        sessionStorage.setItem('userId', this.loginResponse.userId)
        this.resetErrorMessage()
        this.goToHome()
      }).catch(error => {
        // Siit saame kätte errori JSONi  ↓↓↓↓↓↓↓↓

        if (error.response.status === 500) {
          router.push({name: 'errorRoute'})
        }

        this.errorResponse = error.response.data


      })
    },
    goToHome() {
      router.push({name: 'homeRoute'})
    },
    resetErrorMessage() {
      this.errorResponse.message = ''
    },

    resetSuccessMessage() {
      this.signupSuccessMessage = ''
    },

    getAndSetUserIdFromSessionStorage() {
      this.loginResponse.userId = sessionStorage.getItem("userId")
    },

    openSignupModal() {
      this.$refs.signupModalRef.$refs.modalRef.openModal()
      this.resetErrorMessage()
      this.resetSuccessMessage()
      this.email = ''
      this.password = ''
    },

    showSignupSuccessMessage(successMessage) {
      this.signupSuccessMessage = successMessage
    }

  },
  mounted() {
    this.getAndSetUserIdFromSessionStorage()
    if (this.loginResponse.userId !== null) {
      this.goToHome()
    }

  }
}

</script>


<style>
body {
  background-image: url("../assets/transparent2.png");
  background-size: cover;
}
</style>
