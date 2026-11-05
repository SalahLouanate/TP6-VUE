<template>
  <div v-if="student">
    <h1>{{ student.firstName }} {{ student.lastName }}</h1>

    <!-- TODO D : édition directe des champs avec v-model sur "student" -->
    <p>
      <label>
        Filière :
        <input v-model="student.filiere" />
      </label>
    </p>
    <p>
      <label>
        Année :
        <input v-model.number="student.year" type="number" min="1" max="5" />
      </label>
    </p>
    <p>
      <label>
        Email :
        <input v-model="student.email" type="email" />
      </label>
    </p>
  </div>
  <div v-else>
    <p>Étudiant introuvable.</p>
  </div>

  <button @click="$router.back()">Retour</button>
</template>

<script>
import { store } from '../store/students.js'

export default {
  name: 'StudentDetailView',
  computed: {
    // TODO C : retrouvez dans store.students l'étudiant dont l'id
    // correspond au paramètre de route (rappel du TP4 : $route.params.id
    // est une chaîne)
    student() {
      const id = Number(this.$route.params.id)
      return store.students.find(s => s.id === id)
    }
  }
}
</script>
