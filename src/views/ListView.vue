<template>
  <div class="container mt-5">
    <h2>Lista de Activos</h2>

    <!-- Filtros -->
    <div class="row g-3 mb-4">
      <div class="col-md-3">
        <input
          v-model="searchQuery"
          type="text"
          class="form-control"
          placeholder="Buscar por nombre..."
        />
      </div>
      <div class="col-md-3">
        <select v-model="selectedAreaFilter" class="form-select">
          <option value="">Todas las áreas</option>
          <option v-for="(area, idx) in areas" :key="idx" :value="area.name">
            {{ area.name }}
          </option>
        </select>
      </div>
      <div class="col-md-2">
        <select v-model="selectedTypeFilter" class="form-select">
          <option value="">Todos los tipos</option>
          <option value="hardware">Hardware</option>
          <option value="software">Cuenta Online</option>
          <option value="digital">Recurso Digital</option>
        </select>
      </div>
      <div class="col-md-2">
        <input v-model="personFilter" type="text" class="form-control" placeholder="Responsable" />
      </div>
      <div class="col-md-2">
        <input v-model="tagFilter" type="text" class="form-control" placeholder="Etiqueta" />
      </div>
    </div>

    <!-- Mensaje si no hay resultados -->
    <div v-if="filteredAssets.length === 0 && assets.length > 0" class="alert alert-info">
      No hay activos que coincidan con tu búsqueda.
    </div>

    <div v-if="assets.length === 0" class="alert alert-warning">
      No hay activos registrados. Ve a
      <router-link to="/add-asset">Agregar Activo</router-link> para comenzar.
    </div>

    <!-- Tabla -->
    <div v-else class="table-responsive">
      <table class="table table-striped table-bordered">
        <thead class="table-dark">
          <tr>
            <th>Tipo</th>
            <th>Nombre</th>
            <th>Serie/Usuario/Loc.</th>
            <th>Sensibilidad</th>
            <th>Área</th>
            <th>Responsable</th>
            <th>Etiquetas</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="asset in filteredAssets" :key="asset.id">
            <td>{{ formatAssetType(asset.type) }}</td>
            <td>{{ getAssetName(asset) }}</td>
            <td>
              {{ asset.type === 'hardware' ? asset.serialNumber || '-' : '' }}
              {{ asset.type === 'software' ? asset.username || '-' : '' }}
              {{ asset.type === 'digital' ? asset.location || '-' : '' }}
            </td>
            <td>
              <span :class="`badge bg-${getSensitivityColor(asset.sensitivity)}`">
                {{ formatSensitivity(asset.sensitivity) }}
              </span>
            </td>
            <td>{{ asset.area?.name || 'Sin área' }}</td>
            <td>{{ asset.person || 'Sin responsable' }}</td>
            <td>{{ asset.tags || '-' }}</td>
            <td>
              <router-link :to="`/detail/${asset.id}`" class="btn btn-sm btn-outline-primary me-1"
                >Ver Detalles</router-link
              >
              <router-link :to="`/edit/${asset.id}`" class="btn btn-sm btn-outline-warning me-1"
                >Editar</router-link
              >
              <button @click="deleteAsset(asset.id)" class="btn btn-sm btn-danger">Eliminar</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Botones de exportación -->
    <div class="mt-4">
      <button @click="exportJSON" class="btn btn-info me-2">Exportar JSON</button>
      <button @click="exportFullData('csv')" class="btn btn-secondary me-2">
        Exportar Todos los Campos (CSV)
      </button>
      <button @click="exportFullData('pdf')" class="btn btn-primary me-2">
        Exportar Tarjetas (PDF)
      </button>
      <router-link to="/" class="btn btn-secondary">Volver al Inicio</router-link>
    </div>

    <!-- Notificación -->
    <MyNotification
      v-if="notification.show"
      :title="notification.title"
      :message="notification.message"
      :type="notification.type"
    />
  </div>
</template>

<script>
import MyNotification from '@/components/MyNotification.vue'
import { saveAs } from 'file-saver'
import { jsPDF } from 'jspdf'

