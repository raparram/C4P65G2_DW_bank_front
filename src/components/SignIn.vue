<template>
    <div class="sign-in">
        <div class="container mt-5">
            <div class="row mb-3">
                <div class="col-4">
                    <label for="username">Usuario</label>
                    <div>
                        <InputText
                            id="username"
                            type="text"
                            v-model="username"
                            required
                        />
                    </div>
                </div>
            </div>
            <div class="row mb-3">
                <div class="col-4">
                    <label for="password">Contraseña</label>
                    <div>
                        <InputText
                            id="password"
                            type="password"
                            v-model="password"
                            required
                        />
                    </div>
                </div>
            </div>
            <Button label="Iniciar sesión" @click="signIn()" />
            <Button
                label="¿No tienes usuario? ¡Regístrate!"
                class="p-button-text"
                v-on:click="goToSignUp()"
            />
        </div>
        <div class="ourBank-sign-in">ourBank</div>
    </div>
</template>

<script>
import gql from "graphql-tag";
import InputText from "primevue/inputtext";
import Button from "primevue/button";
import Swal from "sweetalert2";

export default {
    name: "SignIn",
    components: {
        InputText,
        Button,
    },
    created() {
        localStorage.clear();
    },
    data() {
        return {
            username: "",
            password: "",
        };
    },
    methods: {
        signIn() {
            if (!this.username || !this.password) {
                Swal.close();
                Swal.fire({
                    icon: "error",
                    title: "Oops...",
                    text: "Por favor ingrese un nombre de usuario y una contraseña valida",
                    showClass: {
                        popup: "animate__animated animate__fadeInDown",
                    },
                    hideClass: {
                        popup: "animate__animated animate__fadeOutUp",
                    },
                });
            } else {
                this.$apollo
                    .mutate({
                        mutation: gql`
                            mutation ($username: String!, $password: String!) {
                                login(
                                    username: $username
                                    password: $password
                                ) {
                                    access
                                    refresh
                                }
                            }
                        `,
                        variables: {
                            username: this.username,
                            password: this.password,
                        },
                    })
                    .then((response) => {
                        this.$emit("login", {
                            access_token: response.data.login.access,
                            refresh_token: response.data.login.refresh,
                            username: this.username,
                        });
                    })
                    .catch((e) => {
                        console.log(JSON.stringify(e, null, 2));
                        //alert("Por favor ingrese un nombre de usuario y una contraseña valida");
                        Swal.close();
                        Swal.fire({
                            icon: "error",
                            title: "Oops...",
                            text: "Por favor ingrese un usuario y una contraseña valida",
                            showClass: {
                                popup: "animate__animated animate__fadeInDown",
                            },
                            hideClass: {
                                popup: "animate__animated animate__fadeOutUp",
                            },
                        });
                    });
            }
        },
        goToSignUp() {
            this.$router.push({ name: "signUp" });
        },
    },
};
</script>

<style scoped>
.p-inputtext {
    width: 100%;
}
</style>
