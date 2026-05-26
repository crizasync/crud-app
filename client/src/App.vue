<script setup>
import { ref, onMounted } from "vue";
const API = "http://localhost:4000/api/items";
const items = ref([]);
const form = ref({ name: "", description: "", price: "" });
const editId = ref(null);

async function load() {
  items.value = await fetch(API).then((r) => r.json());
}

async function save() {
  if (editId.value) {
    await fetch(`${API}/${editId.value}`, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(form.value),
    });
    editId.value = null;
  } else {
    await fetch(API, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(form.value),
    });
  }
  form.value = { name: "", description: "", price: "" };
  load();
}

function startEdit(item) {
  editId.value = item.id;
  form.value = {
    name: item.name,
    description: item.description,
    price: item.price,
  };
}

async function remove(id) {
  await fetch(`${API}/${id}`, { method: "DELETE" });
  load();
}

onMounted(load);
</script>

<template>
  <main class="page-shell">
    <section class="hero">
      <p class="eyebrow">INVENTORY dashboard</p>
      <h1>Items</h1>
      <p class="hero-copy">
        Manage inventory with a simple system built for quick edits and clear
        scanning.
      </p>
    </section>

    <section class="content-grid">
      <article class="panel form-panel">
        <div class="panel-heading">
          <h2>{{ editId ? "Update item" : "Add item" }}</h2>
          <span>Fast entry</span>
        </div>

        <form class="item-form" @submit.prevent="save">
          <input v-model="form.name" placeholder="Name" required />
          <input v-model="form.description" placeholder="Description" />
          <input v-model="form.price" placeholder="Price" />
          <button type="submit">{{ editId ? "Update" : "Add" }}</button>
        </form>
      </article>

      <article class="panel list-panel">
        <div class="panel-heading">
          <h2>Saved items</h2>
          <span>{{ items.length }} total</span>
        </div>

        <ul class="item-list">
          <li v-for="item in items" :key="item.id" class="item-card">
            <div class="item-copy">
              <strong>{{ item.name }}</strong>
              <p>{{ item.description || "No description" }}</p>
            </div>

            <div class="item-meta">
              <span class="price">P{{ item.price }}</span>
              <div class="actions">
                <button class="secondary" @click="startEdit(item)">Edit</button>
                <button class="danger" @click="remove(item.id)">Delete</button>
              </div>
            </div>
          </li>
        </ul>
      </article>
    </section>
  </main>
</template>

<style scoped>
.page-shell {
  width: min(1120px, 100%);
  margin: 0 auto;
}

.hero {
  display: grid;
  gap: 0.75rem;
  margin-bottom: 1.5rem;
  padding: 1.5rem;
  border: 1px solid var(--color-border-strong);
  background: linear-gradient(
    135deg,
    rgba(255, 69, 69, 0.1),
    rgba(255, 250, 246, 0.98)
  );
}

.eyebrow {
  color: var(--color-accent);
  text-transform: uppercase;
  letter-spacing: 0.22em;
  font-size: 0.75rem;
  font-weight: 700;
}

h1,
h2 {
  color: var(--color-heading);
  line-height: 1;
}

h1 {
  font-size: clamp(2.5rem, 6vw, 4.5rem);
  text-transform: uppercase;
  letter-spacing: 0.08em;
}

.hero-copy {
  max-width: 60ch;
  color: var(--color-text-muted);
}

.content-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1.25rem;
}

.panel {
  border: 1px solid var(--color-border-strong);
  background: var(--color-surface);
  padding: 1.25rem;
  box-shadow:
    0 0 0 1px rgba(255, 69, 69, 0.06),
    0 18px 36px rgba(85, 40, 40, 0.08);
}

.list-panel {
  display: flex;
  flex-direction: column;
  min-height: 0;
  overflow: hidden;
}

.panel-heading {
  display: flex;
  justify-content: space-between;
  gap: 1rem;
  align-items: baseline;
  margin-bottom: 1rem;
  text-transform: uppercase;
  letter-spacing: 0.08em;
}

.panel-heading span {
  color: var(--color-text-muted);
  font-size: 0.8rem;
}

.item-form {
  display: grid;
  gap: 0.8rem;
}

.item-list {
  gap: 0.55rem;
  display: grid;
  flex: 1;
  list-style: none;
  padding: 0;
  margin: 0;
  overflow-y: auto;
  padding-right: 0.25rem;
  min-height: 0;
}

.item-card {
  display: grid;
  gap: 0.55rem;
  padding: 0.7rem 0.8rem;
  border: 1px solid var(--color-border-soft);
  background: linear-gradient(
    180deg,
    rgba(255, 250, 246, 1),
    rgba(255, 69, 69, 0.025)
  );
}

.item-copy {
  display: grid;
  gap: 0.2rem;
}

.item-copy strong {
  font-size: 1rem;
}

.item-copy p {
  font-size: 0.95rem;
  line-height: 1.35;
}

.item-copy p,
.price {
  color: var(--color-text-muted);
}

.item-meta {
  display: flex;
  justify-content: flex-start;
  gap: 0.7rem;
  align-items: center;
  flex-wrap: nowrap;
}

.actions {
  display: flex;
  gap: 0.4rem;
  flex-wrap: nowrap;
}

.price {
  margin-right: auto;
}

.item-card button {
  padding: 0.5rem 0.75rem;
  font-size: 0.88rem;
}

button,
input {
  border-radius: 0;
  border: 1px solid var(--color-border-strong);
  font: inherit;
}

input {
  width: 100%;
  padding: 0.9rem 1rem;
  background: #fffaf6;
  color: var(--color-text);
}

input::placeholder {
  color: rgba(79, 32, 32, 0.55);
}

button {
  padding: 0.85rem 1.05rem;
  cursor: pointer;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  font-weight: 700;
  background: var(--color-accent);
  color: white;
}

button.secondary {
  background: transparent;
  color: var(--color-text);
}

button.danger {
  background: #831818;
  color: #ffffff;
}

button:hover {
  filter: brightness(1.08);
}

@media (min-width: 900px) {
  .content-grid {
    grid-template-columns: 360px 1fr;
    align-items: stretch;
  }

  .form-panel {
    align-self: stretch;
  }

  .form-panel,
  .list-panel {
    height: 100%;
  }

  .list-panel {
    max-height: 70vh;
  }
}
</style>