export default {
  components: {
    MyNotification,
  },
  data() {
    return {
      assets: JSON.parse(localStorage.getItem('assets') || '[]'),
      searchQuery: '',
      selectedAreaFilter: '',
      selectedTypeFilter: '',
      personFilter: '',
      tagFilter: '',
      notification: {
        show: false,
        title: '',
        message: '',
        type: 'success',
      },
    }
  },
  computed: {
    organization() {
      const orgData = localStorage.getItem('organization')
      return orgData ? JSON.parse(orgData) : { name: '', responsable: '', areas: [] }
    },
    areas() {
      return this.organization.areas || []
    },
    filteredAssets() {
      let result = [...this.assets]

      if (this.searchQuery) {
        const query = this.searchQuery.toLowerCase()
        result = result.filter((asset) => this.getAssetName(asset).toLowerCase().includes(query))
      }

      if (this.selectedTypeFilter) {
        result = result.filter((asset) => asset.type === this.selectedTypeFilter)
      }

      if (this.selectedAreaFilter) {
        result = result.filter((asset) => asset.area?.name === this.selectedAreaFilter)
      }

      if (this.personFilter) {
        const personQuery = this.personFilter.trim().toLowerCase()
        result = result.filter((asset) => (asset.person || '').toLowerCase().includes(personQuery))
      }

      if (this.tagFilter) {
        const tagQuery = this.tagFilter.trim().toLowerCase()
        result = result.filter((asset) => {
          if (!asset.tags) return false
          return asset.tags.toLowerCase().includes(tagQuery)
        })
      }

      return result
    },
  },
  methods: {
    getAssetName(asset) {
      switch (asset.type) {
        case 'hardware':
          return asset.name || '-'
        case 'software':
          return asset.accountName || '-'
        case 'digital':
          return asset.digitalName || '-'
        default:
          return '-'
      }
    },
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
    deleteAsset(id) {
      const confirmDelete = confirm('¿Estás seguro de eliminar este activo?')
      if (!confirmDelete) return

      this.assets = this.assets.filter((asset) => asset.id !== id)
      localStorage.setItem('assets', JSON.stringify(this.assets))

      this.showNotification('Éxito', 'Activo eliminado correctamente', 'success')
      setTimeout(() => {
        this.$router.push('/list')
      }, 1000)
    },
    exportJSON() {
      try {
        const blob = new Blob([localStorage.getItem('assets')], { type: 'application/json' })
        saveAs(blob, 'activos.json')
        this.showNotification('Éxito', 'Archivo JSON descargado correctamente', 'success')
      } catch (error) {
        this.showNotification('Error', 'Error al exportar como JSON', 'danger')
        console.error(error)
      }
    },
    exportFullData(format) {
      const filtered = this.filteredAssets
      if (!filtered.length) {
        this.showNotification('Advertencia', 'No hay activos para exportar', 'warning')
        return
      }

      if (format === 'csv') {
        this.exportAllFieldsToCSV(filtered)
      } else if (format === 'pdf') {
        this.exportCardsToPDF(filtered)
      }
    },
    exportAllFieldsToCSV(data) {
      const headers = [
        'Tipo',
        'Nombre',
        'Descripción',
        'Serie/Usuario/Loc.',
        'Usuario',
        '¿Tiene 2FA?',
        'URL',
        'Tipo de Cuenta',
        'Fecha de renovación',
        'Cuenta compartida',
        'Marca y Modelo',
        'Fecha de Adquisición',
        'Ubicación Física',
        'Estado del Dispositivo',
        'Último Mantenimiento',
        'Dispositivo Cifrado',
        'Tipo de Archivo',
        'Fecha de Creación',
        'Acceso Restringido',
        'Nivel de Sensibilidad',
        'Área',
        'Responsable',
        'Etiquetas',
      ]

      const csvRows = [headers.join(',')]

      for (const asset of data) {
        const row = [
          this.formatAssetType(asset.type),
          this.getAssetName(asset),
          asset.description || '',
          asset.serialNumber || asset.username || asset.location || '',
          asset.username || '',
          asset.hasTwoFactor === 'si' ? 'Sí' : 'No',
          asset.url || '',
          asset.accountType || '',
          asset.renewalDate || '',
          asset.sharedAccount ? 'Sí' : 'No',
          asset.brandModel || '',
          asset.acquisitionDate || '',
          asset.physicalLocation || '',
          this.formatDeviceStatus(asset.deviceStatus),
          asset.lastMaintenance || '',
          asset.isEncrypted === 'si' ? 'Sí' : 'No',
          asset.digitalType || '',
          asset.creationDate || '',
          asset.restrictedAccess === 'si' ? 'Sí' : 'No',
          this.formatSensitivity(asset.sensitivity),
          asset.area?.name || '',
          asset.person || '',
          asset.tags || '',
        ]
        csvRows.push(row.map((r) => `"${r}"`).join(','))
      }

      const csvString = csvRows.join('\n')
      const blob = new Blob(['\uFEFF', csvString], { type: 'text/csv;charset=utf-8,' })
      saveAs(blob, 'activos-completos.csv')

      this.showNotification('Éxito', 'Archivo CSV generado con todos los campos', 'success')
    },
    exportCardsToPDF(data) {
      const doc = new jsPDF()

      // Información de la organización
      const now = new Date()
      doc.setFontSize(16)
      doc.text(`Organización: ${this.organization.name}`, 14, 14)
      doc.setFontSize(12)
      doc.text(`Responsable: ${this.organization.responsable || 'No definido'}`, 14, 22)
      doc.text(`Fecha de generación: ${now.toLocaleString()}`, 14, 30)

      let startY = 40

      // Colores por sensibilidad
      const sensitivityColors = {
        alta: [255, 99, 71],
        media: [255, 204, 0],
        baja: [144, 238, 144],
      }

      // Generar tarjetas
      for (const asset of data) {
        const fields = []

        // Datos comunes
        fields.push({ label: 'Tipo', value: this.formatAssetType(asset.type) })
        fields.push({ label: 'Nombre', value: this.getAssetName(asset) })
        fields.push({ label: 'Descripción', value: asset.description || '-' })
        fields.push({
          label: 'Serie / Usuario / Ubicación',
          value: asset.serialNumber || asset.username || asset.location || '-',
        })
        fields.push({
          label: 'Nivel de Sensibilidad',
          value: this.formatSensitivity(asset.sensitivity),
        })
        fields.push({ label: 'Área', value: asset.area?.name || '-' })
        fields.push({ label: 'Responsable', value: asset.person || '-' })
        fields.push({ label: 'Etiquetas', value: asset.tags || '-' })

        // Datos específicos por tipo
        if (asset.type === 'hardware') {
          fields.push({ label: 'Número de Serie', value: asset.serialNumber || '-' })
          fields.push({ label: 'Marca y Modelo', value: asset.brandModel || '-' })
          fields.push({ label: 'Ubicación Física', value: asset.physicalLocation || '-' })
          fields.push({
            label: 'Estado del Dispositivo',
            value: this.formatDeviceStatus(asset.deviceStatus),
          })
          fields.push({ label: 'Último Mantenimiento', value: asset.lastMaintenance || '-' })
          fields.push({
            label: 'Dispositivo Cifrado',
            value: asset.isEncrypted === 'si' ? 'Sí' : 'No',
          })
        }

        if (asset.type === 'software') {
          fields.push({ label: 'Usuario', value: asset.username || '-' })
          fields.push({ label: 'URL', value: asset.url || '-' })
          fields.push({ label: 'Tipo de Cuenta', value: asset.accountType || '-' })
          fields.push({ label: '¿Tiene 2FA?', value: asset.hasTwoFactor === 'si' ? 'Sí' : 'No' })
          fields.push({ label: 'Cuenta Compartida', value: asset.sharedAccount ? 'Sí' : 'No' })
          fields.push({ label: 'Fecha de Renovación', value: asset.renewalDate || '-' })
        }

        if (asset.type === 'digital') {
          fields.push({ label: 'Tipo de Archivo', value: asset.digitalType || '-' })
          fields.push({ label: 'Ubicación', value: asset.location || '-' })
          fields.push({ label: 'Fecha de Creación', value: asset.creationDate || '-' })
          fields.push({
            label: '¿Acceso restringido?',
            value: asset.restrictedAccess === 'si' ? 'Sí' : 'No',
          })
        }

        // Definir color según sensibilidad
        const bgColor = sensitivityColors[asset.sensitivity] || [255, 255, 255] // blanco por defecto

        // Añadir rectángulo de fondo
        doc.setFillColor(bgColor[0], bgColor[1], bgColor[2])
        doc.rect(12, startY - 5, 180, fields.length * 5 + 10, 'F')

        // Agregar texto
        const content = fields.map((f) => `${f.label}: ${f.value}`).join('\n')
        doc.setFont(undefined, 'normal')
        doc.text(content, 14, startY)
        startY += fields.length * 5 + 20

        // Separador entre tarjetas
        doc.setDrawColor(200, 200, 200)
        doc.setLineWidth(0.5)
        doc.line(12, startY - 10, 192, startY - 10)

        // Salto de página si se pasa del límite
        if (startY > 280) {
          doc.addPage()
          startY = 20
          // Volver a poner encabezado en nueva página
          doc.setFontSize(16)
          doc.text(`Organización: ${this.organization.name}`, 14, 14)
          doc.setFontSize(12)
          doc.text(`Responsable: ${this.organization.responsable || 'No definido'}`, 14, 22)
          doc.text(`Fecha de generación: ${now.toLocaleString()}`, 14, 30)
          startY = 40
        }
      }

      // Guardar documento
      doc.save('activos-tarjetas.pdf')
      this.showNotification('Éxito', 'PDF generado con tarjetas estilizadas', 'success')
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
