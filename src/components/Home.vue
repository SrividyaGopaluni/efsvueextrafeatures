


<template>
  <main>
    <!-- <br/>
    <v-container fluid ma-0 pa-0 fill-height>
      <v-layout column align-center>
        <blockquote>
          &#8220;Welcome {{validUserName}}!&#8221;
          <footer>
            <small>
              <em>&mdash;Eagle Financial Services, your Midwest Financial Services Partner.</em>
            </small>
          </footer>
        </blockquote>


        

      </v-layout>
    </v-container>
    <br/> <br/> <br/> -->
    <!-- <div id="app">
      <v-app id="inspire">
        <v-carousel>
          <v-carousel-item
            v-for="(item,i) in items"
            :key="i"
            :src="item.src"
            reverse-transition="fade-transition"
            transition="fade-transition"
          ></v-carousel-item>
        <div class="display-3">Slide</div>
        </v-carousel>
      </v-app>
    </div> -->
    <div id="header-carousel">
      <!--<v-carousel style="height: 100%">
        <v-carousel-item v-for="(slide, i) in slides" :src="slide.src" :key="i">
          <v-jumbotron dark>
            <v-container fill-height>
              <v-layout align-center>
                <v-flex>
                  <h3 class="display-3">{{ slide.title }}</h3>
                  <span class="subheading">{{ slide.text }}</span>
                </v-flex>
              </v-layout>
            </v-container>
          </v-jumbotron>
        </v-carousel-item>
      </v-carousel>-->



      <v-carousel
        cycle
        height="250"
        hide-delimiter-background
        show-arrows-on-hover
      >
        <v-carousel-item
          v-for="(slide, i) in slides"
          :key="i"
        >
          <v-sheet
            :color="colors[i]"
            height="100%"
          >
            <v-row
              class="fill-height"
              align="center"
              justify="center"
            >
              <div class="display-3">{{ slide }} </div>
            </v-row>
          </v-sheet>
        </v-carousel-item>
      </v-carousel>
    </div>
    <v-container fluid fill-height>
      <v-layout justify-left row>
        <v-flex xs12 sm6>
          <v-card>
            <v-container
              fluid
              grid-list-md
            >
              <v-layout >
                <v-flex xs8>
                  <v-card >
                    <v-img
                      :src="require('@/assets/images/customer.jpg')" height="300px" width="500px">
                    </v-img>

                    <v-card-actions>
                      <v-btn flat color="black" @click="viewCustomers">View Details</v-btn>
                    </v-card-actions>
                  </v-card>
                </v-flex>

                <v-flex xs8>
                  <v-card>
                    <v-img
                      :src="require('@/assets/images/nonstock.jpg')" height="300px" width="500px">
                    </v-img>

                    <v-card-actions>
                      <v-btn flat color="black" @click="viewInvestments">View Details</v-btn>
                    </v-card-actions>
                  </v-card>
                </v-flex>
                <v-flex xs8>
                  <v-card>
                    <v-img
                      :src="require('@/assets/images/stocks.jpg')" height="300px" width="500px">
                    </v-img>

                    <v-card-actions>
                      <v-btn flat color="black" @click="viewStocks">View Details</v-btn>
                    </v-card-actions>
                  </v-card>
                </v-flex>
              </v-layout>
            </v-container>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>
  </main>

</template>

<script>
  import router from '../router';

  export default {
    name: 'Home',
    data: () => ({
      validUserName: "Guest",

      colors: [
        'bdbdbd grey',
        'bdbdbd grey lighten-1',
        'bdbdbd grey ',
        'bdbdbd grey lighten-1',
        
      ],
      slides: [
        'Eagle Financial Services',
        'Customers',
        'Stocks',
        'Investments',
        
      ],

      
       
    }),
    mounted() {
      this.getUser();
    },
    methods: {
      viewCustomers() {
        router.push('/customer-list');
      },

      viewInvestments() {
        router.push('/investment-list');
      },










      viewStocks() {
        router.push('/stock-list');
      },






      
      getUser() {
        if (localStorage.getItem("isAuthenticates")
          && JSON.parse(localStorage.getItem("isAuthenticates")) === true) {
          this.validUserName = JSON.parse(localStorage.getItem("log_user"));
        }
      },
      



    }
  }



  
</script>	
