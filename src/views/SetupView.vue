<template>
  <div class="container mt-5">
    <h2>Configurar Organización</h2>

    <!-- Estado actual -->
    <div v-if="organization.name" class="alert alert-success mb-4">
      Organización: <strong>{{ organization.name }}</strong>
      <div v-if="organization.responsable">Responsable: {{ organization.responsable }}</div>
    </div>

    <!-- Nombre de la organización -->
    <form @submit.prevent="saveOrganization">
      <div class="mb-3">
        <label for="orgName" class="form-label">Nombre de la Organización</label>
        <input
          id="orgName"
          v-model="organization.name"
          type="text"
          class="form-control"
          placeholder="Ej: Empresa S.A.C."
          required
        />
      </div>

      <!-- Responsable del seguimiento -->
      <div class="mb-3">
        <label for="responsable" class="form-label">Responsable del Seguimiento de Activos</label>
        <input
          id="responsable"
          v-model="organization.responsable"
          type="text"
          class="form-control"
          placeholder="Ej: Juan Pérez"
        />
      </div>

      <!-- Gestión de Áreas -->
      <h4 class="mt-4">Áreas de la Organización</h4>
      <div v-for="(area, areaIndex) in organization.areas" :key="areaIndex" class="card mb-4 p-3">
        <div class="row align-items-center mb-3">
          <div class="col-md-5">
            <input
              v-model="area.name"
              type="text"
              class="form-control"
              placeholder="Nombre del área"
              required
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
          <div class="col-md-2 text-end">
            <button
              @click="removeArea(areaIndex)"
              type="button"
              class="btn btn-outline-danger"
              :disabled="organization.areas.length === 1"
            >
              Eliminar
            </button>
          </div>
        </div>

        <!-- Personal por área -->
        <h6 class="mt-3">Personal del Área</h6>
        <div
          v-for="(persona, personIndex) in area.personal"
          :key="personIndex"
          class="input-group mb-2"
        >
          <input
            v-model="persona.nombre"
            type="text"
            class="form-control"
            placeholder="Nombre completo"
          />
          <button
            @click="removePersona(areaIndex, personIndex)"
            type="button"
            class="btn btn-outline-danger"
            :disabled="area.personal.length === 1"
          >
            Eliminar
          </button>
        </div>

        <button @click="addPersona(areaIndex)" type="button" class="btn btn-sm btn-secondary mb-3">
          + Agregar Persona
        </button>
      </div>

      <button @click="addArea" type="button" class="btn btn-secondary mb-4">Agregar Área</button>

      <!-- Botones de acción -->
      <div>
        <button type="submit" class="btn btn-primary me-2">Guardar Configuración</button>
        <router-link to="/" class="btn btn-secondary">Volver al Inicio</router-link>
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
</template>

<script setup>
import { ref } from 'vue'
import MyNotification from '@/components/MyNotification.vue'

// Cargar organización desde localStorage
const orgData = localStorage.getItem('organization')
const organization = ref(orgData ? JSON.parse(orgData) : {})

// Validar o inicializar estructura si no existe
if (!organization.value || typeof organization.value !== 'object') {
  organization.value = {
    name: '',
    responsable: '',
    areas: [],
  }
}

if (!Array.isArray(organization.value.areas)) {
  organization.value.areas = []
}

if (organization.value.areas.length === 0) {
  organization.value.areas.push({
    name: '',
    responsable: '',
    personal: [{ nombre: '' }],
  })
} else {
  // Asegurar que cada área tenga personal
  organization.value.areas.forEach((area) => {
    if (!Array.isArray(area.personal)) {
      area.personal = []
    }
    if (area.personal.length === 0) {
      area.personal.push({ nombre: '' })
    }
  })
}

const notification = ref({
  show: false,
  title: '',
  message: '',
  type: 'success',
})

function addArea() {
  organization.value.areas.push({
    name: '',
    responsable: '',
    personal: [{ nombre: '' }],
  })
}

function removeArea(index) {
  if (organization.value.areas.length > 1) {
    organization.value.areas.splice(index, 1)
  }
}

function addPersona(areaIndex) {
  organization.value.areas[areaIndex].personal.push({ nombre: '' })
}

function removePersona(areaIndex, personIndex) {
  const area = organization.value.areas[areaIndex].personal
  if (area.length > 1) {
    area.splice(personIndex, 1)
  }
}

function saveOrganization() {
  localStorage.setItem('organization', JSON.stringify(organization.value))
  showNotification('Éxito', 'Organización y áreas guardadas correctamente', 'success')
}

function showNotification(title, message, type = 'success') {
  notification.value = { show: true, title, message, type }
  setTimeout(() => {
    notification.value.show = false
  }, 3000)
}
</script>
