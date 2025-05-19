<template>
  <div v-if="show" class="modal-overlay" @click.self="$emit('close')">
    <div class="modal-content">
      <span class="close" @click="$emit('close')">&times;</span>
      <h2>{{ title }}</h2>
      <form @submit.prevent="$emit('submit', employeeData)">
        <div class="form-group">
          <label>Имя:</label>
          <input 
            type="text" 
            v-model="employeeData.name" 
            required
            placeholder="Введите имя"
          >
        </div>
        
        <div class="form-group">
          <label>Телефон:</label>
          <input 
            type="tel" 
            v-model="employeeData.phone" 
            required
            placeholder="Введите телефон"
          >
        </div>
        
        <div class="form-group" v-if="employeesList.length > 0">
          <label>Начальник:</label>
          <select v-model="employeeData.boss">
            <option value="">Нет начальника</option>
            <option 
              v-for="employee in employeesList" 
              :key="employee.id"
              :value="employee.name"
            >
              {{ employee.name }}
            </option>
          </select>
        </div>
        
        <button type="submit" class="submit-button">Добавить</button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    show: Boolean,
    title: String,
    employeesList: Array,
    initialData: Object
  },
  data() {
    return {
      employeeData: { ...this.initialData }
    };
  }
};
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0,0,0,0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background: white;
  border-radius: 8px;
  padding: 25px;
  width: 450px;
  max-width: 90%;
  box-shadow: 0 5px 15px rgba(0,0,0,0.3);
  position: relative;
}

.close {
  position: absolute;
  top: 15px;
  right: 15px;
  font-size: 24px;
  color: gray;
  cursor: pointer;
  transition: color 0.2s;
}

.close:hover {
  color: darkgray;
}

h2 {
  margin-top: 0;
  color: darkgray;
  padding-bottom: 10px;
  border-bottom: 1px solid #eee;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  font-weight: 500;
  color: #555;
}

.form-group input,
.form-group select {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
  transition: border 0.3s;
}

.form-group input:focus,
.form-group select:focus {
  border-color: #4CAF50;
  outline: none;
}

.submit-button {
  width: 100%;
  padding: 12px;
  background: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background 0.3s;
}

.submit-button:hover {
  background: #3e8e41;
}
</style>
