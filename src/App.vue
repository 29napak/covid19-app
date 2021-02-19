<template>
  <v-app>
    <v-content>
      <Home />
    </v-content>
   <v-spacer></v-spacer>

    <v-footer>
      <div class="text-center mx-auto">
    
       <p>Napak Nantapudchim</p>
        <a class="footlink" href="https://github.com/29napak/covid19-app"><img src="./assets/git.png"> Github</a>
      </div>
    </v-footer>
  </v-app>

</template>
<script>
import Home from "./components/Home";
export default {
  name: "App",
  components: {
    Home
  },
  data: () => ({
  })
};
</script>
<style>
#app {
  background: #ffff
   none repeat scroll 0% 0% !important;
  font-family: "Roboto", sans-serif;
}
.v-application a{
  text-decoration: none;
  color:rgb(0, 0, 0) !important;
  
}
.v-footer a:hover{
  color: rgb(255, 19, 255)  !important;
 
  transition: 0.5s;

}
.v-footer p{
  margin-bottom: unset;
}
.footlink img{
  width: 25px ;
  position: relative;
  bottom: -5px;
}
.v-footer{
  margin: 50px 0 0 0;
}

</style>