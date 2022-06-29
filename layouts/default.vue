<template>
  <v-app dark>
    <div>
    <v-app-bar
      class="navbar-collapse collapse flex"
      src ="stai_logo.png"
      :clipped-left="clipped" 
      fixed
      app
      >
      <template v-slot:img="{ props }">
        <v-img
          v-bind="props"
          gradient="to top right, rgba(55,236,186,.7), rgba(22,25,25,.7)"
        ></v-img>
      </template>
      <div class="navbar row flex">
          <div class="logo">
            <nuxt-link :to="'/' ">
              <img src="stai_logo.png" height="50" width="100">  
            </nuxt-link>
          </div>
          <div class="navbar button">
            <nuxt-link :to="'/login' ">
              <v-btn text>Login</v-btn>
            </nuxt-link>
          </div>
          <div class="navbar button">
            <nuxt-link :to="'/statistics' ">
              <v-btn text>Statistics</v-btn>
            </nuxt-link>
          </div>         
        <v-spacer></v-spacer>
        <div class="searchfield flex">
          <v-form>
            <v-text-field v-model="searchInput"  type="text" placeholder="Search by Height, Block Hash, Coin hash or Wallet Address">   
              </v-text-field>
          </v-form>
        </div>
        <v-btn icon @click="submit(searchInput)">
            <v-icon>mdi-magnify</v-icon>
        </v-btn>
      </div>      
    </v-app-bar>
    </div>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer
      :absolute="!fixed" 
      app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data() {
    return {
      searchInput: "",
      clipped: false,
      drawer: false,
      fixed: false,
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'STAI Explorer',
    }
  },
  methods: {
    async submit() {
      try {
        const searchResult = await fetch("http://localhost:8080/stai/search/"+ this.searchInput).then((res) => res.json())
        this.searchInput=""
        const result = Object.entries(searchResult)[0][0]
        console.log(result)
        
        if(result === 'Block') {
          const pushParameter = Object.entries(searchResult)[0][1]["header_hash_hax"]
          this.$router.push('/block/'+pushParameter);
        }
        else if(result === 'Wallet') {
          const pushParameter = Object.entries(searchResult)[0][1]["puzzle_hash"]
          this.$router.push('/wallet/'+pushParameter);
        }
        else if(result === 'Coin') {
          const pushParameter = Object.entries(searchResult)[0][1]["coin_name"]
          this.$router.push('/coin/'+pushParameter);
        }
        else if(result === 'Height') {
          const pushParameter = Object.entries(searchResult)[0][1]["header_hash_hax"]
          this.$router.push('/block/'+pushParameter);
        }
      }catch (error) {console.log(error)} ;

    }
  },
}
</script>

<style >
.form-control {
    display: block;
    width: 100%;
    height: calc(1.5em + 0.75rem + 2px);
    padding: 0.375rem 0.75rem;
    font-size: .9rem;
    font-weight: 400;
    line-height: 1.5;
    color: #95BC0E;

    background-clip: padding-box;
    border: 1px solid #95BC0E;
    border-radius: 0.25rem;
    transition: border-color .15s ease-in-out,box-shadow .15s ease-in-out;
}
.form-control-sm {
    height: calc(1.5em + 0.5rem + 2px);
    padding: 0.25rem 0.5rem;
    font-size: .7875rem;
    line-height: 1.5;
    border-radius: 0.2rem;
}
input::placeholder{
color:black;
}
.navbar {
    position: relative;
    padding: 0.5rem 1rem;
    align-items: center;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    padding: 0!important;
    margin-right: auto;
    margin-left: auto;
    box-sizing: border-box;
}
.logo {
  margin-top: 0,75rem;
  margin-right: 1rem;
}
.button {
  margin-left: 1rem;
}
.searchfield {
  margin-top: 1rem;
  margin-bottom: auto;
}
*, :after, :before {
    box-sizing: border-box;
}
.nav-link {
    font-size: 1.1rem!important;
}
.navbar-collapse {
    flex-basis: 100%;
    flex-grow: 1;
    align-items: center;
}
.navbar-expand-lg .navbar-collapse {
    display: flex!important;
    flex-basis: auto;
}

a {
  text-decoration: none;
  color: #95BC0E;
}

th {
  background-color: #96bc0e7a !important;
  font-style: normal;
  font-size:100% !important;
  }

.card {
  padding: 0.75rem;
  background-color: #384141;
  border: 1px solid #95BC0E;
  font-size: 1.35rem;
  text-align: center !important;
  margin: 0.5rem;
  margin-top: 1rem;
  margin-bottom: 1rem;
}
.card-deck {
    flex: 1 0 0%;
    margin-right: 15px;
    margin-bottom: 0;
    margin-left: 15px;
}



</style>
