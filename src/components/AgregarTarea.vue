<template>
  <h1>Agregar tarea</h1>
  <div class="container container-border">
     <form @submit.prevent="submitForm">
       <div class="d-flex flex-column">
         <div class="mb-3">
           <input type="text" placeholder="NombreProyecto" class="mt-4 form-control" id="proyecto" name="proyecto" v-model="form.proyecto">
         </div>
         <select class="form-select" name="proyecto" id="proyecto" v-model="selectedProyectoId">
           <option value="" disabled>Selecciona un proyecto</option>
           <option v-for="proyecto in proyectos" :key="proyecto.id" :value="proyecto.id" class="d-flex gap-2">
            <p class="me-2">{{ proyecto.id }}. {{ " " }} {{ proyecto.nombre }}</p> 
          </option>
         </select>
       </div>
       <div class="mb-3">
         <input type="text" placeholder="Nombre" class="mt-4 form-control" id="nombre" v-model="form.nombre">
       </div>
       <div class="d-flex flex-column mb-3">
         <div class="mb-3 w-100">
           <textarea placeholder="Agregar Mensaje" class="form-control" id="mensaje" rows="2" v-model="form.mensaje"></textarea>
         </div>
         <select class="form-select" id="estado" v-model="form.estado">
           <option value="Pendiente">Pendiente</option>
           <option value="En progreso">En progreso</option>
           <option value="Completada">Completada</option>
         </select>
       </div>
       <div v-if="errorMessage" class="alert alert-danger" role="alert">
         {{ errorMessage }}
       </div>
       <button type="submit" class="btn btn-primary mb-3">Enviar</button>
     </form>
  </div>
  <section class="mt-4">
    <ListarTareas :key="componentKey" :tareas="tareas" @editar="editarTarea" @actualizarTareas="actualizarTareas" />
  </section>
 </template>

 <script>
import ListarTareas from './ListarTareas'

 export default {
  name: 'AgregarTarea',
  components: {
    ListarTareas
  },
  props: {
     tareaParaEditar: Object
  },
  data() {
    return {
        form: {
            proyecto: '',
            nombre: '',
            mensaje: '',
            estado: 'Pendiente',
            selectedProyectoId: '',
            proyectoId: this.$route.params.proyectoId
        },
        tareas: [],
        errorMessage: '',
        proyectos: [],
        tareaEnEdicion: null, // Nueva variable para almacenar la tarea en edición
        componentKey: 0,
    };
  },
  actualizarTareas() {
        // Aquí puedes cargar las tareas actualizadas desde localStorage
        // y asignarlas a localTareas
        const savedTareas = localStorage.getItem('listaTareas');
        if (savedTareas) {
            this.localTareas = JSON.parse(savedTareas);
        }
    },
  created() {

    const savedTareas = localStorage.getItem('listaTareas');
    if (savedTareas) {
        this.tareas = JSON.parse(savedTareas);
    }

    const savedProyectos = localStorage.getItem('listaProyectos'); // Carga listaProyectos
    if (savedProyectos) {
        this.proyectos = JSON.parse(savedProyectos); // Asigna la lista de proyectos a una propiedad de datos
    }

    const proyectoFiltrado = this.proyectos.find(proyecto => proyecto.id === Number(this.$route.params.proyectoId));

    // Verifica si se encontró un proyecto
    if (proyectoFiltrado) {
        console.log(proyectoFiltrado.nombre);
        this.form.proyecto = proyectoFiltrado.nombre;
    } else {
        console.log("No se encontró un proyecto con el ID proporcionado.");
    }
  },
  computed: {
     proyectosFiltrados() {
       // Filtra los proyectos basándote en el proyectoId recibido
       return this.proyectos.filter(proyecto => proyecto.id === this.$route.params.proyectoId);
     }
  },
  watch: {
     selectedProyectoId(newId) {
       this.form.proyectoId = newId;
     },
     tareaParaEditar(newTarea) {
       if (newTarea) {
         this.form = { ...newTarea };
       }
     },
     tareas(newTareas) {
        // Actualiza localTareas con las nuevas tareas
        this.localTareas = [...newTareas];
     }
  },
  methods: {
    submitForm() {
        // Verifica si los campos nombre y mensaje están vacíos
        if (!this.form.nombre || !this.form.mensaje) {
            this.errorMessage = 'Por favor, completa todos los campos.';
            return; // Detiene la ejecución del método
        }

        if (!this.form.proyecto) {
            this.errorMessage = 'Por favor, selecciona un proyecto.';
            return; // Detiene la ejecución del método
        }

        // Si se está editando una tarea existente, actualiza la tarea
        if (this.tareaEnEdicion) {
            const index = this.tareas.findIndex(tarea => tarea.id === this.tareaEnEdicion.id);
            if (index !== -1) {
                this.tareas[index] = { ...this.form };
            }
            // Resetea el estado de edición
            this.tareaEnEdicion = null;
        } else {
            // Si se está creando una nueva tarea, añade la nueva tarea a la lista
            const id = this.tareas.length > 0 ? this.tareas[this.tareas.length - 1].id + 1 : 0;
            const nuevaTarea = {
                id: id,
                proyecto: this.form.proyecto,
                nombre: this.form.nombre,
                mensaje: this.form.mensaje,
                estado: this.form.estado,
                proyectoId: this.form.proyectoId
            };
            this.tareas.push(nuevaTarea);
        }

        localStorage.setItem('listaTareas', JSON.stringify(this.tareas));

        this.form.nombre = '';
        this.form.mensaje = '';
        this.form.estado = 'Pendiente';
        this.errorMessage = ''; // Limpia el mensaje de error

        console.log("Tarea emitida");
        this.$emit('tareaActualizada');
        this.$emit('actualizarTareas'); // Emite el evento tareaActualizada

        this.componentKey++;
    },
    eliminarTarea(id) {
      const index = this.localTareas.findIndex(tarea => tarea.id === id);
      if (index !== -1) {
          this.localTareas.splice(index, 1);
          localStorage.setItem('listaTareas', JSON.stringify(this.localTareas));
      }
    },
     editarTarea(tarea) {
        this.tareaEnEdicion = tarea;
        this.form = { ...tarea };
    },
    }
 }
 </script>
 
 <style>
 .container-border {
  border: solid rgba(0, 0, 0, 0.25) 1px;
  border-radius: 14px;
 }
 </style>
 