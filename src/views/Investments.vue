<template>
<v-container v-if="user !== null">
    <v-snackbar top right dark :color="alert.type" v-model="alert.is">
        {{ alert.text }}

        <template v-slot:action="{ attrs }">
            <v-btn :color="alert.type === 'primary' ? 'secondary' : 'white'" text v-bind="attrs" @click="closeAlert">
                Close
            </v-btn>
        </template>
    </v-snackbar>
    <v-row v-if="!showViewed" justify="center">
        <v-col cols="12"  class="py-0 d-flex justify-space-between">
            <div>
                <v-btn :outlined="!all" :color="all ? 'primary secondary--text' : 'primary'" depressed class="mr-2" @click="init('all')">All</v-btn>
                <v-btn :outlined="all" :color="!all ? 'primary secondary--text' : 'primary'" depressed @click="init('due')">Due for payment</v-btn>
            </div>
            <v-spacer></v-spacer>
            <v-btn @click="refresh" text color="primary">
              <v-icon>mdi-autorenew</v-icon>
              Refresh
            </v-btn>
        </v-col>
        <v-col cols="12">
            <v-data-table :search="search" :loading="loading.investments" loading-text="Loading... Please wait" :headers="headers" :items="investments" sort-by="calories" class="elevation-1">
                <template v-slot:top>
                    <v-toolbar flat color="white">
                        <v-toolbar-title  class="text-uppercase">{{title}}</v-toolbar-title>
                        <v-divider class="mx-4" inset vertical></v-divider>
                        <v-spacer></v-spacer>
                        <v-text-field v-model="search" prepend-icon="mdi-magnify" label="Search" single-line hide-details></v-text-field>
                        <!-- <v-spacer></v-spacer>
                        <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">New Investment</v-btn> -->
                    </v-toolbar>
                </template>
                <template v-slot:item.actions="{ item }">
                    <v-btn color="secondary primary--text" small depressed @click="view(item)">
                        view
                    </v-btn>
                </template>
            </v-data-table>
        </v-col>
    </v-row>
    <v-row v-else>
        <view-investment :investment="viewedInvestment" :toggleView="toggleView" />
    </v-row>
</v-container>
</template>

<script>
// @ is an alias to /src
import ViewInvestment from "../components/ViewInvestment";
import {
    mapActions,
    mapGetters,
    mapMutations
} from "vuex";

export default {
    name: "Home",
    components: {
        ViewInvestment,
    },
    data: () => ({
      title: "Investments",
        all: true,
        showViewed: false,
        viewedInvestment: {},
        search: "",
        headers: [{
                text: "Investor",
                align: "start",
                sortable: false,
                value: "investor",
            },
            {
                text: "Product",
                value: "product",
            },
            {
                text: "Principal Sum",
                value: "principalSum",
            },
            {
                text: "Interest Rate",
                value: "interestRate",
            },
            {
                text: "Distribution Date",
                value: "distributionDate",
            },
            {
                text: "Expiring Date",
                value: "expiringDate",
            },

            {
                text: "Actions",
                value: "actions",
                sortable: false,
            },
        ],
    }),

    computed: {
        ...mapGetters({
            alert: "Get_Alert",
            loading: "Get_Loading",
            user: "Get_User",
            allInvestments: "Get_Investments",
        }),
        investments() {
            this.allInvestments.forEach((el) => {
                let name = `${el.investorDetails.firstName} ${el.investorDetails.lastName}`;
                el.investor = name;

                el.principalSum = el.principalSum.toLocaleString("en-NG", {
                    style: "currency",
                    currency: "NGN",
                });

                el.distributionAmount = el.distributionAmount.toLocaleString("en-NG", {
                    style: "currency",
                    currency: "NGN",
                });

                el.effectiveDate = new Date(el.effectiveDate).toLocaleDateString(
                    "en-NG"
                );
                el.expiringDate = new Date(el.expiringDate).toLocaleDateString("en-NG");
            });

            return this.allInvestments;
        },
    },
    created() {
        this.$store.dispatch("initInvestments");
    },
    methods: {
        ...mapMutations({
            setAlert: "Set_Alert",
            setDialog: "Set_Dialog",
        }),
        ...mapActions({
            getInvestments : "initInvestments"
        }),

        init(arg) {
            if (arg === "all") {
                this.all = true;
                this.title = 'Investments'
                this.getInvestments()

            } else if (arg === "due") {

                this.all = false;

                let year = new Date().getFullYear() 
                let month = new Date().getMonth() + 1
                let day = new Date().getDay()

                const date = {
                  year,
                  month,
                  day
                }

                this.getInvestments(date)
                this.title = "Due Investments"
            }
        },
        view(item) {
          item.fromInvestor = false
            this.viewedInvestment = item;
            this.toggleView(true);
        },

        toggleView(on) {
            this.showViewed = on;
        },
        closeAlert() {
            this.setAlert({
                is: false,
                type: "",
                text: "",
            });
        },
        refresh(){
          if(this.all){
            this.init('all')
          }else {
            this.init('due')
          }
        }
    },
};
</script>