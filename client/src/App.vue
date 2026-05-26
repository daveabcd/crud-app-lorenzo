<script setup>
import { ref, onMounted } from 'vue'
const API = 'http://localhost:4000/api/items'
const items = ref([])
const form = ref({ name: '', description: '', price: 0 })
const editId = ref(null)

async function load() {
  items.value = await fetch(API).then(r => r.json())
}

async function save() {
  if (editId.value) {
    await fetch(`${API}/${editId.value}`, {
    method: 'PUT', headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(form.value) })
    editId.value = null
  } else {
    await fetch(API, { method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(form.value) })
  }
  form.value = { name: '', description: '', price: 0 }; load()
}

function startEdit(item) {
  editId.value = item.id
  form.value = { name: item.name, description: item.description, price: item.price }
}

async function remove(id) {
  await fetch(`${API}/${id}`, { method: 'DELETE' }); load()
}

onMounted(load)
</script>

<template>
  <main>
    <h1>Items</h1>
    <form @submit.prevent="save">
      <input v-model="form.name" placeholder="Name" required />
      <input v-model="form.description" placeholder="Description" />
      <input v-model="form.price" type="number" placeholder="Price" />
      <button type="submit">{{ editId ? 'Update' : 'Add' }}</button>
    </form>
    <ul>
      <li v-for="item in items" :key="item.id">
        <strong>{{ item.name }}</strong> — {{ item.description }} — ${{ item.price }}
        <button @click="startEdit(item)">Edit</button>
        <button @click="remove(item.id)">Delete</button>
      </li>
    </ul>
  </main>
</template>

<style scoped>
main {
  max-width: 600px;
  margin: 40px auto;
  font-family: 'Segoe UI', sans-serif;
  padding: 20px;
  background: #f9f9f9;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}

h1 {
  color: #2c3e50;
  margin-bottom: 20px;
}

form {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 24px;
}

input {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 14px;
}

button {
  padding: 8px 14px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
}

button[type="submit"] {
  background: #3498db;
  color: white;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  background: white;
  padding: 12px;
  margin-bottom: 10px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  gap: 10px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.06);
}

li button {
  background: #e74c3c;
  color: white;
}

li button:first-of-type {
  background: #f39c12;
}
</style>