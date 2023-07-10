<template>
  <div class="container">
    <h1>Todos</h1>
    <form @submit.prevent="createTodo">
      <div class="form-group">
        <label for="kegiatan">Kegiatan</label>
        <input type="text" class="form-control" v-model="newTodo.kegiatan" required>
      </div>
      <div class="form-group">
        <label for="date">Date</label>
        <input type="date" class="form-control" v-model="newTodo.date" required>
      </div>
      <div class="form-group">
        <label for="nama">Nama</label>
        <input type="text" class="form-control" v-model="newTodo.nama" required>
      </div>
      <button type="submit" class="btn btn-primary">Add Todo</button>
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
            <button class="btn btn-sm btn-danger" @click="deleteTodo(todo._id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      todos: [], // Array to store todos
      newTodo: { kegiatan: '', date: '', nama: '' } // Object to store new todo data
    };
  },
  mounted() {
    // Fetch all todos when the component is mounted
    this.fetchTodos();
  },
  methods: {
  fetchTodos() {
    const token = localStorage.getItem('token'); // Get the token from localStorage

    // Make a GET request to fetch all todos
    // Replace '/api/todos' with your API endpoint
    fetch('http://localhost:5000/todo/list', {
      headers: {
        'Authorization': `Bearer ${token}` // Include the token in the Authorization header
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

  createTodo() {
    const token = localStorage.getItem('token'); // Get the token from localStorage

    // Make a POST request to create a new todo
    // Replace '/api/todos' with your API endpoint
    fetch('http://localhost:5000/todo/list', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${token}` // Include the token in the Authorization header
      },
      body: JSON.stringify(this.newTodo)
    })
      .then(response => response.json())
      .then(data => {
        this.todos.push(data);
        this.newTodo = { kegiatan: '', date: '', nama: '' };
      })
      .catch(error => {
        console.error('Failed to create todo:', error);
      });
  },

  deleteTodo(id) {
    const token = localStorage.getItem('token'); // Get the token from localStorage

    // Make a DELETE request to delete a todo
    // Replace '/api/todos' with your API endpoint
    fetch(`http://localhost:5000/todo/list/${id}`, {
      method: 'DELETE',
      headers: {
        'Authorization': `Bearer ${token}` // Include the token in the Authorization header
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
</style>