<template>
  <div class="app">
    

    <main class="grid">
      <ContactForm
        :contact-to-edit="selectedContact"
        :is-saving="isSaving"
        @save-contact="saveContact"
        @cancel-edit="cancelEdit"
      />

      <section class="card list-card">
        <div class="section-title">
          <h2>Liste des contacts</h2>
          <button class="btn refresh" @click="fetchContacts">Actualiser</button>
        </div>

        <p v-if="message" class="message success">{{ message }}</p>
        <p v-if="error" class="message error">{{ error }}</p>
        <p v-if="isLoading" class="loading">Chargement des contacts...</p>

        <ContactList
          v-else
          :contacts="contacts"
          @edit-contact="selectContact"
          @delete-contact="deleteContact"
        />
      </section>
    </main>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import ContactForm from './components/ContactForm.vue'
import ContactList from './components/ContactList.vue'

const API_URL = 'http://localhost:3000/contacts'

const contacts = ref([])
const selectedContact = ref(null)
const isLoading = ref(false)
const isSaving = ref(false)
const error = ref('')
const message = ref('')

const clearAlerts = () => {
  error.value = ''
  message.value = ''
}

const fetchContacts = async () => {
  clearAlerts()
  isLoading.value = true
  try {
    const response = await fetch(API_URL)
    if (!response.ok) throw new Error('Impossible de récupérer les contacts')
    contacts.value = await response.json()
  } catch (err) {
    error.value = err.message
  } finally {
    isLoading.value = false
  }
}

const saveContact = async (contactData) => {
  clearAlerts()
  isSaving.value = true

  try {
    const isEdit = selectedContact.value !== null
    const url = isEdit ? `${API_URL}/${selectedContact.value.id}` : API_URL
    const method = isEdit ? 'PUT' : 'POST'

    const response = await fetch(url, {
      method,
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(contactData)
    })

    if (!response.ok) throw new Error('Erreur pendant l’enregistrement')

    const savedContact = await response.json()

    if (isEdit) {
      contacts.value = contacts.value.map((contact) =>
        contact.id === savedContact.id ? savedContact : contact
      )
      message.value = 'Contact modifié avec succès.'
    } else {
      contacts.value.push(savedContact)
      message.value = 'Contact ajouté avec succès.'
    }

    selectedContact.value = null
  } catch (err) {
    error.value = err.message
  } finally {
    isSaving.value = false
  }
}

const selectContact = (contact) => {
  clearAlerts()
  selectedContact.value = { ...contact }
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

const cancelEdit = () => {
  selectedContact.value = null
  clearAlerts()
}

const deleteContact = async (contact) => {
  const confirmed = confirm(`Voulez-vous supprimer ${contact.name} ?`)
  if (!confirmed) return

  clearAlerts()
  try {
    const response = await fetch(`${API_URL}/${contact.id}`, { method: 'DELETE' })
    if (!response.ok) throw new Error('Erreur pendant la suppression')
    contacts.value = contacts.value.filter((item) => item.id !== contact.id)
    message.value = 'Contact supprimé avec succès.'
  } catch (err) {
    error.value = err.message
  }
}

onMounted(fetchContacts)
</script>
