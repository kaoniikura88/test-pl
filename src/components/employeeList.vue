<template>
  <div class="employee-container">
    <div class="employee-card" :style="{ 'margin-left': `${level * 15}px` }">
      <div class="card-row name-row">
        <span 
          v-if="hasSubordinates"
          @click.stop="toggleExpand"
          class="toggle-icon"
        >
          {{ isExpanded ? '▼' : '▶' }}
        </span>
        <span class="name-text">{{ employee.name }}</span>
      </div>
    
      <div class="card-row phone-row">
        {{ employee.phone }}
      </div>
   
      <div class="card-row actions-row">
        <button @click.stop="$emit('delete', employee)" class="delete-btn">
          Удалить
        </button>
      </div>
    </div>

    <div v-if="isExpanded && hasSubordinates" class="subordinates-container">
      <employee-row
        v-for="sub in sortedSubordinates"
        :key="sub.id"
        :employee="sub"
        :all-employees="allEmployees"
        :level="level + 1"
        :expanded-ids="expandedIds"
        @delete="$emit('delete', $event)"
        @add="$emit('add', $event)"
        @toggle="$emit('toggle', $event)"
      />
    </div>
  </div>
</template>

<script>
export default {
  name: 'EmployeeRow',
  props: {
    employee: Object,
    allEmployees: Array,
    level: Number,
    expandedIds: {
      type: Set,
      default: () => new Set()
    },
    sortColumn: String,
    sortDirection: String
  },
  computed: {
    hasSubordinates() {
      return this.subordinates.length > 0;
    },
    isExpanded() {
      return this.expandedIds.has(this.employee.id);
    },
    subordinates() {
      return this.allEmployees.filter(e => e.boss === this.employee.name);
    },
    sortedSubordinates() {
      return [...this.subordinates].sort((a, b) => {
        const valueA = a[this.sortColumn] || '';
        const valueB = b[this.sortColumn] || '';
        return this.sortDirection === 'asc' 
          ? valueA.localeCompare(valueB)
          : valueB.localeCompare(valueA);
      });
    }
  },
  methods: {
    toggleExpand() {
      this.$emit('toggle', this.employee.id);
    }
  }
};
</script>

<style scoped>
.employee-card {
  display: flex;
  flex-direction: column;
  border: 1px solid #eee;
  border-radius: 8px;
  padding: 12px 15px;
  margin-bottom: 10px;
  background: white;
  word-break: break-word;
  cursor: pointer;
}

.card-row {
  display: block;
  padding: 4px 0;
  position: relative;
}

.name-row {
  font-weight: 500;
}

.actions-row {
  padding-bottom: 2px;
}

.toggle-icon, .toggle-spacer {
  position: absolute;
  left: 5px;
  width: 15px;
  text-align: center;
}

.delete-btn {
  background: none;
  border: none;
  color: #f44336;
  padding: 0;
  cursor: pointer;
  font-size: 14px;
  white-space: nowrap;
}

@media (min-width: 1025px) {
  .employee-card {
    display: grid;
    grid-template-columns: minmax(150px, 1fr) minmax(150px, 1fr) auto;
    align-items: center;
    padding: 12px 20px;
    gap: 15px;
  }

  .name-row {
    padding-left: 30px;
  }

  .card-row {
    padding: 0;
    overflow: hidden;
    padding-left: 30px;
  }

  .name-row, .phone-row {
    white-space: normal;
    word-break: break-word;
    padding-right: 10px;
  }

  .phone-row, .actions-row {
    padding-left: 0;
  }

  .toggle-icon {
    left: 0px;
  }
}
</style>
