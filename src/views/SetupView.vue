<template>
  <div class="container mt-5">
    <h2>⚙️ Configuración</h2>

    <!-- Datos de la organización -->
    <section class="mb-4">
      <h4 class="mb-3">Organización</h4>
      <div class="mb-3">
        <label for="orgName" class="form-label">Nombre de la Organización</label>
        <input v-model="organization.name" id="orgName" type="text" class="form-control" />
      </div>
      <div class="mb-3">
        <label for="orgResponsible" class="form-label"
          >Responsable del seguimiento de activos</label
        >
        <input
          v-model="organization.responsable"
          id="orgResponsible"
          type="text"
          class="form-control"
        />
      </div>
    </section>

    <!-- Gestión de áreas -->
    <section class="mb-5">
      <h4 class="mb-3">Áreas</h4>

      <div
        v-for="(area, index) in organization.areas"
        :key="index"
        class="card shadow-sm border mb-3"
      >
        <div class="card-body">
          <div class="row g-3">
            <div class="col-md-5">
              <input
                v-model="area.name"
                type="text"
                class="form-control"
                placeholder="Nombre del área"
              />
            </div>
            <div class="col-md-5">
              <input
                v-model="area.responsable"
                type="text"
                class="form-control"
                placeholder="Responsable del área"
              />
            </div>
            <div class="col-md-2 d-flex align-items-center">
              <button @click="removeArea(index)" class="btn btn-danger btn-sm w-100">
                Eliminar
              </button>
            </div>
          </div>

          <!-- Gestión de personal -->
          <div class="mt-3">
            <button @click="addPersonToArea(index)" class="btn btn-sm btn-outline-secondary mb-2">
              + Añadir Persona
            </button>
          </div>

          <div v-if="area.personal && area.personal.length > 0" class="mt-2">
            <h6 class="small fw-bold">Personal del área:</h6>
            <div
              v-for="(persona, pIndex) in area.personal"
              :key="pIndex"
              class="input-group input-group-sm mb-2"
            >
              <input
                v-model="area.personal[pIndex].nombre"
                type="text"
                class="form-control form-control-sm"
                placeholder="Nombre o apodo"
              />
              <button @click="removePersonFromArea(index, pIndex)" class="btn btn-outline-danger">
                <i class="bi bi-trash"></i>
              </button>
            </div>
          </div>
        </div>
      </div>

      <button @click="addNewArea" class="btn btn-outline-primary btn-sm mt-3">+ Añadir Área</button>
    </section>

    <!-- Guardar / Exportar -->
    <section class="mb-5">
      <button @click="saveOrganization" class="btn btn-success me-2">Guardar Configuración</button>
      <button @click="exportAllData" class="btn btn-primary me-2">📤 Exportar Todo</button>
      <router-link to="/list" class="btn btn-secondary">Volver al Listado</router-link>
    </section>

    <!-- Importar backup -->
    <section class="mb-5">
      <h4 class="mb-3">📥 Importar Datos desde Backup (.json)</h4>
      <div class="alert alert-warning small" role="alert">
        <strong>Advertencia:</strong> Esto reemplazará los activos actuales. Asegúrate de tener un
        respaldo si es necesario.
      </div>

      <label for="jsonFile" class="form-label"
        >Selecciona un archivo .json generado por esta herramienta</label
      >
      <input type="file" id="jsonFile" accept=".json" @change="importJSON" class="form-control" />

      <small class="text-muted d-block mt-2">
        El archivo debe contener una lista de activos en formato válido
      </small>
    </section>

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

export default {
  components: {
    MyNotification,
  },
  data() {
    return {
      organization: {
        name: '',
        responsable: '',
        areas: [],
      },
      notification: {
        show: false,
        title: '',
        message: '',
        type: 'success',
      },
    }
  },
  created() {
    const orgData = localStorage.getItem('organization')
    if (orgData) {
      const parsedOrg = JSON.parse(orgData)
      // Asegura que todas las áreas tengan personal como array
      parsedOrg.areas = parsedOrg.areas.map((area) => ({
        ...area,
        personal: Array.isArray(area.personal) ? area.personal : [],
      }))
      this.organization = parsedOrg
    } else {
      this.organization.areas = [
        {
          name: '',
          responsable: '',
          personal: [],
        },
      ]
    }
  },
  methods: {
    addNewArea() {
      this.organization.areas.push({
        name: '',
        responsable: '',
        personal: [],
      })
    },
    removeArea(index) {
      this.organization.areas = this.organization.areas.filter((_, i) => i !== index)
    },
    addPersonToArea(areaIndex) {
      const area = this.organization.areas[areaIndex]
      if (!area.personal) {
        area.personal = []
      }
      area.personal.push({ nombre: '' })
    },
    removePersonFromArea(areaIndex, personIndex) {
      this.organization.areas[areaIndex].personal.splice(personIndex, 1)
    },
    saveOrganization() {
      // Asegura que cada área tenga personal como array
      this.organization.areas = this.organization.areas.map((area) => {
        return {
          ...area,
          personal: Array.isArray(area.personal) ? area.personal : [],
        }
      })

      localStorage.setItem('organization', JSON.stringify(this.organization))
      this.showNotification('Éxito', 'Organización y personal guardados correctamente', 'success')
    },
    exportAllData() {
      const organization = localStorage.getItem('organization')
      const assets = localStorage.getItem('assets')

      if (!organization || !assets) {
        this.showNotification('Error', 'Faltan datos (organización o activos)', 'danger')
        return
      }

      try {
        const fullData = {
          organization: JSON.parse(organization),
          assets: JSON.parse(assets),
        }

        const blob = new Blob([JSON.stringify(fullData, null, 2)], { type: 'application/json' })
        saveAs(blob, 'backup-mis-activos-digitales.json')
        this.showNotification('Éxito', 'Datos completos exportados correctamente', 'success')
      } catch (error) {
        this.showNotification('Error', 'No se pudieron preparar los datos para exportar', 'danger')
        console.error(error)
      }
    },
    importJSON(event) {
      const file = event.target.files[0]
      if (!file) return

      const reader = new FileReader()
      reader.onload = (e) => {
        try {
          const content = e.target.result
          const parsed = JSON.parse(content)

          // Validar que tenga 'organization' y 'assets'
          if (!parsed.organization || !parsed.assets) {
            throw new Error('Faltan campos "organization" o "assets" en el archivo')
          }

          // Guardar datos en localStorage
          localStorage.setItem('organization', JSON.stringify(parsed.organization))
          localStorage.setItem('assets', JSON.stringify(parsed.assets))

          this.showNotification('Éxito', 'Datos importados correctamente', 'success')
          setTimeout(() => {
            this.$router.push('/list')
          }, 1500)
        } catch (error) {
          this.showNotification('Error', 'El archivo no tiene un formato válido', 'danger')
          console.error(error)
        }
      }

      reader.readAsText(file)
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
.card-body input {
  margin-bottom: 0.5rem;
}

.input-group .btn {
  padding: 0 0.75rem;
}
</style>
