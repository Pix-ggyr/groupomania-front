<template>
  <section class="body" @click="closePopup">
    <main class="container">
      <img
        id="logo"
        src="../assets/icon-above-font.png"
        alt="logo groupomania"
      />
      <h1>Log into our new application !</h1>
      <form id="login">
        <label for="user-email">Your email address</label><br />
        <input
          type="text"
          id="user-email"
          name="user-email"
          aria-label="user-email"
          placeholder="user@mail.com"
          v-model="email"
        /><br />
        <label for="password">Enter your password</label><br />
        <input
          type="password"
          id="password"
          name="password"
          aria-label="password"
          v-model="password"
        /><br />
        <input
          id="login-btn"
          type="submit"
          name="submit"
          aria-label="submit"
          value="Login"
          @click.prevent.stop="login()"
        />
      </form>
      <p>
        Not registered yet ?
        <a class="popup-trigger" @click.prevent.stop="showRegisterPopup()"
          >Sign-up right now !</a
        >
      </p>
    </main>
    <PopupRegister v-if="displayPopupRegister" />
    <Footer />
  </section>
</template>

<script>
import bus from '@/bus';
import Footer from '@/components/Footer';
import PopupRegister from '@/components/popups/PopupRegister';
import axios from 'axios';

export default {
  name: 'Login',
  components: {
    Footer,
    PopupRegister,
  },
  created() {
    bus.$on('close-popup', this.closeRegisterPopup);
  },
  data() {
    return {
      displayPopupRegister: false,
      email: '',
      password: '',
    };
  },
  methods: {
    showRegisterPopup() {
      this.displayPopupRegister = true;
    },
    closeRegisterPopup() {
      this.displayPopupRegister = false;
    },
    closePopup() {
      if (!this.$el.classList.contains('blurry')) return;
      bus.$emit('close-popup');
    },
    async login() {
      const res = await axios.post('http://localhost:3000/api/v1/user/login', {
        email: this.email,
        password: this.password,
      });
      const { accessToken, user } = res.data;
      bus.$emit('logged-in', { accessToken, user });
      window.location.pathname = '/';
    },
  },
};
</script>

<style lang="scss" scoped>
#login {
  display: flex;
  flex-flow: column nowrap;
}

#login input {
  margin: 10px;
  color: darkblue;
  background-color: white;
  font-family: 'Dosis', sans-serif;
  font-weight: 700;
  height: 40px;
  width: 220px;
  border: none;
  border-radius: 3px;
  padding-left: 5px;
  font-size: 16px;
}

#login label {
  font-size: 18px;
  padding-left: 10px;
  color: darkblue;
}

#login > #login-btn {
  background-color: rgb(19, 93, 230);
  color: white;
  text-transform: uppercase;
  box-shadow: rgba(0, 0, 0, 0.356) -2px 2px 0.1em;
  width: 100px;
  align-self: center;
}

a.popup-trigger {
  font-weight: bold;
  color: darkblue;
  cursor: pointer;
}
</style>
