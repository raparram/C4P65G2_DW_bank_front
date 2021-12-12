<template>
    <div class="sing-up">
        <div class="sing-up-form">
            <div class="container mt-5">
                <div class="row">
                    <div class="col-12 col-sm-6 col-md-4 mb-2">
                        <label for="first_name">Nombre</label>
                        <div>
                            <InputText
                                id="first_name"
                                type="text"
                                v-model="first_name"
                            />
                        </div>
                    </div>
                    <div class="col-12 col-sm-6 col-md-4 mb-2">
                        <label for="last_name">Apellido</label>
                        <div>
                            <InputText
                                id="last_name"
                                type="text"
                                v-model="last_name"
                            />
                        </div>
                    </div>
                    <div class="col-12 col-sm-6 col-md-4 mb-2">
                        <label for="username">Nombre de usuario</label>
                        <div>
                            <InputText
                                id="username"
                                type="text"
                                v-model="username"
                            />
                        </div>
                    </div>
                    <div class="col-12 col-sm-6 col-md-4 mb-2">
                        <label for="email">Correo electrónico</label>
                        <div>
                            <InputText
                                id="email"
                                type="email"
                                v-model="email"
                            />
                        </div>
                    </div>
                    <div class="col-12 col-sm-6 col-md-4 mb-2">
                        <label for="password">Contraseña</label>
                        <div>
                            <InputText
                                id="password"
                                type="password"
                                v-model="password"
                            />
                        </div>
                    </div>
                    <div class="col-12 col-sm-6 col-md-4 mb-2">
                        <label for="balance">Depósito</label>
                        <div>
                            <InputNumber
                                v-model="balance"
                                mode="currency"
                                currency="COP"
                                locale="es-CO"
                            />
                        </div>
                    </div>
                </div>
                <Button label="Registrarme" @click="signUp()" />
                <Button
                    label="¿Tienes usuario? ¡Inicia sesión!"
                    class="p-button-text"
                    v-on:click="goToSignIn()"
                />
            </div>
        </div>
        <div class="ourBank-sign-up">ourBank</div>
    </div>
</template>

<script>
import gql from "graphql-tag";
import Swal from "sweetalert2";
import InputText from "primevue/inputtext";
import InputNumber from "primevue/inputnumber";
import Button from "primevue/button";

export default {
    name: "SignUp",
    components: {
        InputText,
        Button,
        InputNumber,
    },
    data() {
        return {
            username: "",
            first_name: "",
            last_name: "",
            email: "",
            password: "",
            balance: "",
        };
    },
    methods: {
        signUp() {
            var msn = "";
            var error = false;
            if (!this.first_name) {
                msn += "Ingresa tu nombre.<br>";
                error = true;
            }
            if (!this.last_name) {
                msn += "Ingresa tu apellido.<br>";
                error = true;
            }
            if (!this.username || this.username.length < 6) {
                msn += "Ingresa un usuario valido, mínimo 6 caracteres.<br>";
                error = true;
            }
            if (
                !this.email ||
                this.email.length < 6 ||
                !this.email.includes("@") ||
                !this.email.includes(".") ||
                this.email.includes(",")
            ) {
                msn += "Ingresa un correo electrónico valido.<br>";
                error = true;
            }
            if (!this.password || this.password.length < 6) {
                msn +=
                    "Ingresa una contraseña valida, mínimo 6 caracteres.<br>";
                error = true;
            }
            if (!this.balance || this.balance < 50000) {
                msn += "El deposito minimo aceptado es $50.000 COP.<br>";
                error = true;
            }
            if (error == false) {
                Swal.close();
                Swal.fire({
                    title: "Creando usuario",
                    html: "Espera un momento mientras creamos tu cuenta...",
                    allowOutsideClick: false,
                    didOpen: () => {
                        Swal.showLoading();
                    },
                });
                this.$apollo
                    .mutate({
                        mutation: gql`
                            mutation (
                                $username: String!
                                $firstName: String!
                                $lastName: String!
                                $email: String!
                                $password: String!
                                $balance: Int!
                            ) {
                                newUserWithAccount(
                                    username: $username
                                    first_name: $firstName
                                    last_name: $lastName
                                    email: $email
                                    password: $password
                                    balance: $balance
                                ) {
                                    id
                                    username
                                    first_name
                                    last_name
                                    email
                                    balance
                                }
                            }
                        `,
                        variables: {
                            username: this.username,
                            firstName: this.first_name,
                            lastName: this.last_name,
                            email: this.email,
                            password: this.password,
                            balance: this.balance,
                        },
                    })
                    .then((response) => {
                        console.log(response);
                        Swal.close();
                        Swal.fire(
                            "Usuario creado",
                            `El usuario ${response.data.newUserWithAccount.username} ha sido creado satisfactoriamente`,
                            "success"
                        );
                        this.goToSignIn();
                    })
                    .catch((e) => {
                        Swal.close();
                        Swal.fire({
                            icon: "warning",
                            title: "¡Lo siento!",
                            text: "Usuario ya usado, intenta con otro.",
                            showClass: {
                                popup: "animate__animated animate__fadeInDown",
                            },
                            hideClass: {
                                popup: "animate__animated animate__fadeOutUp",
                            },
                        });
                        console.log(JSON.stringify(e, null, 2));
                    });
            } else {
                Swal.close();
                Swal.fire({
                    icon: "error",
                    title: "Por favor ...",
                    html: msn,
                    showClass: {
                        popup: "animate__animated animate__fadeInDown",
                    },
                    hideClass: {
                        popup: "animate__animated animate__fadeOutUp",
                    },
                });
            }
        },
        goToSignIn() {
            this.$router.push({ name: "signIn" });
        },
    },
};
</script>

<style scoped>
.p-inputtext {
    width: 100%;
}
</style>
