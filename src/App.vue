<template>
  <b-container id="app">

    <h1 style="margin-left: auto; margin-right: auto;">Empleados</h1>
    <hr>

    <b-row>
      <b-col>
        <b-form-group horizontal :label-cols="4" breakpoint="md" description="" label="Nombre">
          <b-form-input v-model="newitem.name" type="text" placeholder="Nombre" pattern="[a-zA-Z]" :state="namest"></b-form-input>
        </b-form-group>

        <b-form-group horizontal :label-cols="4" breakpoint="md" description="" label="Email">
          <b-form-input v-model="newitem.email" type="email" placeholder="Email" pattern="/^[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,4}$/" :state="emailst"></b-form-input>
        </b-form-group>

        <b-form-group horizontal :label-cols="4" breakpoint="md" description="" label="Fecha de Nacimiento" :state="fnst">
          <b-form-input v-model="newitem.fecNac" type="date"></b-form-input>
        </b-form-group>

        <b-form-group horizontal :label-cols="4" breakpoint="md" description="" label="Domicilio">
          <b-form-input v-model="newitem.domicilio.cd" type="text" placeholder="Ciudad" :state="cdst"></b-form-input>
          <b-form-input v-model="newitem.domicilio.calle" type="text" placeholder="Calle" :state="cst"></b-form-input>
          <b-form-input v-model="newitem.domicilio.num" type="text" placeholder="Numero" :state="nst"></b-form-input>
          <b-form-input v-model="newitem.domicilio.col" type="text" placeholder="Colonia" :state="clst"></b-form-input>
          <b-form-input v-model="newitem.domicilio.cp" type="number" placeholder="Codigo Postal" :state="cpst"></b-form-input>
        </b-form-group>

        <b-button :disabled="valido" variant="secundary" @click="regEmp">Registrar</b-button>
      </b-col>
    </b-row>

    <hr>

    <b-row>
      <b-table :fields="fields" :items="items">
        <template slot="index" scope="data">
          {{data.index + 1}}
        </template>
        <template slot="name" scope="data">
          {{data.item.name}}
        </template>
        <template slot="email" scope="data">
          {{data.item.email}}
        </template>
        <template slot="fecNac" scope="data">
          {{data.item.fecNac}}
        </template>
        <template slot="cd" scope="data">
          {{data.item.domicilio.cd}}
        </template>
        <template slot="actions" scope="data">
          <!-- We use click.stop here to prevent a 'row-clicked' event from also happening -->
          <b-btn size="sm" variant="success" @click="maps(data.item.domicilio.cd, data.item.domicilio.calle, data.item.domicilio.num, data.item.domicilio.col, data.item.domicilio.cp)">Detalles</b-btn>
        </template>

      </b-table>
    </b-row>

    <hr>

    <b-row v-show="detalles.flag">
      <div id="map_canvas" style="height:400px; width:550px;margin-right: auto; margin-left: auto"></div>
      <b-list-group>
        <b-list-group-item >
          Ciudad: {{ detalles.cd }}
        </b-list-group-item>
        <b-list-group-item>
          Calle: {{ detalles.calle }}
        </b-list-group-item>
        <b-list-group-item>
          Numero: {{ detalles.num }}
        </b-list-group-item>
        <b-list-group-item>
          Colonia: {{ detalles.col }}
        </b-list-group-item>
        <b-list-group-item>
          CP: {{ detalles.cp }}
        </b-list-group-item>
      </b-list-group>
    </b-row>

  </b-container>
</template>

<script>
import List from "./List.vue";

