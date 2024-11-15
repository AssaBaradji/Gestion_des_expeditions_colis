<template>
  <div class="container mt-5">
    <h1 class="mb-4 text-center fw-bold title-margin" style="color: #3fb59e">
      Liste des Utilisateurs
    </h1>

    <div class="d-flex justify-content-between align-items-center mb-4">
      <div class="input-group" style="max-inline-size: 300px">
        <span class="input-group-text search-icon">
          <i class="fas fa-search"></i>
        </span>
        <input
          type="text"
          class="form-control"
          v-model="searchQuery"
          placeholder="Rechercher un utilisateur..."
        />
      </div>

      <router-link
        to="/users/add"
        class="btn btn- fw-bold"
        style="background-color: #3fb59e; color: white"
      >
        <i class="fas fa-plus"></i> Ajouter Utilisateur
      </router-link>
    </div>

    <div v-if="filteredUsers.length === 0" class="text-center">
      <p>Aucun utilisateur trouvé.</p>
    </div>

    <table v-else class="table table-striped table-bordered">
      <thead>
        <tr>
          <th scope="col">ID</th>
          <th scope="col">Nom</th>
          <th scope="col">Email</th>
          <th scope="col">Rôle</th>
          <th scope="col">Statut</th>
          <th scope="col" class="text-center">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in filteredUsers" :key="user.id">
          <td>{{ user.id }}</td>
          <td>{{ user.nom }}</td>
          <td>{{ user.email }}</td>
          <td>{{ user.role }}</td>

          <td>
            {{
              typeof user.statut === 'string'
                ? user.statut
                : user.statut
                ? 'Actif'
                : 'Bloqué'
            }}
          </td>
          <td class="text-center">
            <router-link
              :to="'/users/show/' + user.id"
              class="btn btn-sm btn-outline-info me-2"
            >
              <i class="fas fa-eye"></i>
            </router-link>
            <router-link
              :to="'/users/edit/' + user.id"
              class="btn btn-sm btn-outline-secondary me-2"
            >
              <i class="fas fa-edit"></i>
            </router-link>
            <button
              class="btn btn-sm btn-outline-danger"
              @click="() => deleteUser(user.id)"
            >
              <i class="fas fa-trash"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
  
  <script setup>
import { computed, onMounted, ref } from 'vue'
import { useUserStore } from '@/store/userStore'
import { useToast } from 'vue-toastification'

const toast = useToast()
const store = useUserStore()
const users = computed(() => store.users)
const searchQuery = ref('')

onMounted(async () => {
  await store.fetchUsers()
})

const filteredUsers = computed(() =>
  users.value.filter(
    user =>
      user.nom?.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      user.email?.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
)

const deleteUser = async id => {
  console.log("ID de l'utilisateur à supprimer :", id)
  try {
    await store.deleteUser(id)
    await store.fetchUsers()
  } catch (error) {
    console.error("Erreur lors de la suppression de l'utilisateur :", error)
    toast.error("Erreur lors de la suppression de l'utilisateur.")
  }
}
</script>
  
  <style scoped>
.text-primary {
  color: #0d6efd;
}
.btn- {
  background-color: #3fb59e;
  border-color: #3fb59e;
}
.btn-outline-info {
  border-color: #17a2b8;
  color: #17a2b8;
}
.btn-outline-secondary {
  border-color: #6c757d;
  color: #6c757d;
}
.btn-outline-danger {
  border-color: #dc3545;
  color: #dc3545;
}
.title-margin {
  margin-block-start: 80px;
}
.search-icon {
  background-color: #e0e0e0;
}
</style>
   