<template>
  <div id="app">

    <form @submit.prevent="addUser" class="user-form">
      <div class="form-group">
        <label for="userName">User Name:</label>
        <input type="text" id="userName" v-model="newUser.userName" class="form-control">
      </div>
      <div class="form-group">
        <label for="userNum">User Phone Number:</label>
        <input type="text" id="userNum" v-model="newUser.userNum" class="form-control">
      </div>
      <div class="form-group">
        <label for="department">Department:</label>
        <select id="department" v-model="newUser.department" class="form-control">
          <option value="" disabled selected>Select Department</option>
          <option v-for="department in departments" :key="department.id" :value="department.id">{{ department.name }}</option>
        </select>
      </div>
      <button type="submit" class="btn btn-primary">Add New User</button>
    </form>
    
    <table class="table">
      <thead>
        <tr>
          <th>Name</th>
          <th>Number</th>
          <th>Department</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in users" :key="user.id">
          <td><input type="text" v-model="user.userName" class="form-control"></td>
          <td><input type="text" v-model="user.userNum" class="form-control"></td>
          <td>
            <select v-model="user.department" class="form-control">
              <option v-for="department in departments" :key="department.id" :value="department.id">{{ department.name }}</option>
            </select>
          </td>
          <td class="actions-button-group">
            <button @click="updateUser(user.id, user)" class="btn btn-success">Update</button>
            <button @click="deleteUser(user.id)" class="btn btn-danger">Delete</button>
          </td>

        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios';

const BASE_URL = "http://localhost:3000/todos/";

export default {
  name: 'App',
  data() {
    return {
      newUser: {
        userName: '',
        userNum: '',
        department: ''
      },
      users: [],
      departments: []
    };
  },
  methods: {
    async getUsers() {
      try {
        const response = await axios.get(BASE_URL);
        this.users = response.data;
      } catch (error) {
        console.error("Error fetching users:", error);
      }
    },
    async addUser() {
      try {
        const response = await axios.post(BASE_URL, this.newUser);
        console.log("New user added:", response.data);
        this.getUsers();
        this.newUser = { userName: '', userNum: '', department: '' };
      } catch (error) {
        console.error("Error adding user:", error);
      }
    },
    async deleteUser(userId) {
      try {
        await axios.delete(BASE_URL + userId);
        console.log("User deleted:", userId);
        this.getUsers();
      } catch (error) {
        console.error("Error deleting user:", error);
      }
    },
    async updateUser(userId, updatedUser) {
      try {
        await axios.put(BASE_URL + userId, updatedUser);
        console.log("User updated:", userId);
        this.getUsers();
      } catch (error) {
        console.error("Error updating user:", error);
      }
    },
    async getDepartments() {
      try {
        const response = await axios.get("http://localhost:3000/departments");
        this.departments = response.data;
      } catch (error) {
        console.error("Error fetching departments:", error);
      }
    },
    getDepartmentName(departmentId) {
      const department = this.departments.find(dep => dep.id === departmentId);
      return department ? department.name : '';
    }
  },
  mounted() {
    this.getUsers();
    this.getDepartments(); // Fetch departments
  }
};
</script>

<style scoped>
#app {
  font-family: Arial, sans-serif;
  margin: 20px;
}

.user-form {
  background-color: #f9f9f9;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  font-weight: bold;
}

.form-control {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

select.form-control {
  height: 40px; /* Adjust the height as needed */
}

.table {
  width: 100%;
  border-collapse: collapse;
  border-spacing: 0;
}

th, td {
  padding: 10px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

th {
  background-color: bisque;
}

.form-control {
  width: 100%;
  padding: 8px;
  border: 1px solid #ccc;
  box-sizing: border-box;
}

.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  color: white;
}

.btn-primary {
  background-color: #4CAF50;
}

.btn-success {
  background-color: #1E90FF;
}

.btn-danger {
  background-color: #FF6347;
}

.btn:hover {
  opacity: 0.9;
}

.actions-button-group button {
  margin-right: 5px; 
}

.btn:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}
</style>
