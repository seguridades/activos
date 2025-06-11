<template>
  <div class="home-container px-3">
    <!-- Hero Section -->
    <section class="hero-section text-center mb-5">
      <img
        src="@/assets/illustration-security.svg"
        alt="Ilustración de seguridad digital"
        class="img-fluid hero-image mb-4"
      />
      <h1 class="display-6 fw-bold">Mapeador de Activos Digitales</h1>
      <p class="lead mt-2">
        Para organizaciones, defensores de derechos humanos y periodistas que necesitan gestionar
        activos digitales de forma segura y autónoma.
      </p>
      <p>
        <small
          >Esta herramienta te ayuda a registrar hardware, cuentas online y recursos digitales de
          forma sencilla y sin conexión permanente.</small
        >
      </p>
    </section>

    <!-- Secciones principales -->
    <section class="mb-5">
      <div class="row g-4 justify-content-center">
        <!-- Introducción -->

        <div class="col-md-6 col-lg-4">
          <div class="card shadow-sm border h-100">
            <div class="card-body d-flex flex-column">
              <h3 class="card-title text-primary d-flex align-items-center">
                <i class="bi bi-shield-fill-check me-2"></i> ¿Por qué mapear tus activos?
              </h3>
              <p class="card-text mt-3 flex-grow-1">
                Tener un inventario actualizado de los activos digitales es fundamental para
                proteger información sensible y garantizar acceso en situaciones críticas.
              </p>
              <p class="card-text text-info-emphasis fw-semibold">
                <i class="bi bi-door-open me-1"></i>
                También es clave para actualizar los protocolos de ingreso y egreso de personas en
                tu equipo. Facilita la transición digital al entrar o salir alguien del proyecto.
              </p>
            </div>
          </div>
        </div>

        <!-- Tipos de activos -->
        <div class="col-md-6 col-lg-4">
          <div class="card shadow-sm border h-100">
            <div class="card-body d-flex flex-column">
              <h3 class="card-title text-secondary d-flex align-items-center">
                <i class="bi bi-diagram-3 me-2"></i> ¿Qué puedes mapear?
              </h3>
              <ul class="mt-3">
                <li><strong>Hardware:</strong> Laptops, servidores, dispositivos móviles, etc.</li>
                <li>
                  <strong>Cuentas Online:</strong> Correos, redes sociales, plataformas educativas,
                  servicios cloud, etc.
                </li>
                <li>
                  <strong>Recursos Digitales:</strong> Documentos, ubicaciones en nube,
                  repositorios, carpetas compartidas, etc.
                </li>
              </ul>
            </div>
          </div>
        </div>

        <!-- Consideraciones importantes -->
        <div class="col-md-6 col-lg-4">
          <div class="card shadow-sm border h-100">
            <div class="card-body d-flex flex-column">
              <h3 class="card-title text-danger d-flex align-items-center">
                <i class="bi bi-exclamation-triangle-fill me-2"></i> Consideraciones Importantes
              </h3>
              <ul class="mt-3">
                <li>
                  Todos los datos se guardan localmente en tu navegador → privacidad asegurada
                </li>
                <li>No uses contraseñas ni datos sensibles directamente en el formulario</li>
                <li>Puedes exportar tu listado en cualquier momento como respaldo</li>
                <li>La herramienta funciona completamente offline</li>
                <li>Este proyecto está bajo licencia Creative Commons</li>
              </ul>
            </div>
          </div>
        </div>

        <!-- Manual rápido -->
        <div class="col-md-6 col-lg-4">
          <div class="card shadow-sm border h-100">
            <div class="card-body d-flex flex-column">
              <h3 class="card-title text-success d-flex align-items-center">
                <i class="bi bi-journal-text me-2"></i> Cómo usar esta herramienta
              </h3>
              <ol class="mt-3">
                <li><strong>Configura tu organización</strong>: nombre y responsable</li>
                <li><strong>Agrega áreas</strong>: Comunicaciones, Seguridad, Legales, etc.</li>
                <li><strong>Asocia personas</strong>: Usa apodos o seudónimos</li>
                <li><strong>Agrega activos</strong>: Completa los campos según tipo</li>
                <li><strong>Exporta</strong>: En JSON, CSV o PDF cuando lo necesites</li>
              </ol>
            </div>
          </div>
        </div>

        <!-- Créditos -->
        <div class="col-md-6 col-lg-4">
          <div class="card shadow-sm border h-100">
            <div class="card-body d-flex flex-column">
              <h3 class="card-title text-dark d-flex align-items-center">
                <i class="bi bi-person-circle me-2"></i> Créditos & Licencia
              </h3>
              <p class="card-text mt-3">
                Esta herramienta fue creada por <strong>seguridades.org</strong> para la comunidad,
                con el objetivo de ayudar a colectivos, defensores y periodistas a gestionar sus
                activos digitales de forma segura y autónoma.
              </p>
              <p class="card-text">
                <strong>Licencia:</strong> Creative Commons Attribution 4.0 International License
                <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank"
                  >Ver licencia</a
                >
              </p>
              <p class="card-text mb-0">
                <strong>Descárgala aquí:</strong>
                <a href="https://github.com/seguridades/activos" target="_blank"
                  >GitHub - Mapeador de Activos</a
                >
              </p>
            </div>
          </div>
        </div>

        <!-- Cómo colaborar -->
        <div class="col-md-6 col-lg-4">
          <div class="card shadow-sm border h-100">
            <div class="card-body d-flex flex-column">
              <h3 class="card-title text-warning d-flex align-items-center">
                <i class="bi bi-tools me-2"></i> ¿Cómo colaborar?
              </h3>
              <ul class="mt-3">
                <li>
                  Reporta errores en el
                  <a href="https://github.com/seguridades/activos/issues" target="_blank"
                    >repositorio de GitHub</a
                  >
                </li>
                <li>Sugiere nuevas funcionalidades o traducciones</li>
                <li>Comparte este recurso con otros equipos de defensa digital</li>
                <li>Contribuye al desarrollo: ¡todo aporte es bienvenido!</li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Botones principales -->
    <section class="text-center mb-5">
      <div class="d-flex flex-wrap justify-content-center gap-3">
        <router-link to="/setup" class="btn btn-lg btn-outline-info px-4 py-3">
          ⚙️ Configurar Organización
        </router-link>
        <router-link to="/add-asset" class="btn btn-lg btn-outline-success px-4 py-3">
          ➕ Agregar Activo
        </router-link>
        <router-link to="/list" class="btn btn-lg btn-outline-primary px-4 py-3">
          📋 Ver Listado
        </router-link>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: 'HomeView',
}
</script>

<style scoped>
.home-container {
  width: 100%;
  padding-left: 1rem;
  padding-right: 1rem;
}

.hero-section {
  padding: 2rem 0;
}

.hero-image {
  max-width: 280px;
  height: auto;
}

.card-title i {
  font-size: 1.2rem;
}

.card-text {
  line-height: 1.6;
}

.card ul,
.card ol {
  padding-left: 1.5rem;
}

.card li {
  margin-bottom: 0.5rem;
}

.btn-outline-info:hover,
.btn-outline-success:hover,
.btn-outline-primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}

@media (min-width: 992px) {
  .hero-image {
    max-width: 350px;
  }
}
</style>
