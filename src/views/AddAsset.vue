<template>
  <div class="container mt-5">
    <!-- Encabezado -->
    <div class="d-flex align-items-center mb-4">
      <i :class="`bi ${getIconByType(asset.type)} me-2 fs-3 text-primary`"></i>
      <h2>Agregar Activo</h2>
    </div>

    <form @submit.prevent="saveAsset">
      <!-- Tipo de Activo -->
      <div class="card shadow-sm border mb-4">
        <div class="card-header bg-white fw-bold">
          <i class="bi bi-tag me-2"></i> Selecciona el tipo de activo
        </div>
        <div class="card-body">
          <select v-model="asset.type" class="form-select form-select-lg" required>
            <option value="hardware">Hardware</option>
            <option value="software">Cuentas Online</option>
            <option value="digital">Recursos Digitales</option>
          </select>
          <small class="text-muted mt-2 d-block">
            El tipo no puede cambiarse después de seleccionarlo.
          </small>
        </div>
      </div>

      <!-- Datos comunes -->
      <div class="card shadow-sm border mb-4">
        <div class="card-header bg-white fw-bold">
          <i class="bi bi-card-heading me-2"></i> Información General
        </div>
        <div class="card-body">
          <!-- Nombre del Activo -->
          <div class="mb-3">
            <label for="assetName" class="form-label">Nombre del Activo</label>
            <input
              v-if="asset.type === 'hardware'"
              v-model="asset.name"
              id="assetName"
              type="text"
              class="form-control"
              placeholder="Ej: MacBook Pro, Teléfono Android"
            />
            <input
              v-if="asset.type === 'software'"
              v-model="asset.accountName"
              id="assetName"
              type="text"
              class="form-control"
              placeholder="Ej: Cuenta de Instagram, Correo Electrónico"
            />
            <input
              v-if="asset.type === 'digital'"
              v-model="asset.digitalName"
              id="assetName"
              type="text"
              class="form-control"
              placeholder="Ej: Base de datos, Documento, Multimedia"
            />
          </div>

          <!-- Descripción -->
          <div class="mb-3">
            <label for="description" class="form-label">Descripción</label>
            <textarea
              v-model="asset.description"
              id="description"
              class="form-control"
              rows="2"
            ></textarea>
          </div>

          <!-- Área Responsable -->
          <div class="mb-3">
            <label for="areaSelect" class="form-label">Área Responsable</label>
            <select
              v-model="selectedArea"
              id="areaSelect"
              class="form-select"
              @change="resetResponsable"
            >
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
              <option
                v-for="(persona, idx) in selectedArea.personal"
                :key="idx"
                :value="persona.nombre"
              >
                {{ persona.nombre }}
              </option>
            </select>
          </div>

          <!-- ¿No está en la lista? -->
          <div class="mb-3 form-check">
            <input
              v-model="useCustomPerson"
              type="checkbox"
              class="form-check-input"
              id="customPersonCheck"
            />
            <label class="form-check-label" for="customPersonCheck">
              ¿El responsable no está en la lista?
            </label>
          </div>

          <!-- Ingreso manual de persona -->
          <div class="mb-3" v-if="useCustomPerson">
            <label for="customPersonInput" class="form-label"
              >Ingresa el nombre del responsable</label
            >
            <input
              v-model="asset.person"
              id="customPersonInput"
              type="text"
              class="form-control"
              placeholder="Ej: María González"
            />
            <small class="text-muted mt-1 d-block">
              Este nombre se agregará automáticamente al listado de personal.
            </small>
          </div>

          <!-- Etiquetas -->
          <div class="mb-0">
            <label for="tags" class="form-label">Etiquetas / Categorías</label>
            <input
              v-model="asset.tags"
              id="tags"
              type="text"
              class="form-control"
              placeholder="Ej: Confidencial, Cliente X, Legales"
            />
            <small class="text-muted mt-1 d-block">Separa por comas si usas múltiples.</small>
          </div>
        </div>
      </div>

      <!-- Campos específicos por tipo -->
      <div v-if="asset.type === 'hardware'" class="card shadow-sm border mb-4">
        <div class="card-header bg-white fw-bold">
          <i class="bi bi-laptop me-2"></i> Detalles del Hardware
        </div>
        <div class="card-body">
          <div class="row g-3">
            <div class="col-md-6">
              <div class="mb-3">
                <label for="serialNumber" class="form-label">Número de Serie</label>
                <input
                  v-model="asset.serialNumber"
                  id="serialNumber"
                  type="text"
                  class="form-control"
                />
              </div>
              <div class="mb-3">
                <label for="brandModel" class="form-label">Marca y Modelo</label>
                <input
                  v-model="asset.brandModel"
                  id="brandModel"
                  type="text"
                  class="form-control"
                />
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
            </div>
            <div class="col-md-6">
              <div class="mb-3">
                <label for="acquisitionDate" class="form-label">Fecha de Adquisición</label>
                <input
                  v-model="asset.acquisitionDate"
                  id="acquisitionDate"
                  type="date"
                  class="form-control"
                />
              </div>
              <div class="mb-3">
                <label for="lastMaintenance" class="form-label"
                  >Última fecha de mantenimiento</label
                >
                <input
                  v-model="asset.lastMaintenance"
                  id="lastMaintenance"
                  type="date"
                  class="form-control"
                />
              </div>
              <div class="mb-3">
                <label class="form-label">¿Dispositivo cifrado?</label>
                <select v-model="asset.isEncrypted" class="form-select">
                  <option value="si">Sí</option>
                  <option value="no">No</option>
                </select>
              </div>
              <div class="mb-3">
                <label class="form-label">Nivel de Sensibilidad</label>
                <select v-model="asset.sensitivity" class="form-select" required>
                  <option value="alta">Alta</option>
                  <option value="media">Media</option>
                  <option value="baja">Baja</option>
                </select>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div v-if="asset.type === 'software'" class="card shadow-sm border mb-4">
        <div class="card-header bg-white fw-bold">
          <i class="bi bi-person-badge me-2"></i> Detalles de la Cuenta
        </div>
        <div class="card-body">
          <div class="row g-3">
            <div class="col-md-6">
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
            </div>
            <div class="col-md-6">
              <div class="mb-3">
                <label class="form-label">¿Tiene 2FA?</label>
                <select v-model="asset.hasTwoFactor" class="form-select">
                  <option value="si">Sí</option>
                  <option value="no">No</option>
                </select>
              </div>
              <div class="mb-3 form-check">
                <input
                  v-model="asset.sharedAccount"
                  id="sharedAccountCheck"
                  type="checkbox"
                  class="form-check-input"
                  :true-value="true"
                  :false-value="false"
                />
                <label class="form-check-label" for="sharedAccountCheck"
                  >¿Es una cuenta compartida?</label
                >
              </div>
              <div class="mb-3">
                <label for="renewalDate" class="form-label">Fecha de renovación (opcional)</label>
                <input
                  v-model="asset.renewalDate"
                  id="renewalDate"
                  type="date"
                  class="form-control"
                />
              </div>
              <div class="mb-0">
                <label class="form-label">Nivel de Sensibilidad</label>
                <select v-model="asset.sensitivity" class="form-select" required>
                  <option value="alta">Alta</option>
                  <option value="media">Media</option>
                  <option value="baja">Baja</option>
                </select>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div v-if="asset.type === 'digital'" class="card shadow-sm border mb-4">
        <div class="card-header bg-white fw-bold">
          <i class="bi bi-cloud me-2"></i> Detalles del Recurso Digital
        </div>
        <div class="card-body">
          <div class="row g-3">
            <div class="col-md-6">
              <div class="mb-3">
                <label for="location" class="form-label">Ubicación del Recurso</label>
                <input v-model="asset.location" id="location" type="text" class="form-control" />
              </div>
              <div class="mb-3">
                <label for="digitalType" class="form-label">Tipo de Archivo</label>
                <select v-model="asset.digitalType" id="digitalType" class="form-select">
                  <option value="documento">Documento (PDF, Excel, Word)</option>
                  <option value="multimedia">Multimedia</option>
                  <option value="base_datos">Base de Datos</option>
                  <option value="certificado">Certificado / Llave</option>
                  <option value="carpeta_compartida">Carpeta Compartida</option>
                  <option value="repositorio">Repositorio (Git, etc.)</option>
                  <option value="otro">Otro</option>
                </select>
              </div>
            </div>
            <div class="col-md-6">
              <div class="mb-3">
                <label for="creationDate" class="form-label">Fecha de creación</label>
                <input
                  v-model="asset.creationDate"
                  id="creationDate"
                  type="date"
                  class="form-control"
                />
              </div>
              <div class="mb-0">
                <label class="form-label">¿Acceso restringido?</label>
                <select v-model="asset.restrictedAccess" class="form-select">
                  <option value="si">Sí</option>
                  <option value="no">No</option>
                </select>
              </div>
              <div class="mb-0 mt-3">
                <label class="form-label">Nivel de Sensibilidad</label>
                <select v-model="asset.sensitivity" class="form-select" required>
                  <option value="alta">Alta</option>
                  <option value="media">Media</option>
                  <option value="baja">Baja</option>
                </select>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Botones -->
      <div class="d-flex gap-3 flex-wrap pt-3">
        <button
          :disabled="isSaving"
          type="submit"
          class="btn btn-success d-flex align-items-center gap-2"
        >
          <i class="bi bi-save"></i> {{ isSaving ? 'Guardando...' : 'Guardar Activo' }}
        </button>
        <router-link to="/list" class="btn btn-outline-secondary d-flex align-items-center gap-2">
          <i class="bi bi-arrow-left"></i> Cancelar
        </router-link>
      </div>
    </form>

    <!-- Notificación -->
    <MyNotification
      v-if="notification.show"
      :title="notification.title"
      :message="notification.message"
      :type="notification.type"
    />
  </div>
  <!-- Espaciado al final -->
  <div class="mb-5"></div>
