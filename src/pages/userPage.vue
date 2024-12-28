<template>
  <div>
    <!-- Search and Add Buttons in the same line -->
    <div class="q-mb-md  q-gutter-md" style="display: flex; justify-content: space-between; align-items: center;">
      <!-- Search Input -->
       <div >
      <q-input 
        v-model="searchQuery" 
        label="Search"
        outlined 
        class="bg-white border-2 border-solid black q-pa-xs rounded"
      />
    </div>
      <!-- Add New Row Button aligned to the right -->
      <q-btn icon="add"  @click="openAddDialog" />
    </div>

    <!-- Table to display rows (Name, Age, Course) -->
    <q-table
      :rows="filteredRows"
      :columns="columns">

      
      <!-- Actions column with Edit and Delete button -->
      <template v-slot:body-cell-actions="props">
        <q-td :props="props">
          <q-btn color="secondary" label="Edit" @click="editRow(props.row)" size="sm"/>
        </q-td>
      </template>
    </q-table>

    <!-- Add Row Dialog -->
    <q-dialog v-model="addDialog">
      <q-card>
        <q-card-section>
          <div class="text-h6">Add New Row</div>
          <q-input v-model="newName" label="Name" />
          <q-input v-model="newAge" label="Age" type="number" />
          <q-input v-model="newCourse" label="Course" />
        </q-card-section>
        <q-card-actions>
          <q-btn flat label="Add" color="positive" @click="addRow"/>
        </q-card-actions>
      </q-card>
    </q-dialog>

    <!-- Edit Row Dialog -->
    <q-dialog v-model="editDialog">
      <q-card>
        <q-card-section>
          <div class="text-h6">Edit Row</div>
          <q-input v-model="editedName" label="Name" />
          <q-input v-model="editedAge" label="Age" type="number" />
          <q-input v-model="editedCourse" label="Course" />
        </q-card-section>
        <q-card-actions>
          <q-btn flat label="Save" color="positive" @click="saveEdit"/>
          <q-btn flat label="Delete" color="negative" @click="deleteRow(editedRow)" class="q-ml-sm"/>
        </q-card-actions>
      </q-card>
    </q-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // Table data (initial rows)
      rows: [
        { name: 'John Doe', age: 25, course: 'Mathematics' },
        { name: 'Jane Smith', age: 30, course: 'Physics' },
        { name: 'Alice Johnson', age: 22, course: 'Biology' }
      ],

      // Columns definition (Name, Age, Course)
      columns: [
        { name: 'name', label: 'Name', required: true, align: 'left', field: row => row.name },
        { name: 'age', label: 'Age', required: true, align: 'center', field: row => row.age },
        { name: 'course', label: 'Course', required: true, align: 'center', field: row => row.course },
        { name: 'actions', label: 'Actions', align: 'center' }
      ],

      // Dialog and input field states
      addDialog: false,
      editDialog: false,
      newName: '',
      newAge: '',
      newCourse: '',
      editedName: '',
      editedAge: '',
      editedCourse: '',
      currentIndex: null,
      editedRow: null,  // To store the row being edited

      // Search query for filtering rows
      searchQuery: ''
    };
  },
  computed: {
    // Filter rows based on search query
    filteredRows() {
      if (!this.searchQuery) {
        return this.rows; // Return all rows if there's no search query
      }

      const query = this.searchQuery.toLowerCase();
      return this.rows.filter(row => {
        return (
          row.name.toLowerCase().includes(query) ||
          row.age.toString().includes(query) ||
          row.course.toLowerCase().includes(query)
        );
      });
    }
  },
  methods: {
    // Open the "Add New Row" dialog
    openAddDialog() {
      this.newName = '';
      this.newAge = '';
      this.newCourse = '';
      this.addDialog = true;
    },

    // Close the "Add New Row" dialog
    closeAddDialog() {
      this.addDialog = false;
    },

    // Add a new row to the table
    addRow() {
      if (this.newName && this.newAge && this.newCourse) {
        this.rows.push({ name: this.newName, age: this.newAge, course: this.newCourse });
        this.closeAddDialog();
      }
    },

    // Open the "Edit Row" dialog
    editRow(row) {
      this.editedName = row.name;
      this.editedAge = row.age;
      this.editedCourse = row.course;
      this.editedRow = row;
      this.currentIndex = this.rows.indexOf(row);
      this.editDialog = true;
    },

    // Close the "Edit Row" dialog
    closeEditDialog() {
      this.editDialog = false;
    },

    // Save the edited row
    saveEdit() {
      if (this.editedName && this.editedAge && this.editedCourse) {
        this.rows[this.currentIndex] = { name: this.editedName, age: parseInt(this.editedAge), course: this.editedCourse };
        this.closeEditDialog();
      } else {
        this.$q.notify({
          color: 'negative',
          message: 'Please provide name, age, and course!',
          icon: 'warning'
        });
      }
    },

    // Delete a row
    deleteRow(row) {
      const index = this.rows.indexOf(row);
      if (index !== -1) {
        this.rows.splice(index, 1);
      }
      this.closeEditDialog();
    }
  }
};
</script>

<style scoped>
.q-table {
  max-width: 100%;
  margin-top: 20px; 
}
</style>