export default {
  components: {List},
  name: 'app',
  data () {
      return {
          //fields: [ 'index', 'Nombre', 'Email', 'Fecha_Nacimiento', 'Ciudad', 'Informacion' ],
          fields: [
              'index',
              { key: 'name', label: 'Nombre' },
              { key: 'email', label: 'Email' },
              { key: 'fecNac', label: 'Fecha de Nacimiento' },
//              { key: 'cd', label: 'Ciudad' },
              { key: 'actions', label: 'Domicilio' }
          ],
          items: [
              { name: 'nombre', email:'prueba@mail', fecNac:'12/02/1995', domicilio:{cd:'neza', calle:'5', num:'180', col:'las aguilas', cp:57900} },
          ],
          newitem:{
              name:'',
              email:'',
              fecNac:'',
              domicilio:{
                  cd:'',
                  calle:'',
                  num:'',
                  col:'',
                  cp:''
              }
          },
          detalles:{
              flag:false,
              cd:'',
              calle:'',
              num:'',
              col:'',
              cp:''
          },
          namest:null,
          emailst: null,
          fnst: null,
          cdst: null,
          cst: null,
          nst: null,
          clst: null,
          cpst: null
      }
  },
    mounted(){
        var x = this.getCookie('vuejs0p');
        x = JSON.parse(x);
        console.log(x);
        console.log(this.items);
        this.items = [];
        this.items = x;
        console.log(this.items);
    },
    methods:{
      regEmp(){
          this.items.push(this.newitem);
          this.newitem = {domicilio:{}};
          this.namest = null;
          this.emailst = null;
          this.fnst = null;
          this.cdst = null;
          this.cst = null;
          this.nst = null;
          this.clst = null;
          this.cpst = null;
          this.createCookie('vuejs0p', this.items, 1);
        },
        createCookie(name, value, expdays) {
            var date, expires, serializedValue;
            // Set Expiration date in the future
            date = new Date();
            date.setTime(date.getTime() + expdays * 86400000);
            expires = 'expires=' + date.toUTCString();
            // JSON stringify the object
            serializedValue = JSON.stringify(value);
            // Set The Cookie
            document.cookie = name + '=' + serializedValue + ';' + expires + ';path=/';
        },
        getCookie(cookieName) {
            var name = cookieName + '='
            var arr = document.cookie.split(';')
            for (var i = 0; i < arr.length; i++) {
                var str = arr[i]
                while (str.charAt(0) === ' ') {
                    str = str.substring(1)
                }
                if (str.indexOf(name) === 0) {
                    return str.substring(name.length, str.length)
                }
            }
            return ''
        },
        maps(...dom){
          this.detalles.flag = true;
          this.detalles.cd = dom[0];
          this.detalles.calle = dom[1];
          this.detalles.num = dom[2];
          this.detalles.col = dom[3];
          this.detalles.cp = dom[4];
            var address = `${dom[0]} calle ${dom[1]} numero ${dom[2]} colonia ${dom[3]} cp ${dom[4]}`;
            var geoCoder = new google.maps.Geocoder(address)
            var request = {address:address};
            geoCoder.geocode(request, function(result, status){
                var latlng = new google.maps.LatLng(result[0].geometry.location.lat(), result[0].geometry.location.lng());
                // some initial values to the map
                var myOptions = {
                    zoom: 15,
                    center: latlng,
                    mapTypeId: google.maps.MapTypeId.ROADMAP
                };
                var map = new google.maps.Map(document.getElementById("map_canvas"),myOptions);
                var marker = new google.maps.Marker({position:latlng,map:map,title:'Empleado'});
            })
        }
    },
    computed: {
        valido: function () {
            let m;
            const r1 = /^.*\S.*[a-zA-Z ]*$/;
            const r2 = /^[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,4}$/;
            const r3 = /^[0-9]{4}-(0[1-9]|1[0-2])-(0[1-9]|[1-2][0-9]|3[0-1])$/;
            const r4 = /^-?\d+$/;
            const r = /^[a-z\d\-_\s]+$/;
            if ((m = r1.exec(this.newitem.name)) !== null) {
                m.forEach((match, groupIndex) => {
                    this.namest = true;
                });
            }
            if ((m = r2.exec(this.newitem.email)) !== null) {
                m.forEach((match, groupIndex) => {
                    this.emailst = true;
                });
            }
            if ((m = r3.exec(this.newitem.fecNac)) !== null) {
                m.forEach((match, groupIndex) => {
                    this.fnst = true;
                });
            }
            if ((m = r1.exec(this.newitem.domicilio.cd)) !== null) {
                m.forEach((match, groupIndex) => {
                    this.cdst = true;
                });
            }
            if ((m = r.exec(this.newitem.domicilio.calle)) !== null) {
                m.forEach((match, groupIndex) => {
                    this.cst = true;
                });
            }
            if ((m = r4.exec(this.newitem.domicilio.num)) !== null) {
                m.forEach((match, groupIndex) => {
                    this.nst = true;
                });
            }
            if ((m = r.exec(this.newitem.domicilio.col)) !== null) {
                m.forEach((match, groupIndex) => {
                    this.clst = true;
                });
            }
            if ((m = r4.exec(this.newitem.domicilio.cp)) !== null) {
                m.forEach((match, groupIndex) => {
                    this.cpst = true;
                });
            }
            return !(this.cpst&&this.clst&&this.nst&&this.cst&&this.cdst&&this.fnst&&this.emailst&&this.namest);
        }
    }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
