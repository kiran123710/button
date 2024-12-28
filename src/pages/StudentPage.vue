<template>
  <!-- Main Content Section -->
  <q-page-container class="q-mb-xl">
    <!-- Toolbar with Left-Aligned Title -->
    <q-toolbar class="text-black bg-blue-8" dense>
      <q-toolbar-title class="text-h6 row justify-start q-px-xs">
        <q-icon name="person" size="sm" class="q-pr-xs" />
        Student Details
        <div class="q-ml-auto">
          <q-btn label="Menu" @click="menuOpened = !menuOpened" icon="menu" class="q-px-xs lt-md">
            <q-menu v-model="menuOpened" transition-show="scale" transition-hide="scale">
              <q-list>
                <q-item clickable @click="search" v-model="search">
                  <q-input v-model="search" placeholder="Search students" class="q-pr-xs" color="black" dense>
                    <template v-slot:prepend>
                      <q-icon name="search" size="sm" />
                    </template>
                  </q-input>
                </q-item>
                <q-item clickable @click="openAddDialog" v-model="addStudent">
                  <q-item-section>Add Student</q-item-section>
                </q-item>
              </q-list>
            </q-menu>
          </q-btn>
        </div>
      </q-toolbar-title>
      <q-card-actions class="q-px-xs">
        <!-- Search Input with Search Icon inside -->
        <q-input v-model="search" placeholder="Search students" class="q-pl-md gt-md bg-white" color="black" dense >
          <template v-slot:prepend>
            <q-icon name="search" size="sm" />
          </template>
        </q-input>
        <q-btn icon="add" label="New Student" rounded color="primary" text-color="white" @click="openAddDialog" class="q-ml-sm q-px-xs gt-md" />
      </q-card-actions>
    </q-toolbar>

    <!-- Table displaying student details -->
    <q-table :rows="filteredRows" :columns="columns" class="q-mt-none shadow-1">
      <template v-slot:body-cell-actions="props">
        <q-td :props="props">
          <q-btn color="primary" label="Edit" @click="openEditDialog(props.row)" size="sm" class="q-mr-xs" />
          <q-btn color="negative" label="Delete" @click="deleteStudent(props.row)" size="sm" />
        </q-td>
      </template>
    </q-table>

    <!-- Add New Student Dialog -->
    <q-dialog v-model="addDialog">
      <q-card>
        <q-card-section>
          <div class="row">
            <div class="col-12 text-center">
              <div text-h6 class="q-mb-md">Add New Student</div>
            </div>
          </div>
          <div class="row">
            <div class="col-12 col-md-6 col-lg-4">
              <q-input v-model="newStudent.name" label="Name" :rules="[nameRules]" autofocus class="q-pr-md" />
            </div>
            <div class="col-12 col-md-6 col-lg-4">
              <q-input v-model="newStudent.age" label="Age" type="number" :rules="[ageRules]" class="q-pr-md" />
            </div>
            <div class="col-12 col-md-6 col-lg-4">
              <q-select v-model="newStudent.course" :options="courseOptions" label="Course" :rules="[courseRules]" emit-value class="q-pr-md" />
            </div>
            <div class="col-12 col-md-6 col-lg-4">
              <div class="q-pa-md" style="max-width: 300px">
                <q-input filled v-model="newStudent.date" mask="date" :rules="['date']">
                  <template v-slot:append>
                    <q-icon name="event" class="cursor-pointer">
                      <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                        <q-date v-model="newStudent.date">
                          <div class="row items-center justify-end">
                            <q-btn v-close-popup label="Close" color="primary" flat />
                          </div>
                        </q-date>
                      </q-popup-proxy>
                    </q-icon>
                  </template>
                </q-input>
              </div>
            </div>
          </div>
        </q-card-section>
        <q-card-actions>
          <q-btn label="Add" color="primary" @click="addStudent" />
          <q-btn label="Cancel" color="negative" @click="closeAddDialog" />
        </q-card-actions>
      </q-card>
    </q-dialog>

    <!-- Edit Student Dialog -->
    <q-dialog v-model="editDialog">
      <q-card>
        <q-card-section>
          <!-- Title centered -->
          <div class="row">
            <div class="col-12 text-center">
              <div text-h6 class="q-mb-md">Edit Student</div>
            </div>
          </div>

          <!-- Form inputs in columns -->
          <div class="row">
            <div class="col-12 col-md-6 col-lg-4">
              <q-input v-model="editStudent.name" label="Name" :rules="[nameRules]" class="q-pr-md" />
            </div>

            <div class="col-12 col-md-6 col-lg-4">
              <q-input v-model="editStudent.age" label="Age" type="number" :rules="[ageRules]" class="q-pr-md" />
            </div>

            <div class="col-12 col-md-6 col-lg-4">
              <q-select v-model="editStudent.course" :options="courseOptions" label="Course" :rules="[courseRules]" emit-value class="q-pr-md" />
            </div>

            <!-- Date picker as input field -->
            <div class="col-12 col-md-6 col-lg-4">
              <div class="q-pa-md" style="max-width: 300px">
                <q-input filled v-model="editStudent.date" mask="date" :rules="['date']">
                  <template v-slot:append>
                    <q-icon name="event" class="cursor-pointer">
                      <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                        <q-date v-model="editStudent.date">
                          <div class="row items-center justify-end">
                            <q-btn v-close-popup label="Close" color="primary" flat />
                          </div>
                        </q-date>
                      </q-popup-proxy>
                    </q-icon>
                  </template>
                </q-input>
              </div>
            </div>
          </div>
        </q-card-section>
        <q-card-actions>
          <q-btn label="Save" color="primary" @click="saveEdit" />
          <q-btn label="Cancel" color="negative" @click="closeEditDialog" />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page-container>

  <!-- Footer Section -->
  <q-footer elevated class="bg-grey-8 text-white">
    <div class="q-pa-md text-center">
      <p class="q-mb-none">© 2024 Student Management System</p>
      <p class="q-mt-none">All rights reserved</p>
    </div>
  </q-footer>
