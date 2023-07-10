<template>
  <div class="login-container">
    <h1>Login</h1>
    <form @submit="handleSubmit">
      <label for="username">Username:</label>
      <input type="text" id="username" v-model="username" required>
      <br>
      <label for="password">Password:</label>
      <input type="password" id="password" v-model="password" required>
      <br>
      <button type="submit">Login</button>
    </form>
    <p>Belum punya akun? <a href="/register">Register</a></p>
  </div>
</template>


<script>
import axios from 'axios';

export default {
  data() {
    return {
      username: '',
      password: ''
    };
  },
  methods: {
    async handleSubmit(event) {
      event.preventDefault();

      try {
        // Send login request to the server
        const response = await axios.post('http://localhost:5000/todo/login', {
          username: this.username,
          password: this.password
        });

        // Store the token in localStorage
        const token = response.data.token;
        localStorage.setItem('token', token);

        // Redirect to a protected route or perform other actions
        // For example, you can use Vue Router's `push` method to navigate to another route
        this.$router.push('/home');
      } catch (error) {
        // Handle error, show error message, etc.
        console.error(error);
      }
    }
  }
};
</script>

<style>
.login-container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: #f7f7f7;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

h1 {
  text-align: center;
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 15px;
}

label {
  font-weight: bold;
}

input[type="text"],
input[type="password"] {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

button[type="submit"] {
  display: block;
  width: 100%;
  padding: 10px;
  border: none;
  border-radius: 4px;
  background-color: #4CAF50;
  color: white;
  font-weight: bold;
  cursor: pointer;
}

button[type="submit"]:hover {
  background-color: #45a049;
}
</style>
