<template>
  <div class="employee-main">
    <div class="header">
      <button @click="showAddModal(null)" class="add-button">
        <span>+</span> Добавить сотрудника
      </button>
    </div>

    <div class="table-container">
      <div class="table-header">
        <div class="header-cell" @click="sortBy('name')">
          Имя
          <span class="sort-icon" :class="{ active: sortColumn === 'name' }">
            {{ sortDirection === 'asc' && sortColumn === 'name' ? '↑' : '↓' }}
          </span>
        </div>
        <div class="header-cell" @click="sortBy('phone')">
          Телефон
          <span class="sort-icon" :class="{ active: sortColumn === 'phone' }">
            {{ sortDirection === 'asc' && sortColumn === 'phone' ? '↑' : '↓' }}
          </span>
        </div>
        <div class="header-cell"></div>
      </div>

      <div class="table-body">
        <employeeList
          v-for="employee in sortedTopLevelEmployees"
          :key="employee.id"
          :employee="employee"
          :all-employees="employees"
          :level="0"
          :expanded-ids="expandedEmployees"
          :sort-column="sortColumn"
          :sort-direction="sortDirection"
          @delete="deleteEmployee"
          @add="showAddModal"
          @toggle="toggleEmployeeExpand"
        />
      </div>
    </div>

    <addEmployeeModal
      v-if="isModalVisible"
      :show="isModalVisible"
      title="Добавить сотрудника"
      :employeesList="employees"
      :initialData="{ name: '', phone: '', boss: selectedBoss }"
      @close="closeModal"
      @submit="addEmployee"
    />
  </div>
</template>

<script>
import addEmployeeModal from './addEmployeeModal.vue';
import employeeList from './employeeList.vue';

export default {
  components: {
    addEmployeeModal,
    employeeList
  },
  data() {
    return {
      isModalVisible: false,
      employees: [],
      selectedBoss: null,
      sortColumn: 'name',
      sortDirection: 'asc',
      expandedEmployees: new Set()
    };
  },
  computed: {
    sortedEmployees() {
      return [...this.employees].sort((a, b) => {
        const valA = a[this.sortColumn] || '';
        const valB = b[this.sortColumn] || '';
        return this.sortDirection === 'asc' 
          ? valA.localeCompare(valB)
          : valB.localeCompare(valA);
      });
    },
    sortedTopLevelEmployees() {
      return this.sortedEmployees.filter(emp => !emp.boss || emp.boss === '');
    }
  },
  methods: {
    sortBy(column) {
      if (this.sortColumn === column) {
        this.sortDirection = this.sortDirection === 'asc' ? 'desc' : 'asc';
      } else {
        this.sortColumn = column;
        this.sortDirection = 'asc';
      }
    },
    showAddModal(boss) {
      this.selectedBoss = boss ? boss.name : '';
      this.isModalVisible = true;
    },
    closeModal() {
      this.isModalVisible = false;
    },
    addEmployee(employeeData) {
      employeeData.id = this.generateId();
      this.employees.push(employeeData);
      this.saveEmployees();
      this.closeModal();
      console.log(this.employees)
    },
    deleteEmployee(employee) {
      if (confirm(`Удалить сотрудника ${employee.name} и всех его подчиненных?`)) {
        const deleteSubordinates = (bossName) => {
          const subs = this.employees.filter(item => item.boss === bossName);
          subs.forEach(sub => {
            deleteSubordinates(sub.name);
            this.employees = this.employees.filter(item => item !== sub);
          });
        };
        
        deleteSubordinates(employee.name);
        this.employees = this.employees.filter(item => item !== employee);
        this.saveEmployees();
      }
    },
    toggleEmployeeExpand(employeeId) {
      if (this.expandedEmployees.has(employeeId)) {
        this.expandedEmployees.delete(employeeId);
      } else {
        this.expandedEmployees.add(employeeId);
      }
      this.expandedEmployees = new Set(this.expandedEmployees);
    },
    generateId() {
      return Date.now().toString(36) + Math.random().toString(36).substr(2);
    },
    saveEmployees() {
      localStorage.setItem('employees', JSON.stringify(this.employees));
    },
    loadEmployees() {
      const saved = localStorage.getItem('employees');
      this.employees = saved ? JSON.parse(saved) : [];
    }
  },
  created() {
    this.loadEmployees();
  }
};
</script>

<style scoped>
.employee-main {
  max-width: 900px;
  margin: 0 auto;
  padding: 25px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.header {
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin-bottom: 25px;
  padding-bottom: 15px;
  border-bottom: 1px solid #eee;
}

.header h2 {
  margin: 0;
  color: #2c3e50;
  font-size: 24px;
}

.add-button {
  background: #4CAF50;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.2s;
  width: auto;
  align-self: flex-start;
}

.add-button:hover {
  background: #3d8b40;
  transform: translateY(-2px);
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.table-container {
  display: flex;
  flex-direction: column;
  border: 1px solid #eee;
  border-radius: 4px;
  overflow: hidden;
}

.table-header {
  display: grid;
  grid-template-columns: 1fr 1fr 100px;
  background: #f5f5f5;
  font-weight: 600;
}

.header-cell {
  padding: 12px 20px;
  cursor: pointer;
  position: relative;
}

.header-cell:last-child {
  cursor: default;
}

.sort-icon {
  position: absolute;
  right: 5px;
  opacity: 0.3;
}

.sort-icon.active {
  opacity: 1;
}

.table-body {
  display: flex;
  flex-direction: column;
}

@media (max-width: 1024px) {
  .employee-manager {
    padding: 15px;
  }

  .table-container {
    padding: 0;
    border: none;
    background: transparent;
  }

  .table-body {
    gap: 8px;
    padding: 0 5px;
  }

  .employee-card {
    margin-left: 0;
    margin-right: 0;
    border-radius: 6px;
    padding: 12px;
  }
}

@media (min-width: 1025px) {
  .employee-card {
    max-width: 100%;
  }

  .table-container {
    overflow: visible;
  }

  .table-body {
    min-width: 0;
  }
}
</style>
