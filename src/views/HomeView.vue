<template>
  <div class="container">
    <h1>Todos</h1>
    <form @submit.prevent="submitForm">
      <div class="form-group">
        <label for="kegiatan">Kegiatan</label>
        <input type="text" class="form-control" v-model="formData.kegiatan" required>
      </div>
      <div class="form-group">
        <label for="date">Date</label>
        <input type="date" class="form-control" v-model="formData.date" required>
      </div>
      <div class="form-group">
        <label for="nama">Nama</label>
        <input type="text" class="form-control" v-model="formData.nama" required>
      </div>
      <button type="submit" class="btn btn-primary">{{ editingTodoId ? 'Update Todo' : 'Add Todo' }}</button>
    </form>
    <table class="table mt-4">
      <thead>
        <tr>
          <th>#</th>
          <th>Kegiatan</th>
          <th>Date</th>
          <th>Nama</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(todo, index) in todos" :key="todo._id">
          <td>{{ index + 1 }}</td>
          <td>{{ todo.kegiatan }}</td>
          <td>{{ todo.date }}</td>
          <td>{{ todo.nama }}</td>
          <td>
            <button class="btn btn-sm btn-primary" @click="editTodoForm(todo)">Edit</button>
            <button class="btn btn-sm btn-danger" @click="deleteTodo(todo._id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- Ganti tombol logout dengan tautan teks -->
    <p class="logout-link" @click="logout">Logout</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      todos: [],
      formData: { kegiatan: '', date: '', nama: '' },
      editingTodoId: null,
      token: null
    };
  },
  mounted() {
    this.token = localStorage.getItem('token');
    this.fetchTodos();
  },
  methods: {
    fetchTodos() {
      fetch('http://localhost:5000/todo/list', {
        headers: {
          'Authorization': 'Bearer ' + this.token
        }
      })
        .then(response => response.json())
        .then(data => {
          this.todos = data;
        })
        .catch(error => {
          console.error('Failed to fetch todos:', error);
        });
    },
    submitForm() {
      const endpoint = this.editingTodoId ? `http://localhost:5000/todo/list/${this.editingTodoId}` : 'http://localhost:5000/todo/list';
      const method = this.editingTodoId ? 'PUT' : 'POST';

      fetch(endpoint, {
        method,
        headers: {
          'Content-Type': 'application/json',
          'Authorization': 'Bearer ' + this.token
        },
        body: JSON.stringify(this.formData)
      })
        .then(response => response.json())
        .then(data => {
          if (this.editingTodoId) {
            const index = this.todos.findIndex(todo => todo._id === this.editingTodoId);
            this.todos.splice(index, 1, data);
            this.editingTodoId = null;
          } else {
            this.todos.push(data);
          }
          this.resetForm();
        })
        .catch(error => {
          console.error('Failed to save todo:', error);
        });
    },
    editTodoForm(todo) {
      this.editingTodoId = todo._id;
      this.formData.kegiatan = todo.kegiatan;
      this.formData.date = todo.date;
      this.formData.nama = todo.nama;
    },
    deleteTodo(id) {
      fetch(`http://localhost:5000/todo/list/${id}`, {
        method: 'DELETE',
        headers: {
          'Authorization': 'Bearer ' + this.token
        }
      })
        .then(response => {
          if (response.ok) {
            this.todos = this.todos.filter(todo => todo._id !== id);
          } else {
            console.error('Failed to delete todo:', response.status);
          }
        })
        .catch(error => {
          console.error('Failed to delete todo:', error);
        });
    },
    resetForm() {
      this.formData = { kegiatan: '', date: '', nama: '' };
    },
    logout() {
      localStorage.removeItem('token');
      window.location.href = '/login'; // Ganti '/login' dengan URL halaman login sebenarnya
    }
  }
};
</script>

<style>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
}

h1 {
  text-align: center;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  padding: 10px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

th {
  background-color: #f2f2f2;
}

.btn {
  display: inline-block;
  padding: 6px 12px;
  margin-bottom: 0;
  font-size: 14px;
  font-weight: normal;
  line-height: 1.42857143;
  text-align: center;
  white-space: nowrap;
  vertical-align: middle;
  cursor: pointer;
  border: 1px solid transparent;
  border-radius: 4px;
}

.btn-primary {
  color: #fff;
  background-color: #007bff;
  border-color: #007bff;
}

.btn-danger {
  color: #fff;
  background-color: #dc3545;
  border-color: #dc3545;
}

.btn-sm {
  padding: 5px 10px;
  font-size: 12px;
}

.logout-link {
  color: blue;
  text-decoration: underline;
  cursor: pointer;
}
</style>
