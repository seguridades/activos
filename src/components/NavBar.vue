<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
    <div class="container-fluid">
      <router-link to="/" class="navbar-brand">Mis Activos Digitales</router-link>

      <button
        class="navbar-toggler"
        type="button"
        data-bs-toggle="collapse"
        :data-bs-target="'#navbarSupportedContent'"
        aria-controls="navbarSupportedContent"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon"></span>
      </button>

      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav me-auto mb-2 mb-lg-0">
          <li class="nav-item">
            <router-link to="/" class="nav-link">Inicio</router-link>
          </li>
          <li class="nav-item">
            <router-link to="/setup" class="nav-link">Configuración</router-link>
          </li>
          <li class="nav-item">
            <router-link to="/list" class="nav-link">Listado de Activos</router-link>
          </li>
          <li class="nav-item">
            <router-link to="/add-asset" class="nav-link">Agregar Activo</router-link>
          </li>
        </ul>
      </div>
    </div>
  </nav>
</template>

<script>
export default {
  name: 'NavBar',
}
</script>

<style scoped>
.navbar-brand {
  font-weight: bold;
}

.nav-link {
  color: white !important;
}

.nav-link:hover {
  color: #f8f9fa !important;
}
</style>
