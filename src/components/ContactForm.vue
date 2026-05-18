<template>
  <section class="card form-card">
    <h2>{{ contactToEdit ? 'Modifier le contact' : 'Ajouter un contact' }}</h2>

    <form @submit.prevent="handleSubmit">
      <label>
        Nom
        <input v-model.trim="form.name" type="text" placeholder="Ex: Ahmed Kamali" />
      </label>

      <label>
        Email
        <input v-model.trim="form.email" type="email" placeholder="exemple@email.com" />
      </label>

      <label>
        Téléphone
        <input v-model.trim="form.phone" type="text" placeholder="+212600000000" />
      </label>

      <p v-if="formError" class="message error">{{ formError }}</p>

      <div class="actions">
        <button class="btn primary" type="submit" :disabled="isSaving">
          {{ isSaving ? 'Enregistrement...' : contactToEdit ? 'Modifier' : 'Ajouter' }}
        </button>
        <button v-if="contactToEdit" class="btn secondary" type="button" @click="cancel">
          Annuler
        </button>
      </div>
    </form>
  </section>
</template>

<script setup>
import { reactive, ref, watch } from 'vue'

const props = defineProps({
  contactToEdit: { type: Object, default: null },
  isSaving: { type: Boolean, default: false }
})

const emit = defineEmits(['save-contact', 'cancel-edit'])

const form = reactive({ name: '', email: '', phone: '' })
const formError = ref('')

const resetForm = () => {
  form.name = ''
  form.email = ''
  form.phone = ''
  formError.value = ''
}

watch(
  () => props.contactToEdit,
  (contact) => {
    if (contact) {
      form.name = contact.name
      form.email = contact.email
      form.phone = contact.phone
    } else {
      resetForm()
    }
  },
  { immediate: true }
)

const isValidEmail = (email) => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)

const handleSubmit = () => {
  formError.value = ''

  if (!form.name || !form.email || !form.phone) {
    formError.value = 'Tous les champs sont obligatoires.'
    return
  }

  if (!isValidEmail(form.email)) {
    formError.value = 'Format email invalide.'
    return
  }

  emit('save-contact', { name: form.name, email: form.email, phone: form.phone })

  if (!props.contactToEdit) resetForm()
}

const cancel = () => {
  resetForm()
  emit('cancel-edit')
}
</script>
