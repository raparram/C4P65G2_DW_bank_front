<template>
    <div class="mb-3">
        <label for="types">Tipo</label>
        <Dropdown 
        v-model="selectedType"
        :options="types" 
        optionLabel="name" 
        placeholder="Seleccione un Tipo" />
    </div>
    <div class="mb-3">
        <label for="value">Valor a transar</label>
        <InputNumber 
        v-model="value" 
        mode="decimal" :minFractionDigits="2" />
    </div>
    <div class="mb-3" style="text-align: center;">
        <Button label="Realizar transacción" @click="newTransaction()"/>
    </div>
</template>

<script>
import gql from "graphql-tag";
import Dropdown from 'primevue/dropdown';
import Button from 'primevue/button';
import InputNumber from 'primevue/inputnumber';

export default {
    name: 'NewTransaction',
    components: {
        Dropdown,
        Button,
        InputNumber
    },
    data() {
        return {
                selectedType: null,
                types: [
                    {name: 'Compra (COP --> USD)', code: 'compra'},
                    {name: 'Venta (USD --> COP) ', code: 'venta'},
                ],
                value: 0,
                usernameLoged: localStorage.getItem('username') || ''
            }
    },
    methods: {
        newTransaction() {
                console.log(this.selectedType, this.value, this.usernameLoged);
                this.$apollo.mutate({
                mutation: gql`
                mutation ($username: String!, $type: String!, $originAmount: Float!) {
                newTransactionf(username: $username, type: $type, originAmount: $originAmount) {
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
                    originAmount: this.value
                }
            }).then(response => {
                this.$emit('newTransaction', response.data.newTransactionf);
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