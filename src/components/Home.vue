<template>
    <Navbar :logOut="logOut"/>
    <!-- INICIO DE DIFERENTE DE ADMIN -->
    <div class="container mt-5" v-if="account.username != 'admin'">
        <div class="row">
            <div class="col-6">
                <Card :style="{'width': '100%', 'height': '100%'}">
                    <template #header>
                        <img src="https://cdni.rt.com/actualidad/public_images/2021.01/article/600adf4ce9ff710ad017b99e.jpg" style="height: 15rem" />
                    </template>
                    <template #title>
                        Información de la Cuenta Forex
                    </template>
                    <template #subtitle>
                        Nombre de usuario: <b>{{account?.username}}</b>
                    </template>
                    <template #content>
                        <div class="row">
                            <div class="col-4">
                                <p>Saldo: </p>
                                <p>Fecha: </p>
                                <p>1 USD: </p>
                                <p>Comisión Compra o Venta: </p>
                            </div>
                            <div class="col-8">
                                <p><b>{{accountf?.usdAmount}} USD</b></p>
                                <p><b>{{prettyDate(accountf?.lastChange)}}</b></p>
                                <p><b>{{exchactual?.usdRate}} COP</b></p>
                                <p><b>{{exchactual?.commissionPercentage}} %</b></p>
                            </div>
                        </div>
                        <Button label="Nueva Transacción Forex" @click="display = true" v-if="accountf?.usdAmount >= 0"/>
                        <Button label="Crear Cuenta Forex" @click="displayNewAccountF = true" v-else/>
                    </template>
                </Card>
            </div>
            <div class="col-6">
                <Card :style="{'width': '100%', 'height': '100%'}  ">
                    <template #header>
                        <img src="https://d31dn7nfpuwjnm.cloudfront.net/images/valoraciones/0034/9426/que-es-estado-cuenta-bancaria.jpg?1567050320" style="height: 15rem" />
                    </template>
                    <template #title>
                        Información de la Cuenta Bancaria
                    </template>
                    <template #subtitle>
                        Propietario de la cuenta: <b>{{account?.username}}</b>
                    </template>
                    <template #content>
                        <div class="row">
                            <div class="col-4">
                                <p>Saldo: </p>
                                <p>Fecha: </p>
                            </div>
                            <div class="col-8">
                                <p><b>$ {{account?.balance}} COP</b></p>
                                <p><b>{{prettyDate(account?.lastChange)}}</b></p>
                            </div>
                        </div>
                    </template>
                </Card>
            </div>
        </div>
        <div class="mt-3">
            <div style="text-align: center;">
                <h2>Mis transacciones Forex</h2>
            </div>
            <DataTable :value="transactions" ref="dt" v-if="transactions.length > 0"
                :paginator="true" :rows="5">
                <Column field="id" header="ID transacción"></Column>
                <Column field="commissionPercentage" header="% Comisión"></Column>
                <Column field="usdRate" header="Tasa de Cambio USD"></Column>
                <Column field="type" header="Tipo"></Column>
                <Column field="originAmount" header="Cantidad Origen">
                    <template #body="row">
                        {{ row.data.originAmount }} {{row.data.type == "compra" ? 'COP' : 'USD'}}
                    </template>
                </Column>
                <Column field="destinationAmount" header="Cuenta Forex USD">
                    <template #body="row">
                        {{row.data.type == "compra" ? row.data.destinationAmount : row.data.originAmount * -1}}
                    </template>
                </Column>
                <Column field="cop2user" header="Cuenta Bancaria COP">
                    <template #body="row">
                        {{row.data.type == "compra" ? row.data.originAmount * -1  : row.data.cop2user }}
                    </template>
                </Column>
                <Column field="date" header="Fecha" :sortable="true">
                    <template #body="row">
                        {{prettyDate(row.data.date)}}
                    </template>
                </Column>
            </DataTable>
        </div>
    </div>
    <Dialog header="Nueva Transacción Forex" v-model:visible="display"
    :style="{width: '50vw'}">
        <NewTransaction v-on:newTransaction="onNewTransaction"/>
    </Dialog>
    <Dialog header="Crear Cuenta Forex" v-model:visible="displayNewAccountF"
    :style="{width: '50vw'}">
        <NewAccountF v-on:newAccountF="onNewAccountF"/>
    </Dialog>
    <!-- FINAL DE DIFERENTE DE ADMIN -->

    <!-- INICIO DE ADMIN -->
    <div class="container mt-5" v-if="account.username == 'admin'">
        <div class="row">
            <div class="col-6">
                <Card :style="{'width': '100%', 'height': '100%'}">
                    <template #header>
                        <img src="https://www.semana.com/resizer/L_Ypx9GqBOUozQz4KD5Wow-aZVI=/1200x675/filters:format(jpg):quality(50)//cloudfront-us-east-1.images.arcpublishing.com/semana/HYYH6PNMI5H3XGOAB6NP2MNX4A.jpg" style="height: 15rem" />
                    </template>
                    <template #title>
                        Tasa de Cambio
                    </template>
                    <template #subtitle>
                        Nombre de usuario: <b>{{account?.username}}</b>
                    </template>
                    <template #content>
                        <div class="row">
                            <div class="col-4">
                                <p>1 USD: </p>
                                <p>Comisión Compra o Venta: </p>
                                <p>Fecha: </p>
                            </div>
                            <div class="col-8">
                                <p><b>{{exchactual?.usdRate}} COP</b></p>
                                <p><b>{{exchactual?.commissionPercentage}} %</b></p>
                                <p><b>{{prettyDate(exchactual?.dateCreation)}}</b></p>
                            </div>
                        </div>
                        <Button label="Nueva Tasa de Cambio" @click="displaynewexchange = true"/>
                    </template>
                </Card>
            </div>
        </div>
        <div class="mt-3">
            <div style="text-align: center;">
                <h2>Tasas de Cambio</h2>
            </div>
            <DataTable :value="exchanges" ref="dt" v-if="exchanges.length > 0"
                :paginator="true" :rows="5">
                    <Column field="usdRate" header="Tasa de Cambio USD"></Column>
                    <Column field="commissionPercentage" header="% Comisión"></Column>
                    <Column field="dateCreation" header="Fecha" :sortable="true">
                    <template #body="row">
                        {{prettyDate(row.data.dateCreation)}}
                    </template>
                </Column>
            </DataTable>
        </div>
    </div>
    <Dialog header="Nueva Tasa de Cambio" v-model:visible="displaynewexchange"
    :style="{width: '50vw'}">
        <NewTasa v-on:newTasa="onNewTasa"/>
    </Dialog>
    <!-- FINAL DE ADMIN -->

