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
        New Stock has been added.
      </v-alert>
      <v-alert v-if="showMsg === 'update'" dismissible
        :value="true"
        type="success"
      >
        Stock information has been updated.
      </v-alert>
         <v-alert v-if="showMsg === 'deleted'" dismissible
        :value="true"
        type="success"
      >
        Selected Stock has been deleted.
      </v-alert>

       </v-flex>
       </v-layout>
      <br/>
      <v-container fluid grid-list-md fill-height>
      <v-layout column>
        <div style="width: 100%; display: table;">
                <div style="display: table-row">
                    <div style="width: 600px; display: table-cell;"> <b>Customer </b></div>
                    <div style="width: 600px; display: table-cell;"> <b>Symbol</b> </div>
                    <div style="width: 600px; display: table-cell;"> <b> Name </b></div>
                    <div style="width: 600px; display: table-cell;"> <b>Shares</b> </div>
                    <div style="width: 600px; display: table-cell;"> <b>Purchase Price</b> </div>
                    <div style="width: 600px; display: table-cell;"> <b>Purchase Date</b> </div>
                    
                    
                    
                </div>
              </div>
       <v-flex md6 v-for="i in stocks" :key="i.text">
          <v-card flat class ="text-xs-center ma-3">
            <v-card-text>


             
              <div style="width: 100%; display: table;">
                <div style="display: table-row">
                    
                    <div style="width: 600px; display: table-cell;"> {{i.customer }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.symbol }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.name }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.shares }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.purchase_price }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.purchase_date }} </div>
                    
                    <div style="width: 600px; display: table-cell;"><v-icon @click="updateStock(i)">edit</v-icon></div>
                    <div style="width: 600px; display: table-cell;"><v-icon @click="deleteStock(i)">delete</v-icon></div>


                </div>
              </div>

              

            </v-card-text>
            
            

          </v-card>  
              

        </v-flex>
      </v-layout>
      </v-container>

      <v-btn class="blue white--text" @click="addNewStock">Add Stock</v-btn>
    </v-container>

  </main>
</template>


<script>

  import router from '../router';
  import {APIService} from '../http/APIService';
  const apiService = new APIService();

  export default {
    name: "StockList",
    data: () => ({
      stocks: [],
      validUserName: "Guest",
      stockSize: 0,
      showMsg: '',
      headers: [
        
        {text: 'CustomerNumber', sortable: false, align: 'left',},
        {text: 'Customer', sortable: false, align: 'left',},
        {text: 'Symbol', sortable: false, align: 'left',},
        {text: 'Name', sortable: false, align: 'left',},
        {text: 'shares', sortable: false, align: 'left',},
        {text: 'purchase_price', sortable: false, align: 'left',},
        {text: 'purchase_date', sortable: false, align: 'left',},
        {text: 'Update', sortable: false, align: 'left',},
        {text: 'Delete', sortable: false, align: 'left',}

      ],

    }),
    mounted() {
      this.getStocks();
      this.showMessages();
    },
    methods: {
      showMessages(){
        console.log(this.$route.params.msg)
         if (this.$route.params.msg) {
           this.showMsg = this.$route.params.msg;
         }
      },
      getStocks() {
        apiService.getStockList().then(response => {
          this.stocks = response.data.data;
          this.stockSize = this.stocks.length;
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
      addNewStock() {
        if (localStorage.getItem("isAuthenticates")
          && JSON.parse(localStorage.getItem("isAuthenticates")) === true) {
          router.push('/stock-create');
        } else {
          router.push("/auth");
        }
      },
      updateStock(stock) {
        router.push('/stock-create/' + stock.pk);
      },
      deleteStock(stock) {
        apiService.deleteStock(stock.pk).then(response => {
          if (response.status === 204) {
            alert("Stock deleted");
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
