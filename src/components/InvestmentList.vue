<template>
  <main>
    <br/>
    <v-container fluid grid-list-md>
      <v-layout column align-left>
        <blockquote>
         Welcome {{validUserName}}!
          <footer>
            <small>
              <em>&mdash;Eagle Financial Services, your Midwest Financial Services Partner.</em>
            </small>
          </footer>
        </blockquote>
      </v-layout>

       <v-layout column align-center>
       <v-flex xs6 sm8 md7>
       <v-alert v-if="showMsg === 'new'"
                dismissible
        :value="true"
        type="success"
      >
        New investment has been added.
      </v-alert>
      <v-alert v-if="showMsg === 'update'" dismissible
        :value="true"
        type="success"
      >
        Investment information has been updated.
      </v-alert>
         <v-alert v-if="showMsg === 'deleted'" dismissible
        :value="true"
        type="success"
      >
        Selected Investment has been deleted.
      </v-alert>

       </v-flex>
       </v-layout>
      <br/>
      <v-container fluid grid-list-md fill-height>
      <v-layout column>
        <div style="width: 100%; display: table;">
                <div style="display: table-row">
                    <div style="width: 600px; display: table-cell;"> <b>Customer </b></div>
                    <div style="width: 600px; display: table-cell;"> <b>Category</b> </div>
                    <div style="width: 600px; display: table-cell;"> <b> Description </b></div>
                    <div style="width: 600px; display: table-cell;"> <b>Acquired Value</b> </div>
                    <div style="width: 600px; display: table-cell;"> <b>Acquired Date</b> </div>
                    <div style="width: 600px; display: table-cell;"> <b>Recent Value</b> </div>
                    <div style="width: 600px; display: table-cell;"> <b>Recent Date </b></div>
                    
                    
                </div>
              </div>
        <v-flex md6 v-for="i in investments" :key="i.text">
          <v-card flat class ="text-xs-center ma-3">
            <v-card-text>


             
              <div style="width: 100%; display: table;">
                <div style="display: table-row">
                    
                    <div style="width: 600px; display: table-cell;"> {{i.customer }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.category }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.description }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.acquired_value }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.acquired_date }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.recent_value }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.recent_date }} </div>
                    
                    <div style="width: 600px; display: table-cell;"><v-icon @click="updateInvestment(i)">edit</v-icon></div>
                    <div style="width: 600px; display: table-cell;"><v-icon @click="deleteInvestment(i)">delete</v-icon></div>


                </div>
              </div>

              

            </v-card-text>
            
            

          </v-card>  
              

        </v-flex>
      </v-layout>
      </v-container>

      <v-btn class="blue white--text" @click="addNewInvestment">Add Investment</v-btn>
    </v-container>

  </main>
</template>


<script>

  import router from '../router';
  import {APIService} from '../http/APIService';
  const apiService = new APIService();

  export default {
    name: "InvestmentList",
    data: () => ({
      investments: [],
      validUserName: "Guest",
      investmentSize: 0,
      showMsg: '',
      headers: [
        
        {text: 'CustomerNumber', sortable: false, align: 'left',},
        {text: 'Category', sortable: false, align: 'left',},
        {text: 'Description', sortable: false, align: 'left',},
        {text: 'Acquired_Value', sortable: false, align: 'left',},
        {text: 'Acquired_Date', sortable: false, align: 'left',},
        {text: 'Recent_Value', sortable: false, align: 'left',},
        {text: 'Recent_Date', sortable: false, align: 'left',},
        {text: 'Update', sortable: false, align: 'left',},
        {text: 'Delete', sortable: false, align: 'left',}

      ],

    }),
    mounted() {
      this.getInvestments();
      this.showMessages();
    },
    methods: {
      showMessages(){
        console.log(this.$route.params.msg)
         if (this.$route.params.msg) {
           this.showMsg = this.$route.params.msg;
         }
      },
      getInvestments() {
        apiService.getInvestmentList().then(response => {
          this.investments = response.data.data;
          this.investmentSize = this.investments.length;
          if (localStorage.getItem("isAuthenticates")
            && JSON.parse(localStorage.getItem("isAuthenticates")) === true) {
            this.validUserName = JSON.parse(localStorage.getItem("log_user"));
          }
        }).catch(error => {
          if (error.response.status === 401) {
            localStorage.removeItem('isAuthenticates');
            localStorage.removeItem('log_user');
            localStorage.removeItem('token');
            router.push("/auth");
          }
        });
      },
      addNewInvestment() {
        if (localStorage.getItem("isAuthenticates")
          && JSON.parse(localStorage.getItem("isAuthenticates")) === true) {
          router.push('/investment-create');
        } else {
          router.push("/auth");
        }
      },
      updateInvestment(investment) {
        router.push('/investment-create/' + investment.pk);
      },
      deleteInvestment(investment) {
        apiService.deleteInvestment(investment.pk).then(response => {
          if (response.status === 204) {
            alert("Investment deleted");
            this.showMsg = 'deleted';
            this.$router.go();
          }
        }).catch(error => {
          if (error.response.status === 401) {
            localStorage.removeItem('isAuthenticates');
            localStorage.removeItem('log_user');
            localStorage.removeItem('token');
            router.push("/auth");
          }
        });
      }
    }
  };
</script>
