<template>
  <tr>
    <td class="name">{{ contact.name }}</td>
    <td>{{ contact.email }}</td>
    <td>{{ contact.phone }}</td>
    <td class="row-actions">
      <button class="btn edit" @click="$emit('edit-contact', contact)">Modifier</button>
      <button class="btn delete" @click="$emit('delete-contact', contact)">Supprimer</button>
    </td>
  </tr>
</template>

<script setup>
defineProps({
  contact: { type: Object, required: true }
})

defineEmits(['edit-contact', 'delete-contact'])
</script>
