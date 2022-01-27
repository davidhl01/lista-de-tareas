<template>
<div id="ProyectoVue">
  <div id="introducir">
          <label for="Introducetarea"><h3>Introduce una tarea</h3></label>
          <input type="text" v-model="nombre" placeholder="Introduce una tarea..." v-on:keyup.enter="añadirtarea">
          &nbsp;&nbsp;
          <label for="descripcion"></label>
          <input type="text" v-model="descripcion" placeholder="Añade una descripción..." v-on:keyup.enter="añadirtarea">

          <!-- Al pulsar enter se añadirá la tarea sin necesidad de pulsar el boton añadir, pero cuando estemos 
          dentro de alguno de los campos introduce tarea o añade descripción -->

          &nbsp;&nbsp;
          <label for="prioridad">Prioridad:</label>
          &nbsp;
          <select v-model="prioridad" required>
              <option disabled value="">------</option>
              <option>1 <!--Alta--></option>  
              <option>2 <!--Media--></option>
              <option>3 <!--Baja--></option>
          </select>
          &nbsp;&nbsp;
          <button v-on:click="añadirtarea" id="tarea">Añadir tarea</button>
      </div>
      <transition-group name="transicion">
      <div id="mostrar" v-for="tarea,index in listadetareas" :key="index" :class="{completada:tarea.estadotarea}">
          <ol>
              <li>
                  <strong>Nombre de la tarea:</strong> {{tarea.nombre}}
                  &nbsp;
                  <strong>Descripción:</strong> {{tarea.descripcion}}
                  &nbsp;
                  <strong>Estado:</strong> {{tarea.estadotarea}} 
                  <br>
                  <strong>Prioridad:</strong> {{tarea.prioridad}}
                  &nbsp;
                  <strong>Fecha:</strong> {{tarea.fecha}}
                  <br><br>
                  <button type="button" v-on:click="tareaCompletada(index)" id="completado">Marcar como completada</button>
                  <button type="button" v-on:click="borrartarea(index)">Borrar</button>
                  <button type="button" v-on:click="aumentarlaPrioridad(index)" id="aumentar">Aumentar prioridad</button>
                  <button type="button" v-on:click="disminuirlaPrioridad(index)">Disminuir prioridad</button>
              </li>
          </ol>
          <br> 
      </div>
      </transition-group>
      <div id="botones">
            <button v-on:click="borrarCompletadas">Borrar completadas</button>
            &nbsp;
            <button v-on:click="mostrarCompletadas">Mostrar completadas</button>
            &nbsp;
            <button v-on:click="mostrarNoCompletadas">Mostrar no completadas</button>
            &nbsp;
            <button v-on:click="mostrarTodas">Mostrar todas</button>
            <br><br>
            <span>Tienes {{listadetareas.length}} tareas, de las cuales {{nocompletadas}} sin completar</span>
      </div>
    </div>
</template>

<script>
export default {
    name: "tareas",
    components:{

    },
    data(){
        return{
            listadetareas:[],
            nombre: "",
            descripcion: "",
            prioridad: "",
            estadotarea: false,
        }
    },
    
    methods: {
        añadirtarea(){
          if(this.nombre && this.descripcion && this.prioridad){ // cuando los 3 campos estén rellenos dejará añadir la tarea
            this.listadetareas.push({nombre: this.nombre, descripcion: this.descripcion, fecha: new Date().toLocaleString(),
            prioridad: this.prioridad, estadotarea: this.estadotarea});
            this.ordenarPrioridad();
            this.updateLocalStorage();
          }
        },

        tareaCompletada(index){
          this.listadetareas[index].estadotarea = true;
          this.updateLocalStorage();
        },

        borrartarea(index){
          this.listadetareas.splice(index, 1);
          this.updateLocalStorage();
        },

        updateLocalStorage(){
          localStorage.all = JSON.stringify(this.listadetareas);
        },

        borrarCompletadas(){
          var com=[];
          this.listadetareas.forEach(element => {
              if(element.estadotarea == false){
                  com.push(element);
              }
          });
          this.listadetareas = com;
          this.updateLocalStorage();
        },

        mostrarCompletadas() { // Muestra solo las tareas completadas
          this.listadetareas = JSON.parse(localStorage.all);
          let com = [];
          this.listadetareas.forEach(tarea => {
              if (tarea.estadotarea) {
                  com.push(tarea);
              }
              this.listadetareas = com;
          });
        },

        mostrarNoCompletadas() { // Muestra solo las tareas no completadas
          this.listadetareas = JSON.parse(localStorage.all);
          let nocom = [];
          this.listadetareas.forEach(tarea => {
              if (!tarea.estadotarea) {
                  nocom.push(tarea);
              }
              this.listadetareas = nocom;
          });
        },

        mostrarTodas() { // Muestra todas las tareas
          this.listadetareas = JSON.parse(localStorage.all);
        },

        aumentarlaPrioridad(index){ // Máxima prioridad es 1
            if (this.listadetareas[index].prioridad > 1) {
              this.listadetareas[index].prioridad--;
              this.ordenarPrioridad();
          }
        },

        disminuirlaPrioridad(index){ // Mínima prioridad es 3
            if (this.listadetareas[index].prioridad < 3) {
              this.listadetareas[index].prioridad++;
              this.ordenarPrioridad();
          }
        },

        ordenarPrioridad() {
          this.listadetareas = this.listadetareas.sort((a, b) => {
              if (a.prioridad < b.prioridad) {
                  return -1;
              } else if (a.prioridad > b.prioridad) {
                  return 1;
              } else {
                  return 0;
              }
          });
          this.updateLocalStorage();
        }
      },

      // Campo calculado con return ('siempre devuelve algo').
    computed:{
      nocompletadas(){
        let numincompletas = this.listadetareas.filter(tarea => !tarea.estadotarea).length;
        return numincompletas;
      }
    },
    
    mounted(){
      if(localStorage.all)
          this.listadetareas = JSON.parse(localStorage.all);
    }
}