</template>

<script>
import MyNotification from '@/components/MyNotification.vue'

export default {
  components: {
    MyNotification,
  },
  data() {
    const orgData = localStorage.getItem('organization')
    const organization = orgData ? JSON.parse(orgData) : { areas: [] }

    return {
      asset: {
        id: null,
        type: 'hardware',
        // Hardware
        name: '',
        description: '',
        serialNumber: '',
        brandModel: '',
        physicalLocation: '',
        deviceStatus: 'en_uso',
        lastMaintenance: '',
        sensitivity: 'baja',
        // Cuentas Online
        accountName: '',
        username: '',
        hasTwoFactor: 'no',
        url: '',
        accountType: 'correo',
        renewalDate: '',
        sharedAccount: false,
        // Recursos Digitales
        digitalName: '',
        digitalType: 'documento',
        location: '',
        creationDate: '',
        restrictedAccess: 'no',
        tags: '',
      },
      organization,
      selectedArea: organization.areas[0] || {},
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
    areas() {
      return this.organization.areas || []
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

      let assets = JSON.parse(localStorage.getItem('assets') || '[]')
      this.asset.id = Date.now()
      this.asset.area = {
        name: this.selectedArea.name,
        responsable: this.selectedArea.responsable,
      }

      assets.push({ ...this.asset })
      localStorage.setItem('assets', JSON.stringify(assets))

      this.showNotification('Éxito', 'Activo guardado correctamente', 'success')

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
