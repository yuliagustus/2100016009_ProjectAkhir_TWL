<template>
  <div class="container">
    <form @submit="registerUser">
      <label for="username">Username</label>
      <input type="text" v-model="username" required />

      <label for="password">Password</label>
      <input type="password" v-model="password" required />

      <button type="submit">Register</button>
    </form>

    <p v-if="error" class="error">{{ error }}</p>
    <p v-if="message" class="success">{{ message }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      username: '',
      password: '',
      error: '',
      message: '',
    };
  },
  methods: {
    async registerUser(event) {
      event.preventDefault();

      try {
        const response = await fetch('http://localhost:5000/todo/register', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            username: this.username,
            password: this.password,
          }),
        });

        const data = await response.json();

        if (response.ok) {
          this.message = data.message;
          this.error = '';

          // Redirect to the login page
          this.$router.push('/login');
        } else {
          this.error = data.error;
          this.message = '';
        }
      } catch (error) {
        console.error(error);
        this.error = 'Server error';
        this.message = '';
      }
    },
  },
};
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

form {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 300px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: #f7f7f7;
}

button[type="submit"] {
  display: block;
  width: 100%;
  padding: 10px;
  border: none;
  border-radius: 4px;
  background-color: khaki;
  color: white;
  font-weight: bold;
  cursor: pointer;
}

button[type="submit"]:hover {
  background-color: #FFFF00;
}

</style>