</template>

<script setup>
import { onMounted, ref, computed } from 'vue';
import { Student } from './student';
import { useQuasar } from 'quasar';

const $q = useQuasar();

const students = ref([]);
const newStudent = ref({
  name: '',
  age: '',
  course: '',
  date: null // Adding date field
});

const editStudent = ref({
  name: '',
  age: '',
  course: '',
  date: null // Adding date field
});

const columns = [
  { name: 'name', label: 'Name', required: true, align: 'left', field: row => row.name },
  { name: 'age', label: 'Age', required: true, align: 'center', field: row => row.age },
  { name: 'course', label: 'Course', required: true, align: 'center', field: row => row.course },
  { name: 'enrollment_date', label: 'Enrollment Date', required: true, align: 'center', field: row => row.date },
  { name: 'actions', label: 'Actions', align: 'center' }
];

const search = ref('');
const addDialog = ref(false);
const editDialog = ref(false);
 // For profile menu toggle

// List of available course options
const courseOptions = [
  'BBA', 'MBA', 'BA', 'Bcom', 'Engineering', 'BCA', 'Medicine'
];

// Predefined rows
onMounted(() => {
  let student1 = new Student();
  student1.name = 'Rock';
  student1.age = 22;
  student1.course = 'BBA';
  student1.date = '2023/01/01'; // Example date
  students.value.push(student1);

  let student2 = new Student();
  student2.name = 'Batista';
  student2.age = 23;
  student2.course = 'MBA';
  student2.date = '2023/02/01'; // Example date
  students.value.push(student2);

  let student3 = new Student();
  student3.name = 'John Cena';
  student3.age = 23;
  student3.course = 'BA';
  student3.date = '2023/03/01'; // Example date
  students.value.push(student3);

  let student4 = new Student();
  student4.name = 'Kane';
  student4.age = 23;
  student4.course = 'Bcom';
  student4.date = '2023/04/01'; // Example date
  students.value.push(student4);

  let student5 = new Student();
  student5.name = 'Undertaker';
  student5.age = 28;
  student5.course = 'BCA';
  student5.date = '2023/04/01'; // Example date
  students.value.push(student5);

});

// Computed property for filtered rows based on search input
const filteredRows = computed(() => {
  return students.value.filter(student => {
    return (
      student.name.toLowerCase().includes(search.value.toLowerCase()) ||
      student.age.toString().includes(search.value) ||
      student.course.toLowerCase().includes(search.value.toLowerCase()) ||
      student.date?.includes(search.value) // Include date in search
    );
  });
});

// Open the dialog to add a new student
const openAddDialog = () => {
  addDialog.value = true;
};

// Close the add student dialog
const closeAddDialog = () => {
  addDialog.value = false;
};

// Add a new student
const addStudent = () => {
  console.log(newStudent.value);  // Debugging line to check input data
  if (newStudent.value.name && newStudent.value.age && newStudent.value.course && newStudent.value.date) {
    const student = new Student();
    student.name = newStudent.value.name;
    student.age = newStudent.value.age;
    student.course = newStudent.value.course;
    student.date = newStudent.value.date;
    students.value.push(student);

    // Reset the new student form
    newStudent.value.name = '';
    newStudent.value.age = '';
    newStudent.value.course = '';
    newStudent.value.date = null;

    closeAddDialog();

    // Notify success
    $q.notify({
      color: 'positive', icon: 'check_circle',
      message: 'Student added successfully!',
      position: 'top',
      timeout: 2000
    });
  } else {
    console.log('Missing data: ', newStudent.value);  // Debugging missing data
    $q.notify({
      color: 'negative',
      icon: 'exclamation',
      message: 'Please fill in all fields!',
      position: 'top',
      timeout: 500
    });
  }
};

// Open the dialog to edit a student's details
const openEditDialog = (row) => {
  editStudent.value = { ...row }; // Clone the row data to the edit form
  editDialog.value = true;
};

// Close the edit student dialog
const closeEditDialog = () => {
  editDialog.value = false;
};

// Save the edited student
const saveEdit = () => {
  if (editStudent.value.name && editStudent.value.age && editStudent.value.course && editStudent.value.date) {
    const index = students.value.findIndex(student => student.name === editStudent.value.name);
    if (index !== -1) {
      students.value[index] = { ...editStudent.value }; // Update the student details
      closeEditDialog();

      // Notify success
      $q.notify({
        color: 'positive',
        icon: 'check_circle',
        message: 'Student details updated successfully!',
        position: 'top',
        timeout: 500
      });
    }
  } else {
    $q.notify({
      color: 'negative',
      icon: 'exclamation',
      message: 'Please fill in all fields!',
      position: 'top',
      timeout: 500
    });
  }
};

// Delete a student
const deleteStudent = (row) => {
  const index = students.value.indexOf(row);
  if (index !== -1) {
    students.value.splice(index, 1);

    // Notify success
    $q.notify({
      color: 'negative',
      icon: 'delete',
      message: 'Student deleted successfully!',
      position: 'top',
      timeout: 500
    });
  }
};
</script>
 