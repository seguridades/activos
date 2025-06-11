<template>
  <div class="container mt-5">
    <!-- Hero -->
    <section class="mb-4 text-center">
      <div class="d-flex justify-content-center mb-3">
        <i class="bi bi-gear-wide-connected display-3 text-primary"></i>
      </div>
      <h3 class="text-muted small">Configura los datos de tu organización y áreas</h3>
    </section>

    <!-- Guardar / Volver - Destacado arriba -->
    <section class="mb-5 position-relative">
      <div class="d-flex flex-wrap gap-3 justify-content-center">
        <button @click="saveOrganization" class="btn btn-lg btn-success fw-bold">
          <i class="bi bi-save me-1"></i> Guardar Configuración
        </button>
        <router-link to="/list" class="btn btn-lg btn-secondary">
          <i class="bi bi-arrow-left me-1"></i> Ir al Listado
        </router-link>
      </div>
    </section>

    <!-- Datos de la organización -->
    <section class="mb-5">
      <div class="card shadow-sm border-0 mb-4">
        <div class="card-body">
          <h4 class="card-title text-primary fw-bold mb-3">🏢 Organización</h4>

          <!-- Nombre -->
          <div class="mb-3">
            <label for="orgName" class="form-label">Nombre de la Organización</label>
            <input v-model="organization.name" id="orgName" type="text" class="form-control" />
            <small class="text-muted mt-1 d-block">Ej: Red de Seguridad Digital</small>
          </div>

          <!-- Responsable -->
          <div class="mb-0">
            <label for="orgResponsible" class="form-label">Responsable del seguimiento</label>
            <input
              v-model="organization.responsable"
              id="orgResponsible"
              type="text"
              class="form-control"
            />
            <small class="text-muted mt-1 d-block">
              Esta persona supervisará el inventario de activos
            </small>
          </div>
        </div>
      </div>
    </section>

    <!-- Gestión de áreas -->
    <section class="mb-5">
      <h4 class="mb-3 d-flex align-items-center">
        <i class="bi bi-diagram-3 me-2"></i> Áreas de Trabajo
        <span class="badge bg-secondary ms-2">{{ organization.areas.length }}</span>
      </h4>

      <div
        v-for="(area, index) in organization.areas"
        :key="index"
        class="card shadow-sm border mb-3"
      >
        <div class="card-header bg-white d-flex justify-content-between align-items-center">
          <strong>{{ area.name || 'Área sin nombre' }}</strong>
          <button @click="removeArea(index)" class="btn btn-sm btn-outline-danger">
            <i class="bi bi-trash"></i>
          </button>
        </div>
        <div class="card-body">
          <div class="row g-3">
            <!-- Nombre del área -->
            <div class="col-md-5">
              <input
                v-model="area.name"
                type="text"
                class="form-control"
                placeholder="Nombre del área"
              />
            </div>

            <!-- Responsable del área -->
            <div class="col-md-5">
              <input
                v-model="area.responsable"
                type="text"
                class="form-control"
                placeholder="Responsable del área"
              />
            </div>
          </div>

          <!-- Personal -->
          <div class="mt-4">
            <h6 class="fw-semibold">🧑‍💼 Personal asignado</h6>

            <div v-if="area.personal && area.personal.length > 0" class="list-personal mt-2">
              <div
                v-for="(persona, pIndex) in area.personal"
                :key="pIndex"
                class="input-group input-group-sm mb-2"
              >
                <input
                  v-model="area.personal[pIndex].nombre"
                  type="text"
                  class="form-control form-control-sm"
                />
                <button @click="removePersonFromArea(index, pIndex)" class="btn btn-outline-danger">
                  <i class="bi bi-trash"></i>
                </button>
              </div>
            </div>

            <button @click="addPersonToArea(index)" class="btn btn-sm btn-outline-secondary mt-2">
              <i class="bi bi-person-plus me-1"></i> Añadir Persona
            </button>
          </div>
        </div>
      </div>

      <button @click="addNewArea" class="btn btn-outline-primary mt-3">
        <i class="bi bi-plus-circle me-1"></i> Añadir Nueva Área
      </button>

      <!-- Guardar / Volver - Destacado arriba -->
      <section class="mb-5 position-relative">
        <div class="d-flex flex-wrap gap-3 justify-content-center">
          <button @click="saveOrganization" class="btn btn-lg btn-success fw-bold">
            <i class="bi bi-save me-1"></i> Guardar Configuración
          </button>
          <router-link to="/list" class="btn btn-lg btn-secondary">
            <i class="bi bi-arrow-left me-1"></i> Ir al Listado
          </router-link>
        </div>
      </section>
    </section>

    <!-- Línea divisoria -->
    <hr class="my-5" />

    <!-- Exportar / Importar -->
    <section class="mb-5">
      <div class="row g-4">
        <!-- Exportar -->
        <div class="col-md-6">
          <div class="card shadow-sm border h-100">
            <div class="card-body d-flex flex-column">
              <h5 class="card-title text-primary d-flex align-items-center">
                <i class="bi bi-box-arrow-up me-2"></i> Exportar Todo
              </h5>
              <p class="card-text small">
                Genera un archivo .json con la organización y todos los activos registrados. Ideal
                para respaldos o migraciones entre dispositivos.
              </p>
              <button @click="exportAllData" class="btn btn-primary mt-auto align-self-start">
                📤 Exportar Todo
              </button>
            </div>
          </div>
        </div>

        <!-- Importar -->
        <div class="col-md-6">
          <div class="card shadow-sm border h-100">
            <div class="card-body d-flex flex-column">
              <h5 class="card-title text-success d-flex align-items-center">
                <i class="bi bi-box-arrow-in-down me-2"></i> Importar desde Backup
              </h5>
              <p class="card-text small">
                Carga un archivo .json generado por esta herramienta. Esta acción reemplazará los
                datos actuales. Asegúrate de tener un respaldo si es necesario.
              </p>

              <label for="jsonFile" class="form-label mt-3">Selecciona un archivo .json</label>
              <input
                type="file"
                id="jsonFile"
                accept=".json"
                @change="importJSON"
                class="form-control form-control-sm"
              />

              <small class="text-muted mt-2">
                Formato esperado: <code>{ organization: {...}, assets: [...] }</code>
              </small>
            </div>
          </div>
        </div>
      </div>
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
      // Valida que cada área tenga personal como array
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
      this.organization.areas.splice(index, 1)
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
      // Asegura que todas las áreas tengan personal como array
      this.organization.areas = this.organization.areas.map((area) => ({
        ...area,
        personal: Array.isArray(area.personal) ? area.personal : [],
      }))

      localStorage.setItem('organization', JSON.stringify(this.organization))
      this.showNotification('Éxito', 'Organización guardada correctamente', 'success')
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

          if (!parsed.organization || !parsed.assets) {
            throw new Error('Falta "organization" o "assets"')
          }

          const confirmImport = confirm('¿Estás seguro? Esto borrará los datos actuales.')
          if (!confirmImport) return

          localStorage.setItem('organization', JSON.stringify(parsed.organization))
          localStorage.setItem('assets', JSON.stringify(parsed.assets))

          this.showNotification('Éxito', 'Datos importados correctamente', 'success')
          setTimeout(() => {
            this.$router.push('/list')
          }, 1500)
        } catch (error) {
          this.showNotification('Error', 'El archivo no tiene un formato válido.', 'danger')
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

.list-personal {
  max-height: 200px;
  overflow-y: auto;
}

/* Scroll fino */
.list-personal::-webkit-scrollbar {
  width: 6px;
}

.list-personal::-webkit-scrollbar-thumb {
  background: #ccc;
  border-radius: 4px;
}
</style>
