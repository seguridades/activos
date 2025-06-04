<template>
  <div class="home-container">
    <!-- Notificación de Beta -->
    <div class="alert alert-info alert-dismissible fade show small" role="alert">
      <strong>🧪 Versión Beta</strong> – Es usable pero seguimos trabajando en mejoras.
      <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Cerrar"></button>
    </div>

    <!-- Introducción -->
    <section class="mb-5">
      <div class="card shadow-sm border-0 mb-4">
        <div class="card-body">
          <h2 class="card-title text-primary">
            📌 ¿Por qué es importante mapear tus activos digitales?
          </h2>
          <p class="card-text">
            Para organizaciones, defensores de derechos humanos y periodistas, tener un inventario
            actualizado de los activos digitales es fundamental. Esto permite identificar puntos
            críticos, proteger información sensible y garantizar el acceso en situaciones de
            emergencia o ausencia de equipos clave.
          </p>
          <p class="card-text">
            Este mapeador te ayuda a registrar hardware, cuentas online y recursos digitales de
            forma sencilla, segura y sin necesidad de conexión permanente.
          </p>
        </div>
      </div>
    </section>

    <!-- Tipos de activos -->
    <section class="mb-5">
      <div class="card shadow-sm border-0 mb-3">
        <div class="card-body">
          <h3 class="card-title text-secondary">
            🔍 ¿Qué activos puedes mapear con esta herramienta?
          </h3>
          <ul>
            <li><strong>Hardware:</strong> Laptops, servidores, dispositivos móviles, etc.</li>
            <li>
              <strong>Cuentas Online:</strong> Correos, redes sociales, plataformas educativas,
              servicios cloud, etc.
            </li>
            <li>
              <strong>Recursos Digitales:</strong> Documentos, ubicaciones en nube, repositorios,
              carpetas compartidas, etc.
            </li>
          </ul>
        </div>
      </div>
    </section>

    <!-- Consideraciones -->
    <section class="mb-5">
      <div class="card shadow-sm border-0 mb-3">
        <div class="card-body">
          <h3 class="card-title text-danger">⚠️ Consideraciones Importantes</h3>
          <ul>
            <li>Todos los datos se guardan localmente en tu navegador → privacidad asegurada</li>
            <li>No uses contraseñas ni datos altamente sensibles directamente en el formulario</li>
            <li>Puedes exportar tu listado en cualquier momento como respaldo</li>
            <li>La herramienta no requiere internet para funcionar</li>
            <li>Este proyecto es de código abierto y está bajo licencia Creative Commons</li>
          </ul>
        </div>
      </div>
    </section>

    <!-- Manual rápido -->
    <section class="mb-5">
      <div class="card shadow-sm border-0 mb-3">
        <div class="card-body">
          <h3 class="card-title text-success">📘 Manual Rápido de Uso</h3>
          <ol>
            <li>
              <strong>Configura tu organización</strong>: Ve a "Configuración" y registra nombre y
              responsable principal
            </li>
            <li>
              <strong>Agrega áreas</strong>: Define las áreas de trabajo (ej: Comunicaciones,
              Seguridad, Legales)
            </li>
            <li>
              <strong>Asocia personas a cada área</strong>: Puedes usar nombres reales, apodos o
              seudónimos
            </li>
            <li>
              <strong>Agrega un activo</strong>: Selecciona tipo y completa los campos relevantes
            </li>
            <li><strong>Exporta</strong>: En JSON, CSV o PDF según tus necesidades</li>
          </ol>
        </div>
      </div>
    </section>

    <!-- Créditos -->
    <section class="mb-5">
      <div class="card shadow-sm border-0 mb-3">
        <div class="card-body">
          <h3 class="card-title text-dark">🎓 Créditos & Licencia</h3>
          <p>
            Esta herramienta fue creada por <strong>Seguridades.org</strong>, con el objetivo de
            ayudar a colectivos, defensores de derechos humanos y periodistas a gestionar sus
            activos digitales de forma segura y autónoma.
          </p>
          <p>
            <strong>Licencia:</strong> Creative Commons Attribution 4.0 International License
            <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank">Ver licencia</a>
          </p>
          <p class="mb-0">
            <strong>Descárgala aquí:</strong>
            <a href="https://github.com/seguridades/activos" target="_blank"
              >GitHub - Mapeador de Activos</a
            >
          </p>
        </div>
      </div>
    </section>

    <!-- Colaborar -->
    <section class="mb-5">
      <div class="card shadow-sm border-0 mb-3">
        <div class="card-body">
          <h3 class="card-title text-warning">🛠️ ¿Cómo colaborar?</h3>
          <ul>
            <li>
              Reporta errores o mejora sugerida en el
              <a href="https://github.com/seguridades/activos/issues" target="_blank"
                >repositorio de GitHub</a
              >
            </li>
            <li>Sugiere nuevas funcionalidades o traducciones</li>
            <li>Comparte este recurso con otros colectivos o equipos de defensa digital</li>
            <li>Contribuye al desarrollo: ¡todo aporte es bienvenido!</li>
          </ul>
        </div>
      </div>
    </section>

    <!-- Botones principales -->
    <section class="mb-5">
      <div class="d-flex flex-wrap gap-3 justify-content-center">
        <router-link to="/setup" class="btn btn-lg btn-outline-info w-100 w-md-auto"
          >⚙️ Configurar Organización</router-link
        >
        <router-link to="/add-asset" class="btn btn-lg btn-outline-success w-100 w-md-auto"
          >➕ Agregar Activo</router-link
        >
        <router-link to="/list" class="btn btn-lg btn-outline-primary w-100 w-md-auto"
          >📋 Ver Listado</router-link
        >
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
  max-width: 960px;
  margin: auto;
}

.card-title {
  font-weight: bold;
  margin-bottom: 1rem;
}

.card-text {
  margin-bottom: 0.8rem;
}

.card-body ul,
.card-body ol {
  padding-left: 1.25rem;
}

.card-body li {
  margin-bottom: 0.5rem;
}

.card-body p {
  line-height: 1.6;
}

.btn-outline-info,
.btn-outline-success,
.btn-outline-primary {
  transition: all 0.3s ease-in-out;
}

.btn-outline-info:hover,
.btn-outline-success:hover,
.btn-outline-primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}
</style>
