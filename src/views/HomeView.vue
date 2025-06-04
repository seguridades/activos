<template>
  <div class="container mt-5">
    <!-- Notificación de Versión Beta -->
    <div class="alert alert-info alert-dismissible fade show small" role="alert">
      <strong>🧪 Versión Beta</strong> – Esta es una versión funcional y usable del mapeador. Se
      sigue trabajando activamente en nuevas funcionalidades y mejoras.
      <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Cerrar"></button>
    </div>

    <!-- Introducción -->
    <section class="mb-5">
      <h2>📌 ¿Por qué es importante mapear tus activos digitales?</h2>
      <p>
        Para organizaciones, defensores de derechos humanos y periodistas, tener un inventario
        actualizado de los activos digitales es fundamental. Esto permite identificar puntos
        críticos, proteger información sensible y garantizar el acceso en situaciones de emergencia
        o ausencia de equipos clave.
      </p>
      <p>
        Este mapeador te ayuda a registrar hardware, cuentas online y recursos digitales de forma
        sencilla, segura y sin necesidad de conexión permanente.
      </p>
    </section>

    <!-- Tipos de activos -->
    <section class="mb-5">
      <h2>🔍 ¿Qué activos puedes mapear con esta herramienta?</h2>
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
    </section>

    <!-- Consideraciones -->
    <section class="mb-5">
      <h2>⚠️ Consideraciones Importantes</h2>
      <ul>
        <li>Todos los datos se guardan localmente en tu navegador → privacidad asegurada</li>
        <li>No uses contraseñas ni datos altamente sensibles directamente en el formulario</li>
        <li>Puedes exportar tu listado en cualquier momento como respaldo</li>
        <li>La herramienta no requiere internet para funcionar</li>
        <li>Este proyecto es de código abierto y está bajo licencia Creative Commons</li>
      </ul>
    </section>

    <!-- Manual resumido -->
    <section class="mb-5">
      <h2>📘 Manual Rápido de Uso</h2>
      <ol>
        <li>
          <strong>Configura tu organización</strong>: Ve a "Configuración" y registra el nombre y
          responsable principal de tu organización
        </li>
        <li>
          <strong>Agrega áreas</strong>: Define las áreas de trabajo (ej: Comunicaciones, Seguridad,
          Legales)
        </li>
        <li>
          <strong>Asocia personas a cada área</strong>: Puedes usar nombres reales, apodos o
          seudónimos
        </li>
        <li>
          <strong>Agrega un activo</strong>: Selecciona tipo (hardware, cuenta online o recurso
          digital) y completa los campos relevantes
        </li>
        <li>
          <strong>Asigna el activo</strong>: Elige el área y responsable desde los datos registrados
        </li>
        <li>
          <strong>Ver Listado</strong>: Revisa, filtra y exporta todos los activos desde la lista
        </li>
        <li><strong>Editar / Eliminar</strong>: Desde la vista de detalles o edición</li>
        <li><strong>Exportar</strong>: Descarga tu listado en formato JSON, CSV o PDF</li>
      </ol>
    </section>

    <!-- Créditos -->
    <section class="mb-5">
      <h2>🎓 Créditos & Licencia</h2>
      <p>
        Esta herramienta fue creada por <strong>Seguridades.org</strong>, con el objetivo de ayudar
        a colectivos, defensores de derechos humanos y periodistas a gestionar sus activos digitales
        de forma segura y autónoma.
      </p>
      <p>
        <strong>Licencia:</strong> Creative Commons Attribution 4.0 International License
        <a href="https://creativecommons.org/licenses/by/4.0/" target="_blank">Ver licencia</a>
      </p>
      <p>
        <strong>Descárgala aquí:</strong>
        <a href="https://github.com/seguridades/activos" target="_blank"
          >GitHub - Mapeador de Activos</a
        >
      </p>
    </section>

    <!-- Colaborar -->
    <section class="mb-5">
      <h2>🛠️ ¿Cómo colaborar?</h2>
      <ul>
        <li>
          Reporta errores o mejoras en el
          <a href="https://github.com/seguridades/activos/issues" target="_blank"
            >repositorio de GitHub</a
          >
        </li>
        <li>Sugiere nuevas funcionalidades o traducciones</li>
        <li>Comparte este recurso con otros colectivos o equipos de defensa digital</li>
        <li>Contribuye al desarrollo: ¡todo aporte es bienvenido!</li>
      </ul>
    </section>

    <!-- Botones principales -->
    <div class="d-flex gap-3 mb-5">
      <router-link to="/setup" class="btn btn-info btn-lg">⚙️ Configurar Organización</router-link>
      <router-link to="/add-asset" class="btn btn-success btn-lg">➕ Agregar Activo</router-link>
      <router-link to="/list" class="btn btn-primary btn-lg">📋 Ver Listado</router-link>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HomeView',
}
</script>

<style scoped>
h2 {
  color: #2c3e50;
  margin-bottom: 1rem;
}

section ul,
section ol {
  padding-left: 1.25rem;
}

.btn-lg {
  font-size: 1rem;
}

.small,
small {
  font-size: 0.9rem;
}
</style>
