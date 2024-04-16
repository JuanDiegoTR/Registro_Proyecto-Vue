<template>
  <h1>Gestor de Proyectos</h1>
  <AgregarTarea :tareaParaEditar="tareaParaEditar" @tareaActualizada="tareaActualizada" />
 
  <h1 class="mt-4">Lista de Tareas</h1>
  <ListarProyectos :key="componentKey" @editar="editarTarea" :tareas="tareas" />
 </template>
 
 <script>
  import AgregarTarea from './AgregarTarea';
  import ListarProyectos from './ListarProyectos.vue';
 
  export default {
     name: 'GestioProyectos',
     components: {
       AgregarTarea,
       ListarProyectos
     },
     data() {
       return {
         tareaParaEditar: null,
         tareas: [],
         componentKey: 0
       };
     },
     created() {
       this.cargarTareas();
     },
     methods: {
       cargarTareas() {
         // Lógica para cargar las tareas desde localStorage y actualizar el estado del componente
         const savedTareas = localStorage.getItem('listaTareas');
         if (savedTareas) {
           this.tareas = JSON.parse(savedTareas);
         }
       },
       editarTarea(tarea) {
         this.tareaParaEditar = tarea;
       },
       tareaActualizada() {
         this.cargarTareas();
         this.componentKey += 1; // Incrementa la clave para forzar la recarga del componente ListarProyectos
         console.log("tarea actualizada");
       }
     }
  }
 </script>

 