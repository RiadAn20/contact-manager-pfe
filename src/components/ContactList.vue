<template>
  <div>
    <p v-if="contacts.length === 0" class="empty">Aucun contact trouvé.</p>

    <div v-else class="table-wrapper">
      <table>
        <thead>
          <tr>
            <th>Nom</th>
            <th>Email</th>
            <th>Téléphone</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <ContactItem
            v-for="contact in contacts"
            :key="contact.id"
            :contact="contact"
            @edit-contact="$emit('edit-contact', $event)"
            @delete-contact="$emit('delete-contact', $event)"
          />
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import ContactItem from './ContactItem.vue'

defineProps({
  contacts: { type: Array, required: true }
})

defineEmits(['edit-contact', 'delete-contact'])
</script>
