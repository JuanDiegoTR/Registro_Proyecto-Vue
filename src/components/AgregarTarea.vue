<template>
  <h1>Agregar tarea</h1>
  <div class="container">
     <form @submit.prevent="submitForm">
       <div class="mb-3">
         <label for="nombre" class="form-label">Nombre</label>
         <input type="text" class="form-control" id="nombre" v-model="form.nombre">
       </div>
       <div class="d-flex">
         <div class="mb-3 w-100">
           <label for="mensaje" class="form-label">Mensaje</label>
           <textarea class="form-control" id="mensaje" rows="2" v-model="form.mensaje"></textarea>
         </div>
         <div class="d-flex flex-column form-check justify-content-center align-content-center">
           <label class="form-check-label" for="estado">Estado</label>
           <select class="form-select" id="estado" v-model="form.estado">
             <option value="Pendiente">Pendiente</option>
             <option value="En progreso">En progreso</option>
             <option value="Completada">Completada</option>
           </select>
         </div>
       </div>
       <div v-if="errorMessage" class="alert alert-danger" role="alert">
         {{ errorMessage }}
       </div>
       <button type="submit" class="btn btn-primary">Enviar</button>
     </form>
  </div>
 </template>
 
 <script>
 export default {
  name: 'AgregarTarea',
  props: {
     msg: String,
     tareaParaEditar: Object
  },
  data() {
     return {
       form: {
         nombre: '',
         mensaje: '',
         estado: 'Pendiente'
       },
       tareas: [],
       errorMessage: '' // Agrega un campo para el mensaje de error
     };
  },
  created() {
     const savedTareas = localStorage.getItem('listaTareas');
     if (savedTareas) {
       this.tareas = JSON.parse(savedTareas);
     }
  },
  watch: {
     tareaParaEditar(newTarea) {
       if (newTarea) {
         this.form = { ...newTarea };
       }
     }
  },
  methods: {
     submitForm() {
       // Verifica si los campos nombre y mensaje están vacíos
       if (!this.form.nombre || !this.form.mensaje) {
         this.errorMessage = 'Por favor, completa todos los campos.';
         return; // Detiene la ejecución del método
       }
 
       // Si tareaParaEditar está presente, actualiza la tarea existente
       if (this.tareaParaEditar) {
         const index = this.tareas.findIndex(tarea => tarea.id === this.tareaParaEditar.id);
         if (index !== -1) {
           this.tareas[index] = { ...this.form };
         }
       } else {
         const id = this.tareas.length > 0 ? this.tareas[this.tareas.length - 1].id + 1 : 0;
         const nuevaTarea = {
           id: id,
           nombre: this.form.nombre,
           mensaje: this.form.mensaje,
           estado: this.form.estado
         };
         this.tareas.push(nuevaTarea);
       }
 
       localStorage.setItem('listaTareas', JSON.stringify(this.tareas));
 
       this.form.nombre = '';
       this.form.mensaje = '';
       this.form.estado = 'Pendiente';
       this.errorMessage = ''; // Limpia el mensaje de error
 
       console.log("Tarea emitida");
       this.$emit('tareaActualizada'); // Emite el evento tareaActualizada
     }
  }
 }
 </script>
 