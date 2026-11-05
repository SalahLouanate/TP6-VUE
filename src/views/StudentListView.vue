<template>
  <h1>Annuaire des étudiants</h1>

  <!-- TODO A : formulaire d'ajout (prenom, nom, filiere, annee, email) -->
  <form @submit.prevent="ajouterEtudiant">
    <input v-model="newStudent.firstName" placeholder="Prénom" required />
    <input v-model="newStudent.lastName" placeholder="Nom" required />
    <input v-model="newStudent.filiere" placeholder="Filière" />
    <input v-model.number="newStudent.year" type="number" min="1" max="5" placeholder="Année" />
    <input v-model="newStudent.email" type="email" placeholder="Email" />
    <button type="submit">Ajouter</button>
  </form>

  <!-- TODO B : recherche (texte) + tri (select : nom / filiere) -->
  <div class="toolbar">
    <input v-model="recherche" placeholder="Rechercher par nom ou prénom..." />
    <select v-model="tri">
      <option value="lastName">Trier par nom</option>
      <option value="filiere">Trier par filière</option>
    </select>
  </div>

  <!-- TODO B (suite) : bouclez sur le résultat recherché + trié -->
  <StudentCard
    v-for="student in etudiantsAffiches"
    :key="student.id"
    :student="student"
  />
</template>

<script>
import StudentCard from '../components/StudentCard.vue'
import { store } from '../store/students.js'

export default {
  name: 'StudentListView',
  components: { StudentCard },
  data() {
    return {
      store,
      recherche: '',
      tri: 'lastName',
      newStudent: { firstName: '', lastName: '', filiere: '', year: 1, email: '' }
    }
  },
  computed: {
    // TODO B : à partir de store.students, filtrez par recherche (nom ou
    // prénom contient le texte, insensible à la casse) puis triez selon
    // this.tri ('lastName' ou 'filiere')
    etudiantsAffiches() {
      const q = this.recherche.trim().toLowerCase()
      const filtered = this.store.students.filter(student => {
        const prenom = (student.firstName || '').toLowerCase()
        const nom = (student.lastName || '').toLowerCase()
        return prenom.includes(q) || nom.includes(q)
      })

      return [...filtered].sort((a, b) => {
        if (this.tri === 'filiere') {
          const compFiliere = (a.filiere || '').localeCompare(b.filiere || '')
          if (compFiliere !== 0) return compFiliere
          return (a.lastName || '').localeCompare(b.lastName || '')
        }
        const compNom = (a.lastName || '').localeCompare(b.lastName || '')
        if (compNom !== 0) return compNom
        return (a.firstName || '').localeCompare(b.firstName || '')
      })
    }
  },
  methods: {
    // TODO A : ajoutez newStudent (avec un nouvel id) à store.students,
    // validez qu'au moins prenom+nom sont renseignés avant d'ajouter
    ajouterEtudiant() {
      if (!this.newStudent.firstName.trim() || !this.newStudent.lastName.trim()) {
        return
      }

      const maxId = this.store.students.reduce((max, s) => Math.max(max, s.id), 0)
      this.store.students.push({
        id: maxId + 1,
        firstName: this.newStudent.firstName.trim(),
        lastName: this.newStudent.lastName.trim(),
        filiere: this.newStudent.filiere.trim(),
        year: Number(this.newStudent.year) || 1,
        email: this.newStudent.email.trim()
      })

      this.newStudent = { firstName: '', lastName: '', filiere: '', year: 1, email: '' }
    }
  }
}
</script>
