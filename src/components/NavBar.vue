<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-primary shadow-sm mb-4 position-relative">
    <div class="container-fluid">
      <!-- Logo + Nombre -->
      <router-link to="/" class="navbar-brand d-flex align-items-center">
        <i class="bi bi-diagram-3 me-2 fs-4"></i>
        <span>Mis Activos Digitales</span>
        <!-- Badge Beta - pequeño y bonito -->
        <span
          class="badge bg-light text-primary ms-2 px-2 py-1 rounded-pill"
          style="font-size: 0.75rem"
          >Beta</span
        >
      </router-link>

      <!-- Botón hamburguesa -->
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

      <!-- Menú -->
      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav ms-auto mb-2 mb-lg-0">
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

  <!-- Notificación de Beta -->
  <div class="container">
    <div class="alert alert-info alert-dismissible fade show small text-center" role="alert">
      <strong>🧪 Versión Beta</strong> – Es usable pero seguimos trabajando en nuevas
      funcionalidades.
      <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Cerrar"></button>
    </div>
  </div>
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

.beta-badge-container {
  position: absolute;
  right: 0;
  top: 0;
  transform: translateX(100%);
  height: 100%;
  display: flex;
  align-items: center;
  z-index: 1000;
}

.beta-badge {
  background-color: #198754 !important; /* color personalizado */
  font-size: 0.85rem;
  white-space: nowrap;
  border-top-left-radius: 10px;
  border-bottom-left-radius: 10px;
  box-shadow: -2px 0 4px rgba(0, 0, 0, 0.1);
}

.nav-link {
  color: white !important;
  transition: all 0.3s ease-in-out;
}

.nav-link:hover,
.nav-link.active {
  color: #f8f9fa !important;
  text-shadow: 0 0 2px rgba(0, 0, 0, 0.2);
}

.navbar-toggler-icon {
  filter: invert(1);
}
</style>
