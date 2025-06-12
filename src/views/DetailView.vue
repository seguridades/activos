<template>
  <div class="container mt-5">
    <!-- Título -->
    <div class="d-flex justify-content-between align-items-center flex-wrap gap-3 mb-4">
      <h4 class="mb-0 d-flex align-items-center fs-5 fw-normal text-muted">
        <i class="bi bi-eye me-2"></i> Detalles del Activo
      </h4>
      <router-link to="/list" class="btn btn-outline-dark d-flex align-items-center gap-2">
        <i class="bi bi-arrow-left"></i> Volver a la lista
      </router-link>
    </div>

    <!-- Mensaje si no hay activo -->
    <div v-if="!selectedAsset" class="alert alert-warning">
      No se ha seleccionado un activo válido.
      <router-link to="/list" class="alert-link">Volver a la lista</router-link>
    </div>

    <!-- Card principal con todos los datos -->
    <div v-else class="card shadow-sm border mb-4">
      <div class="card-body">
        <h5 class="card-title d-flex align-items-center">
          <i :class="`bi ${getIconByType(selectedAsset.type)} me-2`"></i>
          {{ assetName }}
        </h5>

        <!-- Información general -->
        <ul class="list-group list-group-flush">
          <!-- Datos comunes -->
          <li class="list-group-item">
            <strong>Tipo:</strong> {{ formatAssetType(selectedAsset.type) }}
          </li>
          <li class="list-group-item"><strong>Nombre:</strong> {{ assetName }}</li>
          <li class="list-group-item">
            <strong>Sensibilidad:</strong>
            <span
              :class="`badge bg-${getSensitivityColor(selectedAsset.sensitivity)}`"
              class="ms-2"
            >
              {{ formatSensitivity(selectedAsset.sensitivity) }}
            </span>
          </li>
          <li class="list-group-item">
            <strong>Descripción:</strong> {{ selectedAsset.description || '-' }}
          </li>
          <li class="list-group-item">
            <strong>Área Responsable:</strong> {{ selectedAsset.area?.name || 'Sin área' }}
          </li>
          <li class="list-group-item">
            <strong>Responsable:</strong> {{ selectedAsset.person || 'Sin responsable' }}
          </li>
          <li class="list-group-item">
            <strong>Etiquetas / Categorías:</strong> {{ selectedAsset.tags || '-' }}
          </li>

          <!-- Hardware -->
          <li v-if="selectedAsset.type === 'hardware'" class="list-group-item">
            <strong>Número de Serie:</strong> {{ selectedAsset.serialNumber || '-' }}
          </li>
          <li v-if="selectedAsset.type === 'hardware'" class="list-group-item">
            <strong>Marca y Modelo:</strong> {{ selectedAsset.brandModel || '-' }}
          </li>
          <li v-if="selectedAsset.type === 'hardware'" class="list-group-item">
            <strong>Ubicación Física:</strong> {{ selectedAsset.physicalLocation || '-' }}
          </li>
          <li v-if="selectedAsset.type === 'hardware'" class="list-group-item">
            <strong>Fecha de Adquisición:</strong> {{ formatDate(selectedAsset.acquisitionDate) }}
          </li>
          <li v-if="selectedAsset.type === 'hardware'" class="list-group-item">
            <strong>Estado del Dispositivo:</strong>
            {{ formatDeviceStatus(selectedAsset.deviceStatus) }}
          </li>
          <li v-if="selectedAsset.type === 'hardware'" class="list-group-item">
            <strong>Último Mantenimiento:</strong> {{ formatDate(selectedAsset.lastMaintenance) }}
          </li>
          <li v-if="selectedAsset.type === 'hardware'" class="list-group-item">
            <strong>Dispositivo Cifrado:</strong>
            {{ selectedAsset.isEncrypted === 'si' ? 'Sí' : 'No' }}
          </li>

          <!-- Software -->
          <li v-if="selectedAsset.type === 'software'" class="list-group-item">
            <strong>Tipo de Cuenta:</strong> {{ selectedAsset.accountType || '-' }}
          </li>
          <li v-if="selectedAsset.type === 'software'" class="list-group-item">
            <strong>Usuario (Login):</strong> {{ selectedAsset.username || '-' }}
          </li>
          <li v-if="selectedAsset.type === 'software'" class="list-group-item">
            <strong>URL:</strong>
            <a :href="selectedAsset.url" target="_blank" v-if="selectedAsset.url">{{
              selectedAsset.url
            }}</a>
            <span v-else>-</span>
          </li>
          <li v-if="selectedAsset.type === 'software'" class="list-group-item">
            <strong>¿Tiene 2FA?:</strong> {{ selectedAsset.hasTwoFactor === 'si' ? 'Sí' : 'No' }}
          </li>
          <li v-if="selectedAsset.type === 'software'" class="list-group-item">
            <strong>Cuenta Compartida:</strong> {{ selectedAsset.sharedAccount ? 'Sí' : 'No' }}
          </li>
          <li v-if="selectedAsset.type === 'software'" class="list-group-item">
            <strong>Fecha de Renovación:</strong> {{ formatDate(selectedAsset.renewalDate) }}
          </li>

          <!-- Digital -->
          <li v-if="selectedAsset.type === 'digital'" class="list-group-item">
            <strong>Tipo de Archivo:</strong> {{ selectedAsset.digitalType || '-' }}
          </li>
          <li v-if="selectedAsset.type === 'digital'" class="list-group-item">
            <strong>Ubicación:</strong> {{ selectedAsset.location || '-' }}
          </li>
          <li v-if="selectedAsset.type === 'digital'" class="list-group-item">
            <strong>Fecha de Creación:</strong> {{ formatDate(selectedAsset.creationDate) }}
          </li>
          <li v-if="selectedAsset.type === 'digital'" class="list-group-item">
            <strong>¿Acceso restringido?:</strong>
            {{ selectedAsset.restrictedAccess === 'si' ? 'Sí' : 'No' }}
          </li>
        </ul>
      </div>

      <!-- Botones de acción -->
      <div class="card-footer bg-white d-flex justify-content-end gap-2">
        <button
          @click="exportToPDF(selectedAsset)"
          class="btn btn-outline-primary d-flex align-items-center gap-2"
        >
          <i class="bi bi-file-earmark-pdf"></i> Exportar como PDF
        </button>
        <router-link
          :to="`/edit/${selectedAsset.id}`"
          class="btn btn-outline-primary d-flex align-items-center gap-2"
        >
          <i class="bi bi-pencil"></i> Editar
        </router-link>
      </div>
    </div>
  </div>
