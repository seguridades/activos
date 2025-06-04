<template>
  <div class="container mt-5">
    <h2>Detalles del Activo</h2>

    <!-- Mensaje si no hay activo seleccionado -->
    <div v-if="!selectedAsset" class="alert alert-warning">
      No se ha seleccionado un activo válido.
      <router-link to="/list" class="alert-link">Volver a la lista</router-link>
    </div>

    <!-- Detalles del activo -->
    <div v-else>
      <!-- Tipo de activo -->
      <div class="card mb-4">
        <div class="card-header bg-primary text-white">
          {{ formatAssetType(selectedAsset.type) }}
        </div>
        <div class="card-body">
          <ul class="list-group list-group-flush">
            <!-- Nombre -->
            <li class="list-group-item"><strong>Nombre:</strong> {{ assetName }}</li>

            <!-- Descripción -->
            <li class="list-group-item">
              <strong>Descripción:</strong> {{ selectedAsset.description || 'No definida' }}
            </li>

            <!-- Nivel de Sensibilidad -->
            <li class="list-group-item">
              <strong>Sensibilidad:</strong>
              <span
                :class="`badge bg-${getSensitivityColor(selectedAsset.sensitivity)}`"
                style="margin-left: 10px"
              >
                {{ formatSensitivity(selectedAsset.sensitivity) }}
              </span>
            </li>

            <!-- Área Responsable -->
            <li class="list-group-item">
              <strong>Área Responsable:</strong> {{ selectedAsset.area?.name || 'Sin área' }}
            </li>

            <!-- Persona Responsable -->
            <li class="list-group-item">
              <strong>Responsable:</strong> {{ selectedAsset.person || 'Sin responsable' }}
            </li>

            <!-- Etiquetas -->
            <li class="list-group-item">
              <strong>Etiquetas / Categorías:</strong> {{ selectedAsset.tags || '-' }}
            </li>
          </ul>
        </div>
      </div>

      <!-- Campos específicos por tipo -->
      <div v-if="selectedAsset.type === 'hardware'" class="card mb-4">
        <div class="card-header bg-dark text-white">Hardware</div>
        <div class="card-body">
          <ul class="list-group list-group-flush">
            <li class="list-group-item">
              <strong>Número de Serie:</strong> {{ selectedAsset.serialNumber || '-' }}
            </li>
            <li class="list-group-item">
              <strong>Marca y Modelo:</strong> {{ selectedAsset.brandModel || '-' }}
            </li>
            <li class="list-group-item">
              <strong>Ubicación Física:</strong> {{ selectedAsset.physicalLocation || '-' }}
            </li>
            <li class="list-group-item">
              <strong>Fecha de Adquisición:</strong> {{ formatDate(selectedAsset.acquisitionDate) }}
            </li>
            <li class="list-group-item">
              <strong>Dispositivo Cifrado:</strong>
              {{ selectedAsset.isEncrypted === 'si' ? 'Sí' : 'No' }}
            </li>
            <li class="list-group-item">
              <strong>Estado del Dispositivo:</strong>
              {{ formatDeviceStatus(selectedAsset.deviceStatus) }}
            </li>
            <li class="list-group-item">
              <strong>Último Mantenimiento:</strong> {{ formatDate(selectedAsset.lastMaintenance) }}
            </li>
          </ul>
        </div>
      </div>

      <div v-if="selectedAsset.type === 'software'" class="card mb-4">
        <div class="card-header bg-info text-white">Cuenta Online</div>
        <div class="card-body">
          <ul class="list-group list-group-flush">
            <li class="list-group-item">
              <strong>Tipo de Cuenta:</strong> {{ selectedAsset.accountType || '-' }}
            </li>
            <li class="list-group-item">
              <strong>Usuario (Login):</strong> {{ selectedAsset.username || '-' }}
            </li>
            <li class="list-group-item">
              <strong>URL:</strong>
              <a :href="selectedAsset.url" target="_blank">{{ selectedAsset.url || '-' }}</a>
            </li>
            <li class="list-group-item">
              <strong>¿Tiene 2FA?:</strong> {{ selectedAsset.hasTwoFactor === 'si' ? 'Sí' : 'No' }}
            </li>
            <li class="list-group-item">
              <strong>¿Es cuenta compartida?:</strong>
              {{ selectedAsset.sharedAccount ? 'Sí' : 'No' }}
            </li>
            <li class="list-group-item">
              <strong>Fecha de renovación:</strong> {{ formatDate(selectedAsset.renewalDate) }}
            </li>
          </ul>
        </div>
      </div>

      <div v-if="selectedAsset.type === 'digital'" class="card mb-4">
        <div class="card-header bg-secondary text-white">Recurso Digital</div>
        <div class="card-body">
          <ul class="list-group list-group-flush">
            <li class="list-group-item">
              <strong>Tipo de Archivo:</strong> {{ selectedAsset.digitalType || '-' }}
            </li>
            <li class="list-group-item">
              <strong>Ubicación:</strong> {{ selectedAsset.location || '-' }}
            </li>
            <li class="list-group-item">
              <strong>Fecha de creación:</strong> {{ formatDate(selectedAsset.creationDate) }}
            </li>
            <li class="list-group-item">
              <strong>¿Acceso restringido?:</strong>
              {{ selectedAsset.restrictedAccess === 'si' ? 'Sí' : 'No' }}
            </li>
          </ul>
        </div>
      </div>

      <!-- Botón volver -->
      <router-link to="/list" class="btn btn-secondary">Volver a la Lista</router-link>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    const assets = JSON.parse(localStorage.getItem('assets') || '[]')
    const assetId = parseInt(this.$route.params.id)

    // Buscar activo por ID
    const selectedAsset = assets.find((asset) => asset.id === assetId) || null

    return {
      selectedAsset,
    }
  },
  computed: {
    assetName() {
      return (
        this.selectedAsset.name ||
        this.selectedAsset.accountName ||
        this.selectedAsset.digitalName ||
        'Sin nombre'
      )
    },
  },
  methods: {
    formatAssetType(type) {
      switch (type) {
        case 'hardware':
          return 'Hardware'
        case 'software':
          return 'Cuenta Online'
        case 'digital':
          return 'Recurso Digital'
        default:
          return 'Desconocido'
      }
    },
    formatSensitivity(sensitivity) {
      switch (sensitivity) {
        case 'alta':
          return 'Alta'
        case 'media':
          return 'Media'
        case 'baja':
          return 'Baja'
        default:
          return 'No definida'
      }
    },
    getSensitivityColor(sensitivity) {
      switch (sensitivity) {
        case 'alta':
          return 'danger'
        case 'media':
          return 'warning text-dark'
        case 'baja':
          return 'success'
        default:
          return 'secondary'
      }
    },
    formatDeviceStatus(status) {
      switch (status) {
        case 'nuevo':
          return 'Nuevo'
        case 'en_uso':
          return 'En Uso'
        case 'en_reparacion':
          return 'En Reparación'
        case 'inactivo':
          return 'Inactivo'
        default:
          return 'Desconocido'
      }
    },
    formatDate(date) {
      return date ? new Date(date).toLocaleDateString() : '-'
    },
  },
}
</script>
