<template>
    <div :key="componentKey" class="container mx-auto">
       <input type="text" v-model="filtroNombre" placeholder="Filtrar por nombre" class="form-control mb-3 mx-1 ">
       <select v-model="filtroEstado" class="form-select mb-3 mx-1">
           <option value="">Todos los estados</option>
           <option value="Pendiente">Pendiente</option>
           <option value="En progreso">En progreso</option>
           <option value="Completada">Completada</option>
       </select>
       <ul class="list-group flex-column overflow-y-auto h-300 overflow-hidden">
        <li class="list-group-item" v-for="tarea in tareasFiltradas" :key="tarea.id">
           <div>
             <strong>Nombre:</strong> {{ tarea.nombre }}
           </div>
           <div class="w-100 d-flex justify-content-between">
                <div class="d-flex flex-column text-start">
                    <div class="w-100 max-h">
                        <strong>Mensaje:</strong> {{ tarea.mensaje }}
                    </div>
                    <div class="">
                        <strong>Estado:</strong> 
                        <span v-if="tarea.estado === 'Pendiente'">Pendiente</span>
                        <span v-else-if="tarea.estado === 'En progreso'">En progreso</span>
                        <span v-else>Completada</span>
                    </div>
                </div>
                <div class="d-flex flex-column gap-2">
                    <button class="btn btn-primary py-0" @click="editarTarea(tarea.id)">Editar</button>
                    <button class="btn btn-danger py-0" @click="eliminarTarea(tarea.id)">Eliminar</button>
                </div>
           </div>
         </li>
       </ul>
    </div>
</template>

<script>
export default {
    name: 'ListarProyectos',
    props: {
        tareas: Array
    },
    data() {
       return {
         localTareas: [],
         filtroNombre: '', // Propiedad para el filtro de nombre
         filtroEstado: '', // Propiedad para el filtro de estado
         componentKey: 0 // Clave para forzar la recarga del componente
       };
    },
    computed: {
        tareasFiltradas() {
            let tareasFiltradas = this.localTareas;
            if (this.filtroNombre) {
                tareasFiltradas = tareasFiltradas.filter(tarea => tarea.nombre.toLowerCase().includes(this.filtroNombre.toLowerCase()));
            }
            if (this.filtroEstado) {
                tareasFiltradas = tareasFiltradas.filter(tarea => tarea.estado === this.filtroEstado);
            }
            return tareasFiltradas;
        }
    },
    watch: {
        tareas(newTareas) {
        this.localTareas = newTareas;
        }
    },
    created() {
       this.cargarTareas();
       window.addEventListener('storage', this.handleStorageChange);
    },
    beforeUnmount() {
       window.removeEventListener('storage', this.handleStorageChange);
    },
    methods: {
        cargarTareas() {
            const savedTareas = localStorage.getItem('listaTareas');
            if (savedTareas) {
                this.localTareas = JSON.parse(savedTareas);
            }
        },
        editarTarea(id) {
            const tarea = this.localTareas.find(tarea => tarea.id === id);
            if (tarea) {
                this.$emit('editar', tarea);
            } else {
                console.error('Tarea no encontrada');
            }
        },
        eliminarTarea(id) {
            const index = this.localTareas.findIndex(tarea => tarea.id === id);
            if (index !== -1) {
                this.localTareas.splice(index, 1);
                this.localTareas = [...this.localTareas]; // Aseguramos la reactividad
                localStorage.setItem('listaTareas', JSON.stringify(this.localTareas));
                this.componentKey++; // Incrementa la clave para forzar la recarga del componente
            } else {
                console.error('Tarea no encontrada');
            }
        },
        handleStorageChange(event) {
            if (event.key === 'listaTareas') {
                this.cargarTareas();
                this.componentKey++; // Incrementa la clave para forzar la recarga del componente
            }
        }
    }
}
</script>

<style>
    .max-h{
        height: clamp(300px, 40%, 1200px);
    }
    .h-300 {
     height: 300px;
    }
</style>
