<template>
  <div class="container mt-5">
    <!-- Título -->
    <h2>Editar Activo</h2>

    <!-- Tipo de Activo -->
    <div class="mb-3">
      <label for="assetType" class="form-label">Tipo de Activo</label>
      <select v-model="asset.type" id="assetType" class="form-select" disabled>
        <option value="hardware">Hardware</option>
        <option value="software">Cuenta Online</option>
        <option value="digital">Recurso Digital</option>
      </select>
      <small class="text-muted">El tipo no puede modificarse.</small>
    </div>

    <!-- Nombre del Activo -->
    <div class="mb-3">
      <label for="assetName" class="form-label">Nombre del Activo</label>
      <input
        v-if="asset.type === 'hardware'"
        v-model="asset.name"
        id="assetName"
        type="text"
        class="form-control"
      />
      <input
        v-if="asset.type === 'software'"
        v-model="asset.accountName"
        id="assetName"
        type="text"
        class="form-control"
      />
      <input
        v-if="asset.type === 'digital'"
        v-model="asset.digitalName"
        id="assetName"
        type="text"
        class="form-control"
      />
    </div>

    <!-- Descripción -->
    <div class="mb-3">
      <label for="description" class="form-label">Descripción</label>
      <textarea v-model="asset.description" id="description" class="form-control"></textarea>
    </div>

    <!-- Selección de Área -->
    <div class="mb-3">
      <label for="areaSelect" class="form-label">Área Responsable</label>
      <select v-model="selectedArea" id="areaSelect" class="form-select" @change="resetResponsable">
        <option v-for="(area, index) in areas" :key="index" :value="area">
          {{ area.name }}
        </option>
      </select>
    </div>

    <!-- Persona Responsable (seleccionable) -->
    <div
      class="mb-3"
      v-if="
        selectedArea &&
        selectedArea.personal &&
        selectedArea.personal.length > 0 &&
        !useCustomPerson
      "
    >
      <label class="form-label">Persona Responsable</label>
      <select v-model="asset.person" class="form-select mb-2">
        <option v-for="(persona, idx) in selectedArea.personal" :key="idx" :value="persona.nombre">
          {{ persona.nombre }}
        </option>
      </select>
    </div>

    <!-- Checkbox - ¿No está en la lista? -->
    <div class="mb-3 form-check">
      <input
        v-model="useCustomPerson"
        type="checkbox"
        class="form-check-input"
        id="customPersonCheck"
      />
      <label class="form-check-label" for="customPersonCheck">¿No está en la lista?</label>
    </div>

    <!-- Ingreso manual de persona -->
    <div class="mb-3" v-if="useCustomPerson">
      <label for="customPersonInput" class="form-label">Ingresa el nombre del responsable</label>
      <input
        v-model="asset.person"
        id="customPersonInput"
        type="text"
        class="form-control"
        placeholder="Ej: María González"
      />
      <small class="text-muted">
        Este nombre se agregará automáticamente al listado de personal.
      </small>
    </div>

    <!-- Campos específicos por tipo -->
    <div v-if="asset.type === 'hardware'">
      <div class="mb-3">
        <label for="serialNumber" class="form-label">Número de Serie</label>
        <input v-model="asset.serialNumber" id="serialNumber" type="text" class="form-control" />
      </div>
      <div class="mb-3">
        <label for="brandModel" class="form-label">Marca y Modelo</label>
        <input v-model="asset.brandModel" id="brandModel" type="text" class="form-control" />
      </div>
      <div class="mb-3">
        <label for="physicalLocation" class="form-label">Ubicación Física</label>
        <input
          v-model="asset.physicalLocation"
          id="physicalLocation"
          type="text"
          class="form-control"
        />
      </div>
      <div class="mb-3">
        <label for="deviceStatus" class="form-label">Estado del Dispositivo</label>
        <select v-model="asset.deviceStatus" id="deviceStatus" class="form-select">
          <option value="nuevo">Nuevo</option>
          <option value="en_uso">En Uso</option>
          <option value="en_reparacion">En Reparación</option>
          <option value="inactivo">Inactivo</option>
        </select>
      </div>
      <div class="mb-3">
        <label for="lastMaintenance" class="form-label">Última fecha de mantenimiento</label>
        <input
          v-model="asset.lastMaintenance"
          id="lastMaintenance"
          type="date"
          class="form-control"
        />
      </div>
      <div class="mb-3">
        <label for="isEncrypted" class="form-label">Dispositivo cifrado</label>
        <select v-model="asset.isEncrypted" id="isEncrypted" class="form-select">
          <option value="si">Sí</option>
          <option value="no">No</option>
        </select>
      </div>
    </div>

    <div v-if="asset.type === 'software'">
      <div class="mb-3">
        <label for="username" class="form-label">Usuario (Login)</label>
        <input v-model="asset.username" id="username" type="text" class="form-control" />
      </div>
      <div class="mb-3">
        <label for="url" class="form-label">URL del Portal</label>
        <input v-model="asset.url" id="url" type="url" class="form-control" />
      </div>
      <div class="mb-3">
        <label for="accountType" class="form-label">Tipo de Cuenta</label>
        <select v-model="asset.accountType" id="accountType" class="form-select">
          <option value="correo">Correo Electrónico</option>
          <option value="red_social">Red Social</option>
          <option value="portal_gubernamental">Portal Gubernamental</option>
          <option value="plataforma_educativa">Plataforma Educativa</option>
          <option value="plataforma_empresa">Plataforma de la Empresa</option>
          <option value="servicio_cloud">Servicio Cloud</option>
          <option value="otro">Otro</option>
        </select>
      </div>
      <div class="mb-3 form-check">
        <input
          v-model="asset.sharedAccount"
          id="sharedAccountCheck"
          type="checkbox"
          class="form-check-input"
        />
        <label class="form-check-label" for="sharedAccountCheck">¿Es una cuenta compartida?</label>
      </div>
      <div class="mb-3">
        <label for="renewalDate" class="form-label">Fecha de renovación (opcional)</label>
        <input v-model="asset.renewalDate" id="renewalDate" type="date" class="form-control" />
      </div>
    </div>

    <div v-if="asset.type === 'digital'">
      <div class="mb-3">
        <label for="location" class="form-label">Ubicación</label>
        <input v-model="asset.location" id="location" type="text" class="form-control" />
      </div>
      <div class="mb-3">
        <label for="digitalType" class="form-label">Tipo de Archivo</label>
        <select v-model="asset.digitalType" id="digitalType" class="form-select">
          <option value="documento">Documento (PDF, Word, Excel)</option>
          <option value="multimedia">Multimedia</option>
          <option value="base_datos">Base de Datos</option>
          <option value="certificado">Certificado / Llave</option>
          <option value="carpeta_compartida">Carpeta Compartida</option>
          <option value="repositorio">Repositorio (Git, etc.)</option>
          <option value="otro">Otro</option>
        </select>
      </div>
      <div class="mb-3">
        <label for="creationDate" class="form-label">Fecha de creación</label>
        <input v-model="asset.creationDate" id="creationDate" type="date" class="form-control" />
      </div>
      <div class="mb-3">
        <label class="form-label">¿Acceso restringido?</label>
        <select v-model="asset.restrictedAccess" class="form-select">
          <option value="si">Sí</option>
          <option value="no">No</option>
        </select>
      </div>
    </div>

    <!-- Nivel de Sensibilidad -->
    <div class="mb-3">
      <label for="sensitivityLevel" class="form-label">Nivel de Sensibilidad</label>
      <select v-model="asset.sensitivity" id="sensitivityLevel" class="form-select" required>
        <option value="alta">Alta</option>
        <option value="media">Media</option>
        <option value="baja">Baja</option>
      </select>
    </div>

    <!-- Etiquetas / Tags -->
    <div class="mb-3">
      <label for="tags" class="form-label">Etiquetas / Categorías</label>
      <input v-model="asset.tags" id="tags" type="text" class="form-control" />
      <small class="text-muted">Separa por comas si usas múltiples etiquetas</small>
    </div>

    <!-- Botones -->
    <button :disabled="isSaving" @click="saveAsset" class="btn btn-success me-2">
      {{ isSaving ? 'Guardando...' : 'Guardar Cambios' }}
    </button>
    <button @click="deleteAsset(asset.id)" type="button" class="btn btn-danger me-2">
      Eliminar Activo
    </button>
    <router-link to="/list" class="btn btn-secondary">Cancelar</router-link>

    <!-- Notificación -->
    <MyNotification
      v-if="notification.show"
      :title="notification.title"
      :message="notification.message"
      :type="notification.type"
    />

    <!-- Espacio adicional al final -->
    <div class="mb-5"></div>
  </div>