</template>

<script>
import { jsPDF } from 'jspdf'

export default {
  data() {
    const assets = JSON.parse(localStorage.getItem('assets') || '[]')
    const assetId = parseInt(this.$route.params.id)
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
    getIconByType(type) {
      switch (type) {
        case 'hardware':
          return 'bi-laptop'
        case 'software':
          return 'bi-person-badge'
        case 'digital':
          return 'bi-cloud'
        default:
          return 'bi-question-circle'
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
    exportToPDF(asset) {
      const doc = new jsPDF()

      // Encabezado del PDF
      const now = new Date()
      doc.setFontSize(16)
      doc.text(`Activo: ${this.assetName}`, 14, 14)

      doc.setFontSize(12)
      doc.text(`Responsable: ${asset.person || 'No definido'}`, 14, 22)
      doc.text(`Fecha de generación: ${now.toLocaleString()}`, 14, 30)

      let startY = 40
      const bgColor = [248, 249, 250] // #f8f9fa → gris claro

      // Datos comunes
      const fields = [
        { label: 'Tipo', value: this.formatAssetType(asset.type) },
        { label: 'Nombre', value: this.assetName },
        { label: 'Descripción', value: asset.description || '-' },
        {
          label: 'Serie / Usuario / Ubicación',
          value: asset.serialNumber || asset.username || asset.location || '-',
        },
        { label: 'Nivel de Sensibilidad', value: this.formatSensitivity(asset.sensitivity) },
        { label: 'Área Responsable', value: asset.area?.name || '-' },
        { label: 'Persona Responsable', value: asset.person || '-' },
        { label: 'Etiquetas', value: asset.tags || '-' },
      ]

      // Añadir datos específicos según tipo
      if (asset.type === 'hardware') {
        fields.push(
          { label: 'Número de Serie', value: asset.serialNumber || '-' },
          { label: 'Marca y Modelo', value: asset.brandModel || '-' },
          { label: 'Ubicación Física', value: asset.physicalLocation || '-' },
          { label: 'Fecha de Adquisición', value: this.formatDate(asset.acquisitionDate) },
          { label: 'Estado del Dispositivo', value: this.formatDeviceStatus(asset.deviceStatus) },
          { label: 'Último Mantenimiento', value: this.formatDate(asset.lastMaintenance) },
          { label: 'Dispositivo Cifrado', value: asset.isEncrypted === 'si' ? 'Sí' : 'No' },
        )
      }

      if (asset.type === 'software') {
        fields.push(
          { label: 'Usuario (Login)', value: asset.username || '-' },
          { label: 'URL', value: asset.url || '-' },
          { label: 'Tipo de Cuenta', value: asset.accountType || '-' },
          { label: '¿Tiene 2FA?', value: asset.hasTwoFactor === 'si' ? 'Sí' : 'No' },
          { label: 'Cuenta Compartida', value: asset.sharedAccount ? 'Sí' : 'No' },
          { label: 'Fecha de Renovación', value: this.formatDate(asset.renewalDate) },
        )
      }

      if (asset.type === 'digital') {
        fields.push(
          { label: 'Tipo de Archivo', value: asset.digitalType || '-' },
          { label: 'Ubicación', value: asset.location || '-' },
          { label: 'Fecha de Creación', value: this.formatDate(asset.creationDate) },
          { label: '¿Acceso restringido?', value: asset.restrictedAccess === 'si' ? 'Sí' : 'No' },
        )
      }

      // Agregar rectángulo de fondo gris claro
      doc.setFillColor(bgColor[0], bgColor[1], bgColor[2])
      doc.rect(12, startY - 5, 180, fields.length * 6 + 10, 'F')

      // Agregar contenido
      for (const f of fields) {
        doc.text(`${f.label}: ${f.value}`, 14, startY)
        startY += 6
      }

      // Guardar documento
      doc.save(`activo-${asset.id}.pdf`)
      this.showNotification('Éxito', 'PDF generado con formato limpio y coherente', 'success')
    },
    showNotification(title, message, type = 'success') {
      this.notification = { show: true, title, message, type }
      setTimeout(() => {
        this.notification.show = false
      }, 3000)
    },
  },
}
</script>

<style scoped>
.card-title i.bi {
  font-size: 1.2rem;
}

.list-group-item strong {
  min-width: 180px;
  display: inline-block;
  font-weight: 600;
}

.btn i.bi {
  font-size: 1rem;
}
</style>
