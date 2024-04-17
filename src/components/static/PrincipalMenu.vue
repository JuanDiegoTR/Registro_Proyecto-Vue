<template>
<nav class=" position-fixed top-0 w-100 bg-body-secondary">
    <ul class="nav gap-2">
        <li class="nav-item mx-1">
        <router-link to="/agregar-proyecto" class="nav-link dropdown-item">Agregar Proyecto</router-link>
        </li>
        <li class="nav-item">
        <router-link to="/agregar-tarea" class="nav-link dropdown-item ">Asignar Tareas</router-link>
        </li>
    </ul>
</nav>
</template>

<script>
export default {
name: 'PrincipalMenu'
}
</script>

<style scoped>
.nav {
list-style-type: none;
padding: 0;
}

.nav-item {
margin: 10px 0;
}

.nav-link {
text-decoration: none;
font-size: 600;
color: #2b1b45;
cursor: pointer;
}

</style>
   