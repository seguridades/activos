<template>
  <div class="container mt-5">
    <!-- Título + Botón -->
    <div class="d-flex justify-content-between align-items-center flex-wrap gap-3 mb-4">
      <!-- Título -->
      <h2 class="mb-0 d-flex align-items-center">
        <i class="bi bi-list-task me-2"></i> Lista de Activos
      </h2>

      <!-- Botón Agregar Activo -->
      <router-link
        to="/add-asset"
        class="btn btn-outline-success d-flex align-items-center gap-2 btn-sm"
      >
        <i class="bi bi-plus-circle"></i> Agregar Activo
      </router-link>
    </div>

    <!-- Filtros -->
    <div class="row g-3 mb-4 align-items-end">
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

    <!-- Vista en tarjetas - Móvil -->
    <div v-if="isMobile" class="mb-4">
      <div v-for="asset in filteredAssets" :key="asset.id" class="card shadow-sm border mb-3">
        <div class="card-body">
          <h5 class="card-title d-flex align-items-center">
            <i :class="`bi ${getIconByType(asset.type)} me-2`"></i>
            {{ getAssetName(asset) }}
          </h5>

          <ul class="list-unstyled small">
            <li><strong>Tipo:</strong> {{ formatAssetType(asset.type) }}</li>
            <li>
              <strong>Serie/Usuario/Loc.:</strong>
              {{
                asset.type === 'hardware'
                  ? asset.serialNumber || '-'
                  : asset.type === 'software'
                    ? asset.username || '-'
                    : asset.type === 'digital'
                      ? asset.location || '-'
                      : '-'
              }}
            </li>
            <li>
              <strong>Sensibilidad:</strong>
              <span :class="`badge bg-${getSensitivityColor(asset.sensitivity)}`" class="ms-1">
                {{ formatSensitivity(asset.sensitivity) }}
              </span>
            </li>
            <li><strong>Área:</strong> {{ asset.area?.name || '-' }}</li>
            <li><strong>Responsable:</strong> {{ asset.person || '-' }}</li>
            <li><strong>Etiquetas:</strong> {{ asset.tags || '-' }}</li>
          </ul>

          <!-- Acciones -->
          <div class="d-flex justify-content-end gap-2 mt-3">
            <router-link :to="`/detail/${asset.id}`" class="btn btn-sm btn-outline-primary">
              <i class="bi bi-eye"></i> Ver
            </router-link>
            <router-link :to="`/edit/${asset.id}`" class="btn btn-sm btn-outline-warning">
              <i class="bi bi-pencil"></i> Editar
            </router-link>
            <button @click="deleteAsset(asset.id)" class="btn btn-sm btn-danger">
              <i class="bi bi-trash"></i>
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Vista en tabla - Desktop -->
    <div v-if="!isMobile" class="table-responsive d-none d-md-block">
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
            <td>
              <i :class="`bi ${getIconByType(asset.type)} me-2`"></i>
              {{ formatAssetType(asset.type) }}
            </td>
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
            <td class="text-end">
              <div class="btn-group btn-group-sm w-100" role="group" aria-label="Acciones">
                <router-link
                  :to="`/detail/${asset.id}`"
                  class="btn btn-outline-primary"
                  title="Ver detalles"
                >
                  <i class="bi bi-eye"></i>
                </router-link>
                <router-link
                  :to="`/edit/${asset.id}`"
                  class="btn btn-outline-warning"
                  title="Editar activo"
                >
                  <i class="bi bi-pencil"></i>
                </router-link>
                <button
                  @click="deleteAsset(asset.id)"
                  class="btn btn-outline-danger"
                  title="Eliminar activo"
                >
                  <i class="bi bi-trash"></i>
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Mensaje de cuántos activos se muestran -->
    <!-- Botón Reiniciar filtros -->
    <button
      @click="resetFilters"
      class="btn btn-outline-secondary w-100"
      title="Limpiar todos los filtros"
    >
      <i class="bi bi-arrow-counterclockwise me-1"></i> Reiniciar filtros
    </button>
    <div v-if="assets.length > 0" class="mt-3 small text-muted d-flex align-items-center">
      <i class="bi bi-card-list me-1"></i>
      <strong>{{ filteredAssets.length }} </strong> activos mostrados de
      <strong>{{ assets.length }} </strong> totales
    </div>

    <!-- Botones de exportación – sin tooltip -->
    <div class="mt-4 d-flex flex-wrap gap-2">
      <!-- Exportar JSON -->
      <button @click="exportJSON" class="btn btn-outline-info d-flex align-items-center gap-2">
        <i class="bi bi-filetype-json"></i> Exportar JSON
      </button>

      <!-- Exportar CSV -->
      <button
        @click="exportFullData('csv')"
        class="btn btn-outline-secondary d-flex align-items-center gap-2"
      >
        <i class="bi bi-file-earmark-text"></i> Exportar CSV
      </button>

      <!-- Exportar PDF -->
      <button
        @click="exportFullData('pdf')"
        class="btn btn-outline-primary d-flex align-items-center gap-2"
      >
        <i class="bi bi-file-earmark-pdf"></i> Exportar PDF
      </button>
    </div>

    <!-- Mensaje sobre filtros y exportación -->
    <div class="mt-3 small text-muted">
      <p class="mb-0">
        <i class="bi bi-info-circle me-1"></i>
        <strong>Nota:</strong> Los datos exportados reflejan lo que ves en pantalla.
      </p>
      <p class="mb-0">
        Si hay filtros aplicados, solo se exportarán los resultados visibles. De lo contrario, se
        incluirán todos los activos registrados.
      </p>
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
      isMobile: false,
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

  mounted() {
    this.isMobile = window.innerWidth < 768
    window.addEventListener('resize', () => {
      this.isMobile = window.innerWidth < 768
    })
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
    resetFilters() {
      this.searchQuery = ''
      this.selectedAreaFilter = ''
      this.selectedTypeFilter = ''
      this.personFilter = ''
      this.tagFilter = ''
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
        // Usar filteredAssets (si hay filtros, exporta solo eso)
        const dataToExport = this.filteredAssets.length > 0 ? this.filteredAssets : this.assets
        const blob = new Blob([JSON.stringify(dataToExport, null, 2)], { type: 'application/json' })
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
      const now = new Date()

      // Encabezado
      doc.setFontSize(16)
      doc.text(`Organización: ${this.organization.name}`, 14, 14)
      doc.setFontSize(12)
      doc.text(`Responsable: ${this.organization.responsable || 'No definido'}`, 14, 22)
      doc.text(`Fecha de generación: ${now.toLocaleString()}`, 14, 30)

      let startY = 40

      // Color gris claro para todas las tarjetas
      const bgColor = [248, 249, 250] // #f8f9fa

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

        // Fondo gris claro para todas las tarjetas
        doc.setFillColor(bgColor[0], bgColor[1], bgColor[2])
        doc.rect(12, startY - 5, 180, fields.length * 5 + 10, 'F')

        // Agregar texto
        const content = fields.map((f) => `${f.label}: ${f.value}`).join('\n')
        doc.setTextColor(0, 0, 0)
        doc.setFont(undefined, 'normal')
        doc.text(content, 14, startY)
        doc.setTextColor(0, 0, 0)

        startY += fields.length * 5 + 20

        // Separador entre tarjetas
        doc.setDrawColor(200, 200, 200)
        doc.setLineWidth(0.5)
        doc.line(12, startY - 10, 192, startY - 10)

        // Salto de página
        if (startY > 280) {
          doc.addPage()
          startY = 20
          doc.setFontSize(16)
          doc.text(`Organización: ${this.organization.name}`, 14, 14)
          doc.setFontSize(12)
          doc.text(`Responsable: ${this.organization.responsable || 'No definido'}`, 14, 22)
          doc.text(`Fecha de generación: ${now.toLocaleString()}`, 14, 30)
        }
      }

      // Guardar documento
      doc.save('activos-tarjetas.pdf')
      this.showNotification('Éxito', 'PDF generado con tarjetas estilizadas', 'success')
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
.table-responsive {
  border-radius: 0.5rem;
  overflow: hidden;
}

.table thead th {
  background-color: #f59e0b !important;
  color: white !important;
}

.table tbody tr:nth-child(even) {
  background-color: #fff;
}

.table tbody tr:nth-child(odd) {
  background-color: #f8f9fa;
}

.btn i.bi {
  font-size: 1.1rem;
}

.btn:hover i.bi {
  transform: scale(1.1);
  transition: all 0.2s ease-in-out;
}
.btn-reset-filters {
  font-size: 0.85rem;
}
</style>