</template>

<script>
import MyNotification from '@/components/MyNotification.vue'

export default {
  components: {
    MyNotification,
  },
  data() {
    const assets = JSON.parse(localStorage.getItem('assets') || '[]')
    const assetId = parseInt(this.$route.params.id)
    const asset = assets.find((a) => a.id === assetId) || {}
    return {
      asset,
      useCustomPerson: false,
      notification: {
        show: false,
        title: '',
        message: '',
        type: 'success',
      },
      isSaving: false,
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
    selectedArea() {
      if (!this.asset.area?.name) return this.organization.areas[0] || {}
      return (
        this.organization.areas.find((area) => area.name === this.asset.area.name) ||
        this.organization.areas[0] ||
        {}
      )
    },
  },
  methods: {
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
    resetResponsable() {
      this.useCustomPerson = false
      if (
        this.selectedArea &&
        this.selectedArea.personal &&
        this.selectedArea.personal.length > 0
      ) {
        this.asset.person = this.selectedArea.personal[0].nombre
      } else {
        this.asset.person = ''
      }
    },
    saveAsset() {
      this.isSaving = true
      // Si marcó "no está en la lista", agregarla al área seleccionada
      if (this.useCustomPerson && this.asset.person.trim()) {
        const exists = this.selectedArea.personal.some((p) => p.nombre === this.asset.person.trim())
        if (!exists) {
          this.selectedArea.personal.push({ nombre: this.asset.person.trim() })
        }

        const updatedOrganization = {
          ...this.organization,
          areas: this.organization.areas.map((area) => {
            if (area.name === this.selectedArea.name) {
              return this.selectedArea
            }
            return area
          }),
        }
        localStorage.setItem('organization', JSON.stringify(updatedOrganization))
      }

      // Guardar activo
      let assets = JSON.parse(localStorage.getItem('assets') || '[]')
      const index = assets.findIndex((a) => a.id === this.asset.id)

      if (index !== -1) {
        assets[index] = { ...this.asset }
        localStorage.setItem('assets', JSON.stringify(assets))
        this.showNotification('Éxito', 'Activo guardado correctamente', 'success')
        setTimeout(() => {
          this.$router.push('/list')
        }, 1000)
      } else {
        this.showNotification('Error', 'No se pudo encontrar el activo para guardar', 'danger')
        this.isSaving = false
      }
    },
    deleteAsset(id) {
      const confirmDelete = confirm('¿Estás seguro de eliminar este activo?')
      if (!confirmDelete) return
      let assets = JSON.parse(localStorage.getItem('assets') || '[]')
      assets = assets.filter((asset) => asset.id !== id)
      localStorage.setItem('assets', JSON.stringify(assets))
      this.showNotification('Éxito', 'Activo eliminado correctamente', 'success')
      setTimeout(() => {
        this.$router.push('/list')
      }, 1000)
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
.card-header i.bi {
  font-size: 1.2rem;
}

.form-label i.bi {
  font-size: 1rem;
}

.btn i.bi {
  font-size: 1rem;
}

.card-body input,
.card-body select,
.card-body textarea {
  margin-bottom: 0.5rem;
}
</style>
