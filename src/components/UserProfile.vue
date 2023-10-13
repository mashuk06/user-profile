<script setup>
  import { ref, reactive, computed, watch } from 'vue'

  const editing = ref(false)
  const userAge = ref(33)
  const profileImage = ref('https://bootdey.com/img/Content/avatar/avatar7.png')
  const userBirthYear = ref(1990)

  const profile = reactive({
    firstName: 'John',
    lastName: 'Doe',
    birthDate: '1990-01-01',
    age: 33,
    email: 'john.doe@example.com',
    phone: '01900000000',
    address: 'Dhaka, Bangladesh',
    description: 'Full Stack Developer'
  })

  const fullName = computed(() => {
    return profile.firstName + ' ' + profile.lastName
  })

  const birthYear = computed(() => {
    userBirthYear.value = new Date().getFullYear() - profile.age
  })

  const legalityCheck = computed(() => {
    return profile.age >= 18;
  });

  const editProfile = () => {
    editing.value = true
  }

  const saveProfile = () => {
    editing.value = false
  }

  const changeProfileImage = () => {
    $refs.fileInput.click()
  }

  const handleFileUpload = (event) => {
    const file = event.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onload = () => {
        profileImage.value = reader.result;
      }
      reader.readAsDataURL(file);
    }
  }

  watch(() => profile.age, (newVal) => {
    userBirthYear.value = new Date().getFullYear() - newVal
    const currentYear = new Date().getFullYear();
    const newBirthYear = currentYear - newVal;
    profile.birthDate = `${newBirthYear}-${profile.birthDate.split('-').slice(1).join('-')}`;
  }, { deep: true })
  watch(() => profile.birthDate, (newVal) => {
    userBirthYear.value = new Date(newVal).getFullYear()
    profile.age = new Date().getFullYear() - new Date(newVal).getFullYear()
  }, { deep: true })
</script>

<template>
  <div class="container">
    <div class="main-body">
      <nav aria-label="breadcrumb" class="main-breadcrumb">
        <ol class="breadcrumb">
          <li class="breadcrumb-item"><a class="link-dark text-decoration-none" href="javascript:;">Home</a></li>
          <li class="breadcrumb-item"><a class="link-dark text-decoration-none" href="javascript:;">User</a></li>
          <li class="breadcrumb-item active text-primary" aria-current="page">User Profile</li>
        </ol>
      </nav>
      <div class="row gutters-sm">
        <div class="col-md-4 mb-3">
          <div class="card">
            <div class="card-body">
              <div class="d-flex flex-column align-items-center text-center">
                <div class="image-container position-relative">
                  <img :src="profileImage" alt="Admin" class="rounded-circle" height="150" width="150">
                  <i class="fa fa-edit edit-icon text-danger" aria-hidden="true" @click="$refs.fileInput.click()"></i>
                  <input type="file" style="display: none;" ref="fileInput" @change="handleFileUpload($event)">
                </div>
                <div class="mt-3">
                  <h4>{{ fullName }}</h4>
                  <p class="text-secondary mb-1">{{ profile.description }}</p>
                  <div class="flex justify-content-between">
                    <p class="m-2"> Birth Year: <b>{{ userBirthYear }}</b></p>
                    <p class="text-success" v-if="legalityCheck">Eligibility: Your age is eligible for election</p>
                    <p class="text-danger" v-else>Eligibility: Your age is not eligible for election</p>
                    <button :class="profile.age >=18 ? 'btn btn-success' : 'btn btn-danger'">
                      {{ profile.age >=18 ? 'Eligible for voting' : 'Not eligible for voting' }}
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-md-8">
          <div class="card mb-3">
            <div class="card-body" v-if="editing">
              <form @submit.prevent="saveProfile">
                <div class="mb-3">
                  <label for="firstName" class="form-label">First Name</label>
                  <input v-model="profile.firstName" type="text" class="form-control" id="firstName" required>
                </div>
                <div class="mb-3">
                  <label for="lastName" class="form-label">Last Name</label>
                  <input v-model="profile.lastName" type="text" class="form-control" id="lastName" required>
                </div>
                <div class="mb-3">
                  <label for="birthdate" class="form-label">Birth date</label>
                  <input v-model="profile.birthDate" type="date" class="form-control" id="birthdate" required>
                </div>
                <div class="mb-3">
                  <label for="age" class="form-label">Age</label>
                  <input v-model="profile.age" type="number" class="form-control" id="age" required>
                </div>
                <div class="mb-3">
                  <label for="email" class="form-label">Email</label>
                  <input v-model="profile.email" type="email" class="form-control" id="email" required>
                </div>
                <div class="mb-3">
                  <label for="phone" class="form-label">Phone</label>
                  <input v-model="profile.phone" type="text" class="form-control" id="phone" required>
                </div>
                <div class="mb-3">
                  <label for="address" class="form-label">Address</label>
                  <input v-model="profile.address" type="text" class="form-control" id="address" required>
                </div>
                <div class="mb-3">
                  <label for="description" class="form-label">Description</label>
                  <textarea v-model="profile.description" class="form-control" id="description" rows="3"></textarea>
                </div>
                <button type="submit" class="btn btn-primary">Save</button>
              </form>
            </div>
            <div class="card-body" v-else>
              <div class="row">
                <div class="col-sm-3">
                  <h6 class="mb-0">Full Name</h6>
                </div>
                <div class="col-sm-9 text-secondary">
                  {{ fullName }}
                </div>
              </div>
              <hr>
              <div class="row">
                <div class="col-sm-3">
                  <h6 class="mb-0">Email</h6>
                </div>
                <div class="col-sm-9 text-secondary">
                  {{ profile.email }}
                </div>
              </div>
              <hr>
              <div class="row">
                <div class="col-sm-3">
                  <h6 class="mb-0">Age</h6>
                </div>
                <div class="col-sm-9 text-secondary">
                  {{ profile.age }}
                </div>
              </div>
              <hr>
              <div class="row">
                <div class="col-sm-3">
                  <h6 class="mb-0">Mobile</h6>
                </div>
                <div class="col-sm-9 text-secondary">
                  {{ profile.phone }}
                </div>
              </div>
              <hr>
              <div class="row">
                <div class="col-sm-3">
                  <h6 class="mb-0">Address</h6>
                </div>
                <div class="col-sm-9 text-secondary">
                  {{ profile.address }}
                </div>
              </div>
              <hr>
              <div class="row">
                <div class="col-sm-12">
                  <a class="btn btn-info" @click.prevent="editProfile">Edit</a>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
  .image-container {
    position: relative;
    display: inline-block;
  }

  .edit-icon {
    position: absolute;
    top: 0;
    right: 0;
    cursor: pointer;
    background-color: white;
  }
</style>
