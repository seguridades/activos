<template>
  <div class="container mt-5">
    <h2>Agregar Activo</h2>
    <form @submit.prevent="saveAsset">
      <!-- Tipo de Activo -->
      <div class="mb-3">
        <label for="assetType" class="form-label">Tipo de Activo</label>
        <select v-model="asset.type" id="assetType" class="form-select" required>
          <option value="hardware">Hardware</option>
          <option value="software">Cuentas Online</option>
          <option value="digital">Recursos Digitales</option>
        </select>
      </div>

      <!-- Campos para Hardware -->
      <div v-if="asset.type === 'hardware'">
        <!-- Nombre del Activo -->
        <div class="mb-3">
          <label for="assetName" class="form-label">Nombre del Dispositivo</label>
          <input v-model="asset.name" type="text" id="assetName" class="form-control" required />
        </div>

        <!-- Descripción -->
        <div class="mb-3">
          <label for="assetDescription" class="form-label">Descripción del Dispositivo</label>
          <textarea
            v-model="asset.description"
            id="assetDescription"
            class="form-control"
          ></textarea>
        </div>

        <!-- Número de Serie -->
        <div class="mb-3">
          <label for="serialNumber" class="form-label">Número de Serie / ID Único</label>
          <input v-model="asset.serialNumber" type="text" id="serialNumber" class="form-control" />
        </div>

        <!-- Etiqueta interna -->
        <div class="mb-3">
          <label for="internalTag" class="form-label">Etiqueta Interna / Código</label>
          <input
            v-model="asset.internalTag"
            type="text"
            id="internalTag"
            class="form-control"
            placeholder="Ej: ACT-0042"
          />
        </div>

        <!-- Marca y modelo -->
        <div class="mb-3">
          <label for="brandModel" class="form-label">Marca y Modelo</label>
          <input
            v-model="asset.brandModel"
            type="text"
            id="brandModel"
            class="form-control"
            placeholder="Ej: Dell Latitude 7490"
          />
        </div>

        <!-- Fecha de adquisición -->
        <div class="mb-3">
          <label for="acquisitionDate" class="form-label">Fecha de Adquisición</label>
          <input
            v-model="asset.acquisitionDate"
            type="date"
            id="acquisitionDate"
            class="form-control"
          />
        </div>

        <!-- ¿Está cifrado? -->
        <div class="mb-3">
          <label for="isEncrypted" class="form-label">Dispositivo cifrado</label>
          <select v-model="asset.isEncrypted" id="isEncrypted" class="form-select">
            <option value="si">Sí</option>
            <option value="no">No</option>
          </select>
        </div>

        <!-- Ubicación física -->
        <div class="mb-3">
          <label for="physicalLocation" class="form-label">Ubicación Física</label>
          <input
            v-model="asset.physicalLocation"
            type="text"
            id="physicalLocation"
            class="form-control"
            placeholder="Ej: Oficina Principal - Escritorio de Juan"
          />
        </div>

        <!-- Estado del dispositivo -->
        <div class="mb-3">
          <label for="deviceStatus" class="form-label">Estado del Dispositivo</label>
          <select v-model="asset.deviceStatus" id="deviceStatus" class="form-select">
            <option value="nuevo">Nuevo</option>
            <option value="en_uso">En Uso</option>
            <option value="en_reparacion">En Reparación</option>
            <option value="inactivo">Inactivo</option>
          </select>
        </div>

        <!-- Fecha último mantenimiento -->
        <div class="mb-3">
          <label for="lastMaintenance" class="form-label">Última fecha de mantenimiento</label>
          <input
            v-model="asset.lastMaintenance"
            type="date"
            id="lastMaintenance"
            class="form-control"
          />
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
      </div>

      <!-- Campos para Cuentas Online -->
      <div v-if="asset.type === 'software'">
        <!-- Nombre de la cuenta -->
        <div class="mb-3">
          <label for="accountName" class="form-label">Nombre de la Cuenta</label>
          <input
            v-model="asset.accountName"
            type="text"
            id="accountName"
            class="form-control"
            required
          />
        </div>

        <!-- Descripción -->
        <div class="mb-3">
          <label for="accountDescription" class="form-label">Descripción</label>
          <textarea
            v-model="asset.description"
            id="accountDescription"
            class="form-control"
          ></textarea>
        </div>

        <!-- Usuario -->
        <div class="mb-3">
          <label for="username" class="form-label">Usuario (Login)</label>
          <input v-model="asset.username" type="text" id="username" class="form-control" />
        </div>

        <!-- ¿Tiene 2FA? -->
        <div class="mb-3">
          <label class="form-label">¿La cuenta tiene 2FA?</label>
          <select v-model="asset.hasTwoFactor" class="form-select">
            <option value="si">Sí</option>
            <option value="no">No</option>
          </select>
        </div>

        <!-- Tipo de Cuenta -->
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

        <!-- URL -->
        <div class="mb-3">
          <label for="url" class="form-label">URL del Portal / Sitio</label>
          <input
            v-model="asset.url"
            type="url"
            id="url"
            class="form-control"
            placeholder="https://ejemplo.com"
          />
        </div>

        <!-- ¿Es cuenta compartida? -->
        <div class="mb-3 form-check">
          <input
            v-model="asset.sharedAccount"
            type="checkbox"
            class="form-check-input"
            id="sharedAccountCheck"
          />
          <label class="form-check-label" for="sharedAccountCheck"
            >¿Es una cuenta compartida?</label
          >
        </div>

        <!-- Fecha de renovación -->
        <div class="mb-3">
          <label for="renewalDate" class="form-label">Fecha de Renovación (opcional)</label>
          <input v-model="asset.renewalDate" type="date" id="renewalDate" class="form-control" />
          <small class="text-muted">Para suscripciones, dominios, licencias, etc.</small>
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
      </div>

      <!-- Campos para Recursos Digitales -->
      <div v-if="asset.type === 'digital'">
        <!-- Nombre del recurso -->
        <div class="mb-3">
          <label for="digitalName" class="form-label">Nombre del Recurso Digital</label>
          <input
            v-model="asset.digitalName"
            type="text"
            id="digitalName"
            class="form-control"
            required
          />
        </div>

        <!-- Descripción -->
        <div class="mb-3">
          <label for="digitalDescription" class="form-label">Descripción del Recurso</label>
          <textarea
            v-model="asset.description"
            id="digitalDescription"
            class="form-control"
          ></textarea>
        </div>

        <!-- Tipo de archivo -->
        <div class="mb-3">
          <label for="digitalType" class="form-label">Tipo de Archivo</label>
          <select v-model="asset.digitalType" id="digitalType" class="form-select">
            <option value="documento">Documento (PDF, Excel, Word, etc.)</option>
            <!-- ✅ Opción actualizada -->
            <option value="multimedia">Multimedia</option>
            <option value="base_datos">Base de Datos</option>
            <option value="certificado">Certificado / Llave</option>
            <option value="carpeta_compartida">Carpeta Compartida</option>
            <option value="repositorio">Repositorio (Git, etc.)</option>
            <option value="otro">Otro</option>
          </select>
        </div>

        <!-- Ubicación -->
        <div class="mb-3">
          <label for="location" class="form-label">Ubicación del Recurso</label>
          <input
            v-model="asset.location"
            type="text"
            id="location"
            class="form-control"
            placeholder="Ej: Servidor interno / Google Drive"
          />
        </div>

        <!-- Fecha de creación -->
        <div class="mb-3">
          <label for="creationDate" class="form-label"
            >Fecha de Creación / Última Actualización</label
          >
          <input v-model="asset.creationDate" type="date" id="creationDate" class="form-control" />
        </div>

        <!-- ¿Acceso restringido? -->
        <div class="mb-3">
          <label class="form-label">¿Tiene acceso restringido?</label>
          <select v-model="asset.restrictedAccess" class="form-select">
            <option value="si">Sí</option>
            <option value="no">No</option>
          </select>
        </div>

        <!-- Nivel de sensibilidad -->
        <div class="mb-3">
          <label for="sensitivityLevel" class="form-label">Nivel de Sensibilidad</label>
          <select v-model="asset.sensitivity" id="sensitivityLevel" class="form-select" required>
            <option value="alta">Alta</option>
            <option value="media">Media</option>
            <option value="baja">Baja</option>
          </select>
        </div>
      </div>

      <!-- Campo común para todas las vistas: Etiquetas / Categorías -->
      <div v-if="asset.type !== ''" class="mb-3">
        <label for="tags" class="form-label">Etiquetas / Categorías</label>
        <input
          v-model="asset.tags"
          type="text"
          id="tags"
          class="form-control"
          placeholder="Ej: Confidencial, Cliente X, Legales"
        />
        <small class="text-muted">Separa por comas si usas múltiples etiquetas</small>
      </div>

      <!-- Selección de Área -->
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

      <!-- Selector de Persona -->
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

      <!-- Checkbox "¿No está en la lista?" -->
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
        <small class="text-muted"
          >Este nombre se agregará automáticamente al listado de personal.</small
        >
      </div>

      <!-- Botón guardar -->
      <button type="submit" class="btn btn-success me-2">Guardar Activo</button>
      <router-link to="/list" class="btn btn-primary">Ver Lista de Activos</router-link>
    </form>

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
        internalTag: '',
        brandModel: '',
        acquisitionDate: '',
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
        // Común a todos los tipos
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
    }
  },
  computed: {
    areas() {
      return this.organization.areas || []
    },
  },
  methods: {
    resetResponsable() {
      this.useCustomPerson = false
      if (this.selectedArea.personal && this.selectedArea.personal.length > 0) {
        this.asset.person = this.selectedArea.personal[0].nombre
      } else {
        this.asset.person = ''
      }
    },
    saveAsset() {
      if (this.useCustomPerson && this.asset.person.trim()) {
        const exists = this.selectedArea.personal.some((p) => p.nombre === this.asset.person.trim())
        if (!exists) {
          this.selectedArea.personal.push({ nombre: this.asset.person.trim() })
          this.saveOrganization()
        }
      }

      let assets = JSON.parse(localStorage.getItem('assets') || '[]')

      this.asset.id = Date.now()
      this.asset.area = { name: this.selectedArea.name, responsable: this.selectedArea.responsable }
      assets.push({ ...this.asset })

      localStorage.setItem('assets', JSON.stringify(assets))

      this.showNotification('Éxito', 'Activo guardado correctamente', 'success')
      this.resetForm()
    },
    saveOrganization() {
      localStorage.setItem('organization', JSON.stringify(this.organization))
    },
    resetForm() {
      this.asset = {
        id: null,
        type: 'hardware',
        name: '',
        description: '',
        serialNumber: '',
        internalTag: '',
        brandModel: '',
        acquisitionDate: '',
        physicalLocation: '',
        deviceStatus: 'en_uso',
        lastMaintenance: '',
        sensitivity: 'baja',
        accountName: '',
        username: '',
        hasTwoFactor: 'no',
        url: '',
        accountType: 'correo',
        renewalDate: '',
        sharedAccount: false,
        digitalName: '',
        digitalType: 'documento',
        location: '',
        creationDate: '',
        restrictedAccess: 'no',
        tags: '',
      }
      this.useCustomPerson = false
      this.selectedArea = this.organization.areas[0] || {}
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