</template>

<script>
import gql from "graphql-tag";
import Navbar from './partials/Navbar.vue';
import NewTransaction from './NewTransaction.vue';
import NewTasa from './NewTasa.vue';
import NewAccountF from './NewAccountF.vue';
import Card from 'primevue/card';
import moment from 'moment';
import Button from 'primevue/button';
import Dialog from 'primevue/dialog';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';
moment.locale('es');

export default {
    name: 'Home',
    components: {
        Navbar,
        NewTransaction,
        NewTasa,
        NewAccountF,
        Card,
        Button,
        Dialog,
        DataTable,
        Column
    },
    data() {
        return {
            accountf: null,
            account: null,
            transactions: [],
            display: false,
            displaynewexchange: false,
            displayNewAccountF: false,

            exchactual: null,
            exchanges: [],
        }
    },
    methods: {
        logOut() {
            this.$emit('logout');
        },
        prettyDate(date) {
            return date? moment(date).format('LLLL') : '--';
        },
        onNewTransaction(transaction) {
            if (transaction.type == 'compra') {
                //console.log(this.account.balance);
                //console.log(transaction.originAmount);
                //console.log(this.accountf.usdAmount);
                //console.log(transaction.destinationAmount);                
                this.account = {
                balance: this.account.balance - transaction.originAmount,
                lastChange: transaction.date
            };
                this.accountf = { 
                usdAmount: this.accountf.usdAmount + transaction.destinationAmount,
                lastChange: transaction.date
            };
            }

            if (transaction.type == 'venta') {
                //console.log(this.account.balance);
                //console.log(transaction.cop2user);
                //console.log(this.accountf.usdAmount);
                //console.log(transaction.OriginalAmount);                
                this.account = {
                balance: this.account.balance + transaction.cop2user,
                lastChange: transaction.date
            };
                this.accountf = { 
                usdAmount: this.accountf.usdAmount - transaction.originAmount,
                lastChange: transaction.date
            };
            }    

            this.account.username = transaction.username;
            this.transactions = [...this.transactions, transaction];
            this.display = false;
        },
        onNewTasa(Tasa) {
                console.log(Tasa);
                this.exchactual = {
                usdRate: Tasa.usdRate,
                commissionPercentage: Tasa.commissionPercentage,
                dateCreation: Tasa.dateCreation
            };

            this.exchanges = [...this.exchanges, Tasa];
            this.displaynewexchange = false;
        },
        onNewAccountF(AccountF) {
                console.log(AccountF);
                this.accountf = {
                usdAmount: AccountF.usdAmount,
                lastChange: AccountF.lastChange
            };

            this.displayNewAccountF = false;
        },        
        CuentayTransaccionForexporUsuario() {
        this.$apollo.query({
            query: gql`
                query ($username: String!) {
                    accountfByUsername(username: $username) {
                        username
                        usdAmount
                        lastChange
                    }
                    transactionsfByUsername(username: $username) {
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
                username: localStorage.getItem('username') || ''
            }
        }).then(response => {
            console.log(response);
            this.accountf = response.data.accountfByUsername;
            this.transactions = response.data.transactionsfByUsername;
        }).catch(e => {
            console.log(JSON.stringify(e, null, 2));
        });
    }
    },
    created() {
        this.$apollo.query({
            query: gql`
                query ($username: String!) {
                    accountByUsername(username: $username) {
                        username
                        balance
                        lastChange
                    }
                    exchangeactual {
                        commissionPercentage
                        usdRate
                        dateCreation
                    }
                    exchanges {
                        dateCreation
                        commissionPercentage
                        usdRate
                    }          
                }
            `,
            variables: {
                username: localStorage.getItem('username') || ''
            }
        }).then(response => {
            console.log(response);
            this.account = response.data.accountByUsername;
            this.exchactual = response.data.exchangeactual;
            this.exchanges = response.data.exchanges;
            this.CuentayTransaccionForexporUsuario();
        }).catch(e => {
            console.log(JSON.stringify(e, null, 2));
        });
    }
}
</script>