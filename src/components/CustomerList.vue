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
        New customer has been added.
      </v-alert>
      <v-alert v-if="showMsg === 'update'" dismissible
        :value="true"
        type="success"
      >
        Customer information has been updated.
      </v-alert>
         <v-alert v-if="showMsg === 'deleted'" dismissible
        :value="true"
        type="success"
      >
        Selected Customer has been deleted.
      </v-alert>

       </v-flex>
       </v-layout>
      <br/>
      <v-container fluid grid-list-md fill-height >
      <v-layout column>
        <div style="width: 100%; display: table;">
                <div style="display: table-row">
                    <div style="width: 600px; display: table-cell;"> <b>Customer Number </b></div>
                    <div style="width: 600px; display: table-cell;"> <b>Name</b> </div>
                    <div style="width: 600px; display: table-cell;"> <b> Address </b></div>
                    <div style="width: 600px; display: table-cell;"> <b>City</b> </div>
                    <div style="width: 600px; display: table-cell;"> <b>State</b> </div>
                    <div style="width: 600px; display: table-cell;"> <b>Zipcode</b> </div>
                    <div style="width: 600px; display: table-cell;"> <b>Email </b></div>
                    <div style="width: 600px; display: table-cell;"> <b>Phone </b></div>
                    
                </div>
              </div>
        <v-flex md6 v-for="i in customers" :key="i.text">
          <v-card flat class ="text-xs-center ma-3">
            <v-card-text>


             
              <div style="width: 100%; display: table;">
                <div style="display: table-row">
                    <div style="width: 600px; display: table-cell;"> {{i.cust_number }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.name }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.address }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.city }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.state }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.zipcode }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.email }} </div>
                    <div style="width: 600px; display: table-cell;">  {{i.cell_phone }} </div>
                    <div style="width: 600px; display: table-cell;"><v-icon @click="updateCustomer(i)">edit</v-icon></div>
                    <div style="width: 600px; display: table-cell;"><v-icon @click="deleteCustomer(i)">delete</v-icon></div>


                </div>
              </div>

              

            </v-card-text>
            
            

          </v-card>  
              

        </v-flex>
      </v-layout>
      </v-container>

      <v-btn class="blue white--text" @click="addNewCustomer">Add Customer</v-btn>
    </v-container>

  </main>
</template>


<script>

  import router from '../router';
  import {APIService} from '../http/APIService';
  const apiService = new APIService();

  export default {
    name: "CustomerList",
    data: () => ({
      customers: [],
      validUserName: "Guest",
      customerSize: 0,
      showMsg: '',
      headers: [
        {
          text: 'Customer Number',
          align: 'left',
          sortable: false,

        },
        {text: 'Name', sortable: false, align: 'left',},
        {text: 'Address', sortable: false, align: 'left',},
        {text: 'City', sortable: false, align: 'left',},
        {text: 'State', sortable: false, align: 'left',},
        {text: 'ZipCode', sortable: false, align: 'left',},
        {text: 'Email', sortable: false, align: 'left',},
        {text: 'Phone', sortable: false, align: 'left',},
        {text: 'Update', sortable: false, align: 'left',},
        {text: 'Delete', sortable: false, align: 'left',}
      ],

    }),
    mounted() {
      this.getCustomers();
      this.showMessages();
    },
    methods: {
      showMessages(){
        console.log(this.$route.params.msg)
         if (this.$route.params.msg) {
           this.showMsg = this.$route.params.msg;
         }
      },
      getCustomers() {
        apiService.getCustomerList().then(response => {
          this.customers = response.data.data;
          this.customerSize = this.customers.length;
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
      addNewCustomer() {
        if (localStorage.getItem("isAuthenticates")
          && JSON.parse(localStorage.getItem("isAuthenticates")) === true) {
          router.push('/customer-create');
        } else {
          router.push("/auth");
        }
      },
      updateCustomer(customer) {
        router.push('/customer-create/' + customer.pk);
      },
      deleteCustomer(customer) {
        apiService.deleteCustomer(customer.pk).then(response => {
          if (response.status === 204) {
            alert("Customer deleted");
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
