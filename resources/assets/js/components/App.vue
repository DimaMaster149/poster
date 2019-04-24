<template>
  <div class="page__wrap">
    <div class="header__wrap">
      <div class="nav-container">
        <div class="header">
          <div class="nav-icon">
            <router-link :to="{ name: 'yandex' }">
              <img
                src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/96/%D0%93%D0%B5%D1%80%D0%B1_%D0%97%D0%B0%D0%BF%D0%BE%D1%80%D0%BE%D0%B6%D1%8C%D1%8F_2003_%D0%B3%D0%BE%D0%B4%D0%B0.svg/1200px-%D0%93%D0%B5%D1%80%D0%B1_%D0%97%D0%B0%D0%BF%D0%BE%D1%80%D0%BE%D0%B6%D1%8C%D1%8F_2003_%D0%B3%D0%BE%D0%B4%D0%B0.svg.png"
                alt
              >
            </router-link>
          </div>
          <span class="mobile-burger">
            <span class="mobile-burger__button" @click="toggleMenu($event)">
              <i class="fas fa-bars"></i>
            </span>
          </span>
          <div class="nav__wrap">
            <div class="nav">
              <div class="nav__item">
                <router-link :to="{ name: 'yandex' }">Карта</router-link>
              </div>
              <div v-if="this.type == 0" class="nav__item">
                <router-link :to="{ name: 'propose' }">Предложить</router-link>
              </div>
              <div v-if="this.type === 1" class="nav__item">
                <router-link :to="{ name: 'admin' }">Добавить</router-link>
              </div>
            </div>
          </div>
          <div class="auth__wrap">
            <div class="auth">
              <div v-if="this.type === 2" class="auth__item">
                <a href="login">Войти</a>
              </div>
              <div v-if="this.type === 2" class="auth__item">
                <a href="register">Зарегистрироваться</a>
              </div>
              <div v-if="this.type !== 2" class="auth__item">
                <router-link to="/" @click.native="logout">Выйти</router-link>
              </div>
            </div>
          </div>
          <on-click-outside :do="hide" v-if="showMobileMenu">
            <div v-show="showMobileMenu" class="nav-mobile">
              <ul class="mobile-menu">
                <li class="mobile-menu__item" @click="hide($event)">
                  <router-link :to="{ name: 'yandex' }">Карта</router-link>
                </li>
                <li v-if="this.type == 0" class="mobile-menu__item" @click="hide($event)">
                  <router-link :to="{ name: 'propose' }">Предложить</router-link>
                </li>
                <li v-if="this.type === 1" class="mobile-menu__item" @click="hide($event)">
                  <router-link :to="{ name: 'admin' }">Добавить</router-link>
                </li>
                <li v-if="this.type === 2" class="mobile-menu__item" @click="hide($event)">
                  <a href="login">Войти</a>
                </li>
                <li v-if="this.type === 2" class="mobile-menu__item" @click="hide($event)">
                  <a href="register">Зарегистрироваться</a>
                </li>
                <li v-if="this.type !== 2" class="mobile-menu__item" @click="hide($event)">
                  <router-link to="/" @click.native="logout">Выйти</router-link>
                </li>
              </ul>
            </div>
          </on-click-outside>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import OnClickOutside from "./lib/OnClickOutside";

export default {
  name: "App",
  components: {
    OnClickOutside
  },
  // type: 0 - user, 1 - admin, 2 - unauthorized
  props: {
    type: Number
  },
  data() {
    return {
      csrf: document
        .querySelector('meta[name="csrf-token"]')
        .getAttribute("content"),
      showMobileMenu: false
    };
  },
  methods: {
    logout() {
      axios
        .post("logout")
        .then(response => {
          if (response.status === 302 || 401) {
            console.log("logout");
            window.location.href = "http://poster.loc/yandex";
          } else {
            // throw error and go to catch block
          }
        })
        .catch(error => {
          console.log(error);
        });
    },
    toggleMenu() {
      this.showMobileMenu = !this.showMobileMenu;
    },
    hide() {
      if (this.showMobileMenu === true) this.showMobileMenu = false;
    }
  }
};
</script>

<style>
body {
  margin: 0 !important;
  padding: 0;
}
a {
  color: black;
  text-decoration: none;
}
li {
  list-style-type: none;
}
ul {
  margin: 0;
  padding: 0;
}

.page__wrap {
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  height: 60px;
  z-index: 1;
}
.nav-icon {
  position: relative;
  top: 9px;
  left: 29px;
}
.nav-icon img {
  width: 42px;
  height: 42px;
}
.nav-container {
  max-width: 2000px;
  width: 100%;
}
.header__wrap {
  width: 100%;
  height: 60px;
  background-color: #ffefd5;
}
.header {
  display: flex;
  position: relative;
  justify-content: space-between;
  font-size: 20px;
  height: 60px;
  width: 100%;
}
.nav__wrap {
  margin-left: 50px;
}
.nav,
.auth {
  display: flex;
  flex-direction: row;
  align-items: center;
  height: 100%;
}
.nav__item,
.auth__item {
  display: block;
  min-width: 50px;
  max-width: 250px;
  height: 30px;
  padding: 0 10px 0 10px;
}
.nav__item a,
.auth__item a,
.mobile-menu__item a {
  text-decoration: none;
}
.nav__item a,
.auth__item a,
.mobile-menu__item {
  color: #00008b;
  font-weight: 600;
}
.nav__item a:hover,
.auth__item a:hover,
.mobile-menu__item a:hover {
  font-size: 21px;
}
.nav__item a:hover,
.auth__item a:hover,
.mobile-menu__item a:hover {
  color: #d2691e;
}
.auth__wrap {
  margin-right: 50px;
}
.auth {
  display: flex;
  flex-direction: row;
}
.nav-mobile {
  display: none;
  position: absolute;
  background-color: white;
  width: 100%;
  height: auto;
  top: 60px;
  z-index: 99999;
}
.mobile-menu__item {
  font-size: 20px;
  color: black;
  font-weight: 600;
  display: flex;
  justify-content: center;
}
.mobile-menu__item + .mobile-menu__item {
  border-top: 2px solid black;
}
.mobile-burger {
  display: none;
  position: relative;
  margin-right: 10px;
  justify-content: center;
  align-items: center;
  display: none;
  z-index: 999;
}
.mobile-burger__button {
  cursor: pointer;
}

@media screen and (max-width: 768px) {
  .nav__wrap {
    display: none;
  }
  .auth__wrap {
    display: none;
  }
  .nav-mobile {
    display: block;
  }
  .mobile-burger {
    display: flex;
  }
}
</style>