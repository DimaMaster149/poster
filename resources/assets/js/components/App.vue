<template>
    <div>
        <div class="header__wrap">
            <div class="nav-container">
                <div class="header">
                    <div class="nav-icon">
                        <router-link :to="{ name: 'yandex' }">
                            <img src="data:image/jpeg;base64,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" alt="">
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
                                <router-link to="/" @click.native="logout" >Выйти</router-link>
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
                                <router-link to="/" @click.native="logout" >Выйти</router-link>
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
import OnClickOutside from './lib/OnClickOutside'

export default {
    name: "App",
    components:{
        OnClickOutside
    },
    // type: 0 - user, 1 - admin, 2 - unauthorized
    props: {
        type: Number
    },
    data(){
        return{
            csrf: document.querySelector('meta[name="csrf-token"]').getAttribute('content'),
            showMobileMenu:false
        }
    },
    methods:{
        logout(){
            axios.post('logout').then(response => {
                if (response.status === 302 || 401) {
                    console.log('logout');
                    window.location.href = 'http://poster.loc/yandex';
                }
                else {
                    // throw error and go to catch block
                }
            }).catch(error => {
                console.log(error);
            });
        },
        toggleMenu(){
            this.showMobileMenu = !this.showMobileMenu;
        },
        hide(){
            if(this.showMobileMenu === true)
            this.showMobileMenu = false
        }
    },
}
</script>

<style>
body{
    margin: 0 !important;
    padding: 0;
}
a{
    color:black;
    text-decoration: none;
}
li{
   list-style-type: none;
}
ul{
    margin:0;
    padding:0;

}
.nav-icon{
    position: relative;
    top:9px;
    left:29px;
}
.nav-icon img{
    width: 42px;
    height: 42px;
}
.nav-container{
    max-width: 2000px;
    width:100%;
}
.header__wrap{
    width:100%;
    height: 60px;
    background-color: #FFEFD5;
}
.header{
    display:flex;
    position: relative;
    justify-content: space-between;
    font-size:20px;
    height:60px;
    width:100%;

}
.nav__wrap{
    margin-left: 50px;
}
.nav, .auth{
    display:flex;
    flex-direction: row;
    align-items: center;
    height: 100%;
}
.nav__item, .auth__item{
    display:block;
    min-width:50px;
    max-width:250px;
    height: 30px;
    padding: 0 10px 0 10px;
}
.nav__item a, .auth__item a, .mobile-menu__item a{
    text-decoration: none;
}
.nav__item a, .auth__item a, .mobile-menu__item{
    color:#00008B;
    font-weight: 600;
}
.nav__item a:hover, .auth__item a:hover, .mobile-menu__item a:hover{
    font-size:21px;
}
.nav__item a:hover,
.auth__item a:hover,
.mobile-menu__item a:hover
{
    color:#D2691E;
}
.auth__wrap{
    margin-right: 50px;
}
.auth{
    display:flex;
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
.mobile-menu__item{
    font-size: 20px;
    color:black;
    font-weight: 600;
    display:flex;
    justify-content: center;
}
.mobile-menu__item + .mobile-menu__item{
    border-top:2px solid black;
}
.mobile-burger{
    display: none;
    position: relative;
    margin-right: 10px;
    justify-content: center;
    align-items: center;
    display:none;
    z-index: 999;
}
.mobile-burger__button{
    cursor: pointer;
}

@media screen and (max-width: 768px) {
.nav__wrap{
    display:none;
}
.auth__wrap{
    display:none;
}
.nav-mobile{
    display:block;
}
.mobile-burger{
    display:flex;
}
}
</style>