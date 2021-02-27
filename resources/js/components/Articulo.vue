<template>
  <div>
    <h1 class="text-center">Agenda telefónica</h1>
    <hr>

  <!-- Button to Open the Modal -->
<div class="text-center">
  <input type="text" v-model="search" placeholder="Buscar en la agenda" class="form-control-lg">
</div>
<div class="text-center"> 
  <button @click="modificar=false; abrirModal();" type="button" class="btn btn-success my-2 botonAgregar">
  Agregar
  </button>
  <button @click="listar();" type="button" class="btn btn-dark my-2 botonAgregar">
  Recargar agenda
  </button>
</div>


<!-- The Modal -->
<div class="modal" :class="{mostrar:modal}"> <!-- Aplicar estilos al modal -->
  <div class="modal-dialog">
    <div class="modal-content">

      <!-- Modal Header -->
      <div class="modal-header">
        <h4 class="modal-title">{{tituloModal}}</h4>
        <button @click="cerrarModal();" type="button" class="close" data-dismiss="modal">&times;</button>
      </div>

      <!-- Modal body -->
      <div class="modal-body">
          <div>
            <label for="nombre">Nombre</label>
            <input v-model="agendado.nombre" type="text" class="form-control" id="nombre" placeholder="Nombre del contacto">
          </div>
          <div class="my-5">
            <label for="descripcion">Descripción</label>
            <input v-model="agendado.descripcion" type="text" class="form-control" id="descripcion" placeholder="Descripción del contacto">
          </div>
          <div class="my-5">
            <label for="numero">Número</label>
            <input v-model="agendado.numero" type="number" class="form-control" id="numero" placeholder="Número de teléfono" max="9999999999">
          </div>

      </div>

      <!-- Modal footer -->
      <div class="modal-footer">
        <button @click="cerrarModal();" type="button" class="btn btn-secondary" data-dismiss="modal">Cancelar</button>
        <button @click="guardar();" type="button" class="btn btn-success" data-dismiss="modal">Guardar</button>
      </div>

    </div>
  </div>
</div>


    <table class="table table-bordered table-hover">
  <thead class="thead-dark">
    <tr>
      <th scope="col">Id</th>
      <th scope="col">Nombre</th>
      <th scope="col">Descripción</th>
      <th scope="col">Número de teléfono</th>
      <th scope="col" colspan="2" class="text-center">Acción</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="art in articulos" :key="art.id">
      <th scope="row">{{art.id}}</th>
      <td>{{art.nombre}}</td>
      <td>{{art.descripcion}}</td>
      <td>{{art.numero}}</td>
      <td>
        <button @click="modificar=true; abrirModal(art);" type="button" class="btn btn-secondary">Editar</button>
      </td>
           <td>
        <button @click="eliminar(art.id)" class="btn btn-danger">Eliminar</button>
      </td>
    </tr>
  </tbody>
</table>


  </div>
</template>
<script>
export default {
  data(){ //LLENADO DINÁMICO DE LA TABLA
    return{
      agendado:{
        nombre:'',
        descripcion:'',
        numero:0,
      },
      search:'',
      search_result:'',
      id:0,
      modificar:true,
      modal:0,
      tituloModal:'',
      articulos:[],
    }
  },
  methods:{
      async listar(){
        const res=await axios.get('/articulos');
        this.articulos = res.data;
      },
      
      async eliminar(id){
        const res=await axios.delete('/articulos/'+id);
        this.listar(); //UNA VEZ QUE SE ELIMINA EL ELEMENTO SE ACTUALIZA LA LISTA
      },
      
      async guardar(){
        if(this.modificar){
          const res=await axios.put('/articulos/'+this.id, this.agendado);
        }else{ 
          const res=await axios.post('/articulos', this.agendado);
        }
        this.cerrarModal();
        this.listar();

      },
      
      abrirModal(data = {}){
        this.modal=1;
        if(this.modificar){
          this.id=data.id;
          this.tituloModal="Modificar elemento agendado";
          this.agendado.nombre = data.nombre;
          this.agendado.descripcion = data.descripcion;
          this.agendado.numero = data.numero;
        }else{
          this.id=0,
          this.tituloModal="Crear elemento";
          this.agendado.nombre = '';
          this.agendado.descripcion = '';
          this.agendado.numero = '';
        }
      },
      cerrarModal(){
        this.modal=0;
      },
      searchdata:function(val){
        axios.get('/search/'+val).then((res)=>{
          this.articulos = res.data;
        })
      },
    },
    watch:{
      search:function(){
          this.searchdata(this.search) //Búsqueda de datos en tiempo real
      }
    },
    created(){ //INICIAR CICLO DE VIDA DEL COMPONENTE
      this.listar(); 
    },
}
</script>
<style>
.mostrar{
  display: list-item;
  opacity: 1;
  background: rgba(0, 0, 0, 0.296);
}
.botonAgregar{
  width: 200px;
}


</style>