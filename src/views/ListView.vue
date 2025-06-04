<template>
  <div class="container mt-5">
    <h2>Lista de Activos</h2>

    <!-- Filtros -->
    <div class="row mb-4">
      <div class="col-md-6">
        <input
          v-model="searchQuery"
          type="text"
          class="form-control"
          placeholder="Buscar por nombre..."
        />
      </div>
      <div class="col-md-6">
        <select v-model="selectedAreaFilter" class="form-select">
          <option value="">Todas las áreas</option>
          <option v-for="(area, idx) in areas" :key="idx" :value="area.name">
            {{ area.name }}
          </option>
        </select>
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
    <div v-else>
      <table class="table table-striped table-bordered">
        <thead class="table-dark">
          <tr>
            <th>Tipo</th>
            <th>Nombre</th>
            <th>Serie</th>
            <th>Usuario</th>
            <th>Ubicación</th>
            <th>Sensibilidad</th>
            <th>Área</th>
            <th>Responsable</th>
            <th>Etiquetas</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="asset in filteredAssets" :key="asset.id">
            <!-- Tipo -->
            <td>{{ formatAssetType(asset.type) }}</td>

            <!-- Nombre según tipo -->
            <td>{{ getAssetName(asset) }}</td>

            <!-- Serie (solo Hardware) -->
            <td>{{ asset.type === 'hardware' ? asset.serialNumber || '-' : '-' }}</td>

            <!-- Usuario (solo Software) -->
            <td>{{ asset.type === 'software' ? asset.username || '-' : '-' }}</td>

            <!-- Ubicación (solo Recurso Digital) -->
            <td>{{ asset.type === 'digital' ? asset.location || '-' : '-' }}</td>

            <!-- Sensibilidad -->
            <td>
              <span :class="`badge bg-${getSensitivityColor(asset.sensitivity)}`">
                {{ formatSensitivity(asset.sensitivity) }}
              </span>
            </td>

            <!-- Área -->
            <td>{{ asset.area?.name || 'Sin área' }}</td>

            <!-- Responsable -->
            <td>{{ asset.person || 'Sin responsable' }}</td>

            <!-- Etiquetas -->
            <td>{{ asset.tags || '-' }}</td>

            <!-- Acciones -->
            <td>
              <router-link :to="`/edit/${asset.id}`" class="btn btn-sm btn-outline-warning me-1"
                >Editar</router-link
              >
              <router-link :to="`/detail/${asset.id}`" class="btn btn-sm btn-outline-primary me-1"
                >Ver Detalles</router-link
              >
              <button @click="deleteAsset(asset.id)" class="btn btn-sm btn-danger">Eliminar</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Botones exportación -->
    <div class="mt-4">
      <button @click="exportJSON" class="btn btn-info me-2">Exportar JSON</button>
      <button @click="exportPDF" class="btn btn-primary me-2">Exportar PDF</button>
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
import autoTable from 'jspdf-autotable'

export default {
  components: {
    MyNotification,
  },
  data() {
    return {
      assets: JSON.parse(localStorage.getItem('assets') || '[]'),
      searchQuery: '',
      selectedAreaFilter: '',
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
      return orgData ? JSON.parse(orgData) : { areas: [] }
    },
    areas() {
      return this.organization.areas || []
    },
    filteredAssets() {
      let result = [...this.assets]

      // Filtrar por nombre
      if (this.searchQuery) {
        const query = this.searchQuery.toLowerCase()
        result = result.filter((asset) => {
          const name = this.getAssetName(asset).toLowerCase()
          return name.includes(query)
        })
      }

      // Filtrar por área
      if (this.selectedAreaFilter) {
        result = result.filter((asset) => asset.area?.name === this.selectedAreaFilter)
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
    deleteAsset(id) {
      const confirmDelete = confirm('¿Estás seguro de eliminar este activo?')
      if (!confirmDelete) return

      this.assets = this.assets.filter((asset) => asset.id !== id)
      localStorage.setItem('assets', JSON.stringify(this.assets))
      this.showNotification('Éxito', 'Activo eliminado correctamente', 'success')
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
    exportPDF() {
      try {
        const doc = new jsPDF()
        doc.setFontSize(16)
        doc.text('Lista de Activos', 14, 16)

        const tableColumn = [
          'Tipo',
          'Nombre',
          'Serie/Usuario/Loc.',
          'Sensibilidad',
          'Área',
          'Responsable',
          'Etiquetas',
        ]

        const tableRows = this.filteredAssets.map((asset) => {
          let serieUsuarioLocation = '-'
          if (asset.type === 'hardware') {
            serieUsuarioLocation = asset.serialNumber || '-'
          } else if (asset.type === 'software') {
            serieUsuarioLocation = asset.username || '-'
          } else if (asset.type === 'digital') {
            serieUsuarioLocation = asset.location || '-'
          }

          return [
            this.formatAssetType(asset.type),
            this.getAssetName(asset),
            serieUsuarioLocation,
            this.formatSensitivity(asset.sensitivity),
            asset.area?.name || '-',
            asset.person || '-',
            asset.tags || '-',
          ]
        })

        autoTable(doc, {
          head: [tableColumn],
          body: tableRows,
          startY: 30,
        })

        doc.save('activos.pdf')
        this.showNotification('Éxito', 'Documento PDF generado correctamente', 'success')
      } catch (error) {
        this.showNotification('Error', 'Ocurrió un error al generar el PDF', 'danger')
        console.error(error)
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
