<template>
    <div class="mb-3">
        <label for="value">Desea crear la Cuenta Forex?</label>
    </div>
    <div class="mb-3" style="text-align: center;">
        <Button label="Guardar" @click="newExchange()"/>
    </div>
</template>

<script>
import gql from "graphql-tag";

import Button from 'primevue/button';

export default {
    name: 'NewAccountF',
    components: {
        Button
    },
    data() {
        return {
                usernameLoged: localStorage.getItem('username') || ''
            }
    },
    methods: {
        newExchange() {
                this.$apollo.mutate({
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
                }
            }).then(response => {
                this.$emit('NewAccountF', response.data.newAccountf);
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