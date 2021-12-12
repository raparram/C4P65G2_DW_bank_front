<template>
    <div class="mb-3">
        <label for="value">1 USD = </label>
        <InputNumber 
        v-model="value" 
        mode="currency" 
        currency="COP" 
        locale="es-CO"/>
    </div>
    <div class="mb-3">
        <label for="value1">% Comisión Compra y Venta</label>
        <InputNumber 
        v-model="value1" 
        mode="decimal" :minFractionDigits="2" />
    </div>
    <div class="mb-3" style="text-align: center;">
        <Button label="Guardar" @click="newExchange()"/>
    </div>
</template>

<script>
import gql from "graphql-tag";

import Button from 'primevue/button';
import InputNumber from 'primevue/inputnumber';

export default {
    name: 'NewTasa',
    components: {
        Button,
        InputNumber
    },
    data() {
        return {
                value: 0,
                value1: 0,
                usernameLoged: localStorage.getItem('username') || ''
            }
    },
    methods: {
        newExchange() {
                this.$apollo.mutate({
                mutation: gql`
                mutation ($commissionPercentage: Float!, $usdRate: Float!, $usernameCreator: String!) {
                newExchange(
                    commissionPercentage: $commissionPercentage, 
                    usdRate: $usdRate, 
                    usernameCreator: $usernameCreator) {
                    id
                    dateCreation
                    commissionPercentage
                    usdRate
                    usernameCreator
                }
                }
                `,
                variables: {
                    usernameCreator: this.usernameLoged,
                    usdRate: this.value,
                    commissionPercentage: this.value1
                }
            }).then(response => {
                this.$emit('NewTasa', response.data.newExchange);
            }).catch(e => {
                //console.log(e);
                console.log(JSON.stringify(e, null, 2));
            });
            
        }
    }
}
</script>

<style scoped>
.p-dropdown,
.p-inputnumber {
    width: 100%;
}
</style>