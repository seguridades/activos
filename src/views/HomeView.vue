<template>
  <div class="px-3">
    <!-- Notificación de Beta -->
    <div class="alert alert-info alert-dismissible fade show small text-center" role="alert">
      <strong>🧪 Versión Beta</strong> – Es usable pero seguimos trabajando en mejoras.
      <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Cerrar"></button>
    </div>

    <!-- Tarjeta Principal (ancho completo) -->
    <section class="mb-5">
      <div class="card shadow-sm border-0 w-100 mb-4">
        <div class="card-body text-center p-5">
          <h2 class="card-title h4 text-primary fw-bold">
            📌 ¿Por qué es importante mapear tus activos digitales?
          </h2>
          <p class="card-text mt-3">
            Para organizaciones, defensores de derechos humanos y periodistas, tener un inventario
            actualizado de los activos digitales es fundamental. Esto permite identificar puntos
            críticos, proteger información sensible y garantizar acceso en situaciones de emergencia
            o ausencia de equipos clave.
          </p>
          <p class="card-text">
            Este mapeador te ayuda a registrar hardware, cuentas online y recursos digitales de
            forma sencilla, segura y sin conexión permanente.
          </p>
        </div>
      </div>
    </section>

    <!-- Secciones en filas de 2 columnas -->
    <section class="mb-5">
      <div class="row g-4">
        <!-- Consideraciones importantes -->
        <div class="col-md-6">
          <div class="card shadow-sm border-0 h-100">
            <div class="card-body">
              <h3 class="card-title text-danger d-flex align-items-center">
                <i class="bi bi-exclamation-triangle-fill me-2"></i> Consideraciones Importantes
              </h3>
              <ul class="mt-3">
                <li>
                  Todos los datos se guardan localmente en tu navegador → privacidad asegurada
                </li>
                <li>
                  No uses contraseñas ni datos altamente sensibles directamente en el formulario
                </li>
                <li>Puedes exportar tu listado en cualquier momento como respaldo</li>
                <li>La herramienta no requiere internet para funcionar</li>
                <li>Este proyecto está bajo licencia Creative Commons</li>
              </ul>
            </div>
          </div>
        </div>

        <!-- Manual rápido -->
        <div class="col-md-6">
          <div class="card shadow-sm border-0 h-100">
            <div class="card-body">
              <h3 class="card-title text-success d-flex align-items-center">
                <i class="bi bi-journal-text me-2"></i> Manual Rápido de Uso
              </h3>
              <ol class="mt-3">
                <li><strong>Configura tu organización</strong>: Ve a "Configuración"</li>
                <li><strong>Agrega áreas</strong>: Define las áreas de trabajo</li>
                <li><strong>Asocia personas</strong>: Puedes usar apodos o seudónimos</li>
                <li><strong>Agrega un activo</strong>: Completa los campos según tipo</li>
                <li><strong>Exporta</strong>: En JSON, CSV o PDF según tus necesidades</li>
              </ol>
            </div>
          </div>
        </div>

        <!-- Tipos de activos -->
        <div class="col-md-6">
          <div class="card shadow-sm border-0 h-100">
            <div class="card-body">
              <h3 class="card-title text-secondary d-flex align-items-center">
                <i class="bi bi-diagram-3 me-2"></i> ¿Qué activos puedes mapear?
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

        <!-- Créditos -->
        <div class="col-md-6">
          <div class="card shadow-sm border-0 h-100">
            <div class="card-body">
              <h3 class="card-title text-dark d-flex align-items-center">
                <i class="bi bi-person-circle me-2"></i> Créditos & Licencia
              </h3>
              <p class="mt-3">
                Esta herramienta fue creada por <strong>Seguridades.org</strong>, con el objetivo de
                ayudar a colectivos, defensores y periodistas a gestionar sus activos de forma
                segura.
              </p>
              <p>
                <strong>Licencia:</strong> Creative Commons Attribution 4.0 International License
                <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank"
                  >Ver licencia</a
                >
              </p>
              <p class="mb-0">
                <strong>Descárgala aquí:</strong>
                <a href="https://github.com/seguridades/activos" target="_blank"
                  >GitHub - Mapeador de Activos</a
                >
              </p>
            </div>
          </div>
        </div>

        <!-- Cómo colaborar -->
        <div class="col-md-6">
          <div class="card shadow-sm border-0 h-100">
            <div class="card-body">
              <h3 class="card-title text-warning d-flex align-items-center">
                <i class="bi bi-tools me-2"></i> ¿Cómo colaborar?
              </h3>
              <ul class="mt-3">
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
        </div>

        <!-- Sobre la herramienta -->
        <div class="col-md-6">
          <div class="card shadow-sm border-0 h-100">
            <div class="card-body">
              <h3 class="card-title text-info d-flex align-items-center">
                <i class="bi bi-shield-check me-2"></i> Sobre esta herramienta
              </h3>
              <p class="mt-3">
                Esta aplicación web funciona completamente offline. Todo lo que agregues se guarda
                en tu navegador, y puedes exportarlo fácilmente.
              </p>
              <p>
                No usamos servidores externos ni base de datos en la nube. Tus datos están solo
                donde tú los dejas.
              </p>
              <p class="mb-0">
                Ideal para organizaciones pequeñas, proyectos comunitarios y equipos que priorizan
                la autogestión digital.
              </p>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Botones principales -->
    <section class="mb-5 text-center">
      <div class="d-flex flex-wrap justify-content-center gap-3">
        <router-link to="/setup" class="btn btn-lg btn-outline-info px-4 py-2">
          ⚙️ Configurar Organización
        </router-link>
        <router-link to="/add-asset" class="btn btn-lg btn-outline-success px-4 py-2">
          ➕ Agregar Activo
        </router-link>
        <router-link to="/list" class="btn btn-lg btn-outline-primary px-4 py-2">
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
</style>
