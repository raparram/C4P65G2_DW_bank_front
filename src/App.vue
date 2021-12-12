<template>
    <router-view v-on:login="completeLogin" v-on:logout="logOut"> </router-view>
</template>

<script>
export default {
    name: "App",
    components: {},
    methods: {
        completeLogin: function (data) {
            for (let i in data) {
                localStorage.setItem(i, data[i]);
            }
            this.$router.push({ name: "home" });
        },
        logOut: function () {
            localStorage.clear();
            this.$router.push({ name: "signIn" });
        },
    },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Barlow+Semi+Condensed:wght@700&display=swap");

* {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
}

html,
body {
    height: 100%;
    margin: 0;
    display: flex;
    flex-direction: column;
}

.sign-in {
    background-image: url("assets/images/signIn.jpg");
    height: 100vh;
    overflow-x: hidden;
    overflow-y: hidden;
    background-repeat: no-repeat;
    background-position: right 0px top 0px;
    background-size: auto 100vh;
    box-shadow: 400px 0 80px 0 white inset;
}

.sing-up {
    display: flex;
    flex-direction: column;
    height: 100vh;
    margin: 0;
}

.sing-up-form {
    height: 300px;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
}

.ourBank-sign-up {
    background-image: url("assets/images/signUp.jpg");
    height: 100%;
    width: 100%;
    overflow-x: hidden;
    overflow-y: hidden;
    background-repeat: no-repeat;
    bottom: 0;
    background-size: 100vw auto;
    box-shadow: 0 150px 80px 0 white inset;
    font-family: "Barlow Semi Condensed", sans-serif;
    color: rgb(45, 87, 44);
    font-size: 75px;
    text-align: center;
}

.ourBank-sign-in {
    font-family: "Barlow Semi Condensed", sans-serif;
    position: fixed;
    bottom: 7%;
    left: 7%;
    color: rgb(45, 87, 44);
    font-size: 75px;
}

.ourBank-nav {
    font-family: "Barlow Semi Condensed", sans-serif;
    color: rgb(45, 87, 44);
    font-size: 40px;
    text-align: right;
}
</style>
