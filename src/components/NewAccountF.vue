<template>
    <div class="mb-3" style="text-align: center">
        <label for="value">¿Desea crear su Cuenta Forex?</label>
    </div>
    <div class="mb-3" style="text-align: center">
        <Button label="Confirmar" @click="newExchange()" />
    </div>
</template>

<script>
import gql from "graphql-tag";
import Button from "primevue/button";
import Swal from "sweetalert2";

export default {
    name: "NewAccountF",
    components: {
        Button,
    },
    data() {
        return {
            usernameLoged: localStorage.getItem("username") || "",
        };
    },
    methods: {
        newExchange() {
            this.$apollo
                .mutate({
                    mutation: gql`
                        mutation ($username: String!) {
                            newAccountf(username: $username) {
                                username
                                usdAmount
                                lastChange
                            }
                        }
                    `,
                    variables: {
                        username: this.usernameLoged,
                    },
                })
                .then((response) => {
                    this.$emit("NewAccountF", response.data.newAccountf);
                })
                .catch((e) => {
                    console.log(JSON.stringify(e, null, 2));
                    var msnError = JSON.stringify(e);
                    if (msnError.includes("Sesión inactiva")) {
                        Swal.close();
                        Swal.fire({
                            icon: "warning",
                            title: "¡Sesión cerrada por inactividad!",
                            text: "Por favor volver a iniciar sesión.",
                            showClass: {
                                popup: "animate__animated animate__fadeInDown",
                            },
                            hideClass: {
                                popup: "animate__animated animate__fadeOutUp",
                            },
                        });
                        this.$router.push({ name: "signIn" });
                    }
                    if (msnError.includes("Failed to fetch")) {
                        Swal.close();
                        Swal.fire({
                            icon: "question",
                            title: "¿Y el internet?",
                            text: "¡Conectate para usar nuestros servicios!",
                        });
                        this.$router.go();
                    }
                });
        },
    },
};
</script>

<style scoped>
.p-dropdown,
.p-inputnumber {
    width: 100%;
}
</style>
