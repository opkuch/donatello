<template>
  <section class="inner-section">
    <div class="section-wrapper">
      <div class="account-form">
        <div class="login flex flex-column">
          <h3 v-if="isSignIn">Log in to Donatello</h3>
          <h3 v-else>Sign up to Donatello</h3>
          <form class="login-form flex flex-column" @submit.prevent="login">
            <input
              class="login-input fullname"
              type="text"
              placeholder="Enter full name"
              v-model="fullname"
              v-if="!isSignIn"
            />

            <input
              class="login-input username"
              type="username"
              placeholder="Enter username"
              v-model="credentials.username"
            />
            <input
              class="login-input password"
              type="password"
              placeholder="Enter password"
              autocomplete="off"
              v-model="credentials.password"
            />
            <div class="upload-for-signup flex flex-column" v-if="!isSignIn">
              <h4>Add an avatar (optional)</h4>
              <h3 class="small-title">Upload from computer</h3>
              <div class="upload-preview">
                <label for="file-upload"><span>Upload</span></label
                ><input
                  type="file"
                  accept="img/*"
                  id="file-upload"
                  @change="handleFile"
                />
              </div>
              <div class="from-web">
                <h3 class="small-title">Attach a link</h3>
                <label for="web-url">
                  <input
                    placeholder="Enter an image url here..."
                    class="login-input"
                    type="text"
                    v-model="userImgUrl"
                    :style="{ display: 'inline', width: '100%' }"
                  />
                </label>
              </div>
            </div>
            <input
              v-if="isSignIn"
              class="login-btn"
              type="submit"
              value="Log in"
              @click="login"
            />
            <input
              v-else
              class="login-btn"
              type="submit"
              value="Sign up"
              @click="signup"
            />
          </form>
          <span :style="{ textAlign: 'center', padding: '0.6em' }">Or</span>
          <div
            class="login-options flex flex-column justify-center align-center"
          >
            <button
              type="button"
              class="google-login-btn flex align-center justify-center"
            >
              <div
                style="
                  margin-right: 10px;
                  background: rgb(255, 255, 255);
                  padding: 10px;
                  border-radius: 2px;
                "
              >
                <svg width="18" height="18" xmlns="http://www.w3.org/2000/svg">
                  <g fill="#000" fill-rule="evenodd">
                    <path
                      d="M9 3.48c1.69 0 2.83.73 3.48 1.34l2.54-2.48C13.46.89 11.43 0 9 0 5.48 0 2.44 2.02.96 4.96l2.91 2.26C4.6 5.05 6.62 3.48 9 3.48z"
                      fill="#EA4335"
                    ></path>
                    <path
                      d="M17.64 9.2c0-.74-.06-1.28-.19-1.84H9v3.34h4.96c-.1.83-.64 2.08-1.84 2.92l2.84 2.2c1.7-1.57 2.68-3.88 2.68-6.62z"
                      fill="#4285F4"
                    ></path>
                    <path
                      d="M3.88 10.78A5.54 5.54 0 0 1 3.58 9c0-.62.11-1.22.29-1.78L.96 4.96A9.008 9.008 0 0 0 0 9c0 1.45.35 2.82.96 4.04l2.92-2.26z"
                      fill="#FBBC05"
                    ></path>
                    <path
                      d="M9 18c2.43 0 4.47-.8 5.96-2.18l-2.84-2.2c-.76.53-1.78.9-3.12.9-2.38 0-4.4-1.57-5.12-3.74L.97 13.04C2.45 15.98 5.48 18 9 18z"
                      fill="#34A853"
                    ></path>
                    <path fill="none" d="M0 0h18v18H0z"></path>
                  </g>
                </svg>
              </div>
              <span style="padding: 10px 10px 10px 0px; font-weight: 500"
                >Continue with Google</span
              ></button
            ><a v-if="isSignIn"
              ><span class="link" @click="$router.push('/signup'); toggleIsSignIn()"
                >Sign up</span
              >
              for an account</a
            >
            <a v-else
              >Already have an account?
              <span
                class="link"
                @click="
                  $router.push('/login');
                  toggleIsSignIn()
                "
                >Log in</span
              ></a
            >
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { uploadImg } from '@/services/img-upload.service'

export default {
  name: 'loginForm',
  props: {},
  data() {
    return {
      credentials: {
        username: null,
        password: null,
      },
      fullname: null,
      userImgUrl: null,
      isSignIn: true,
    }
  },
  created() {
    this.loggedinUser = this.$store.getters.user
    const { loginOrSignup } = this.$route.params
    if (loginOrSignup === 'signup') this.isSignIn = false
  },
  methods: {
    login() {
      this.$emit('login', JSON.parse(JSON.stringify(this.credentials)))
    },
    signup() {
      if (
        !this.fullname ||
        !this.credentials.username ||
        !this.credentials.password
      ) return
        
      
      const signupInfo = JSON.parse(JSON.stringify(this.credentials))
      signupInfo.fullname = this.fullname
      this.userImgUrl
        ? (signupInfo.imgUrl = this.userImgUrl)
        : (signupInfo.imgUrl = '')
      this.$emit('signup', signupInfo)
    },
    handleFile(ev) {
      var file
      if (ev.type === 'change') file = ev.target.files[0]
      else if (ev.type === 'drop') file = ev.dataTransfer.files[0]
      this.onUploadFile(file)
    },
    async onUploadFile(file) {
      // this.isLoading = true
      const res = await uploadImg(file)
      // this.isLoading = false
      this.userImgUrl = res.url
    },
    toggleIsSignIn() {
      this.isSignIn = !this.isSignIn
      this.clearFields()
    },
  },
}
</script>