</script>

<style scoped>

#introducir{
	margin: auto;
	text-align: center;
	color: #fff;
	padding: 30px;
	width: 50%;
	height: 120px;
	border-radius: 20px;
	border: 2px solid #fff;
	background-color: #395064;
	margin-bottom: 10px;
	box-shadow: 10px 5px 5px #2e3a49;
}

#introducir input[type=text] {
	border: 2px solid black;
	margin-bottom: 20px;
	padding: 10px;
	width: 25%;
	border-radius: 10px;
	font-family: 'Baloo Tamma 2', cursive;
}

select {
	border-radius: 10px;
	font-family: 'Baloo Tamma 2', cursive;
}

#introducir button {
	background: #ed99a7;
	border: 2px solid black;
	color: #fff;
	display: inline-block;
	font-size: 16px;
	padding: 10px;
	border-radius: 10px;
	font-family: 'Baloo Tamma 2', cursive;
}

#introducir button:hover {
	border: 2px solid #fff;
}

#mostrar{
	font-size: 20px;
	margin: auto;
	text-align: center;
	color: #fff;
	text-shadow: -1px -1px 1px #395064, 1px 1px 1px #395064, -1px 1px 1px #395064, 1px -1px 1px #395064;
	width: 50%;
  height: 150px;
	padding: 30px;
	border-radius: 20px;
	background-color: #ed99a7;
	border: 2px solid #fff;
	box-shadow: 10px 5px 5px #2e3a49;
  margin-bottom: 10px;
}

#botones{
  font-size: 20px;
	margin: auto;
	text-align: center;
	color: #fff;
	text-shadow: -1px -1px 1px #395064, 1px 1px 1px #395064, -1px 1px 1px #395064, 1px -1px 1px #395064;
	width: 50%;
  height: 110px;
	padding: 30px;
	border-radius: 20px;
	background-color: #ed99a7;
	border: 2px solid #fff;
	box-shadow: 10px 5px 5px #2e3a49;
}

#botones button{
  font-family: 'Baloo Tamma 2', cursive;
	background: #486077;
	border: 2px solid black;
	color: #fff;
	display: inline-block;
	font-size: 16px;
	padding: 10px;
	border-radius: 10px;
}

#botones button:hover{
  border: 2px solid #fff;
}

#mostrar button{
	font-family: 'Baloo Tamma 2', cursive;
	background: #486077;
	border: 2px solid black;
	color: #fff;
	display: inline-block;
	font-size: 16px;
	padding: 10px;
	border-radius: 10px;
}

#mostrar button:hover {
	border: 2px solid #fff;
}

ol{
	list-style: none;
	margin: auto;
}

ol li{
	margin-bottom: 10px;
}

strong{
	text-shadow: -1px -1px 1px #395064, 1px 1px 1px #395064, -1px 1px 1px #395064, 1px -1px 1px #395064;
}

.completada {
    text-decoration: line-through;
}

#completado{
	margin-right: 20px;
}

#aumentar{
	margin-right: 20px;
	margin-left: 20px;
}

/*Transicion*/

.transicion-leave-active {
    transition: all 1s cubic-bezier(1, 0.5, 0.8, 1);
  }
  
.transicion-enter-from,
.transicion-leave-to {
    transform: translateX(10px);
    opacity: 5%;
}

</style>