<template>
    <div class="mb-3">
        <label for="types">Tipo</label>
        <Dropdown
            v-model="selectedType"
            :options="types"
            optionLabel="name"
            placeholder="Seleccione un Tipo"
        />
    </div>
    <div class="mb-3">
        <label for="value">Valor a transar</label>
        <InputNumber v-model="value" mode="decimal" :minFractionDigits="2" />
    </div>
    <div class="mb-3" style="text-align: center">
        <Button label="Realizar transacción" @click="newTransaction()" />
    </div>
</template>

<script>
import gql from "graphql-tag";
import Dropdown from "primevue/dropdown";
import Button from "primevue/button";
import InputNumber from "primevue/inputnumber";
import Swal from "sweetalert2";

export default {
    name: "NewTransaction",
    components: {
        Dropdown,
        Button,
        InputNumber,
    },
    data() {
        return {
            selectedType: null,
            types: [
                { name: "Compra (COP --> USD)", code: "compra" },
                { name: "Venta (USD --> COP) ", code: "venta" },
            ],
            value: 0,
            usernameLoged: localStorage.getItem("username") || "",
        };
    },
    methods: {
        newTransaction() {
            console.log(this.selectedType, this.value, this.usernameLoged);
            var errorInput = false;
            if (this.selectedType == null) {
                alert(
                    "Seleccione el tipo de transacción entre 'compra' y 'venta'"
                );
                errorInput = true;
            }
            if (this.value <= 0) {
                alert("Ingrese un valor a transar mayor a $0");
                errorInput = true;
            }
            if (!errorInput) {
                this.$apollo
                    .mutate({
                        mutation: gql`
                            mutation (
                                $username: String!
                                $type: String!
                                $originAmount: Float!
                            ) {
                                newTransactionf(
                                    username: $username
                                    type: $type
                                    originAmount: $originAmount
                                ) {
                                    id
                                    username
                                    type
                                    usdRate
                                    originAmount
                                    destinationAmount
                                    commissionPercentage
                                    cop2user
                                    cop2bank
                                    date
                                }
                            }
                        `,
                        variables: {
                            username: this.usernameLoged,
                            type: this.selectedType.code,
                            originAmount: this.value,
                        },
                    })
                    .then((response) => {
                        this.$emit(
                            "newTransaction",
                            response.data.newTransactionf
                        );
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
                        if (
                            msnError.includes(
                                "El monto origen debe ser mayor a cero"
                            )
                        ) {
                            alert("El valor a transar debe ser mayor a cero.");
                        }
                        if (
                            msnError.includes(
                                "no posee fondos necesarios en USD"
                            )
                        ) {
                            alert(
                                "El ususario no tiene dinero suficiente en la cuenta forex"
                            );
                        }
                        if (
                            msnError.includes(
                                "Error: Dinero insuficiente en la cuenta"
                            )
                        ) {
                            alert(
                                "El ususario no tiene dinero suficiente en la cuenta bancaria"
                            );
                        }
                    });
            }
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
