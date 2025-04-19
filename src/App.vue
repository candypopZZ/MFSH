<script setup>
import HelloWorld from './components/HelloWorld.vue'
import { ref, onMounted } from 'vue'
import PocketBase from 'pocketbase'

const pb = new PocketBase('http://127.0.0.1:8090')
const hi = "my name is"
const isTrue = ref(true)
const solatAPIlink = "https://www.e-solat.gov.my/index.php?r=esolatApi/takwimsolat&period=today&zone=WLY01"
let records = ref([])
const todoInput = ref('')
let number = ref(0)
let prayerTime = ref({})
let capitalCities = ref(["London", "Paris", "Berlin", "Kuala Lumpur"])

function increment() {
  number.value++
  console.log(number)
}

function startInterval() {
  setInterval(increment, 1000)
}

function addCity() {
  if (name.value.trim() !== "") {
    capitalCities.value.push(name.value)
    name.value = "" // Reset input
  }
}

function getPrayerTime() {
  fetch(solatAPIlink)
    .then(result => result.json())
    .then(data => {
      prayerTime.value = data.prayerTime[0] || {}
      console.log(prayerTime.value)
    })
    .catch(error => console.error("Error fetching prayer time:", error))
}

function createTodo(){
  const Todo = {
    item: todoInput.value,
    isDone: false
  }
  pb.collection('todos').create(newTodo).then(res => {
    records.value.puch(res)
    todoInput.value = ''
  })
}

onMounted(async() => {
  getPrayerTime()
})
</script>

<template>
<div>
    <h1 class="text-2xl font-bold mb-4">ToDos</h1>
    <ul class="list-disc pl-6">
      <li v-for="todo in todos" :key="todo.id" class="mb-2">
        <h2 class="font-semibold">{{ todo.item }}</h2>
      </li>
    </ul>
  </div>
  <button class="bg-indigo-400">Add item</button>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-6">
    <div class="max-w-4xl mx-auto bg-white rounded-xl shadow-lg p-6">
      <!-- Counter Section -->
      <div class="flex items-center space-x-4 mb-8">
        <div class="bg-indigo-100 rounded-lg p-4">
          <h1 class="text-4xl font-bold text-indigo-700">{{ number }}</h1>
        </div>
        <button 
          @click="startInterval" 
          class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-lg transition-colors duration-300 shadow-md flex items-center"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
            <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm1-11a1 1 0 10-2 0v3.586L7.707 9.293a1 1 0 00-1.414 1.414l3 3a1 1 0 001.414 0l3-3a1 1 0 00-1.414-1.414L11 10.586V7z" clip-rule="evenodd" />
          </svg>
          Increment
        </button>
      </div>

      <!-- City Input Section -->
      <div class="mb-8">
        <div class="flex flex-col md:flex-row space-y-3 md:space-y-0 md:space-x-3">
          <input 
            type="text" 
            v-model="name" 
            placeholder="Enter a city name"
            class="flex-grow px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition-all duration-300" 
          />
          <button 
            @click="addCity" 
            class="bg-emerald-600 hover:bg-emerald-700 text-white px-4 py-2 rounded-lg transition-colors duration-300 shadow-md flex items-center justify-center"
          >
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd" d="M10 3a1 1 0 011 1v5h5a1 1 0 110 2h-5v5a1 1 0 11-2 0v-5H4a1 1 0 110-2h5V4a1 1 0 011-1z" clip-rule="evenodd" />
            </svg>
            Add a city
          </button>
        </div>
      </div>

      <!-- Info Display Section -->
      <div class="mb-8">
        <h1 class="text-xl text-gray-700 font-medium mb-1">{{ hi }}</h1>
        <h2 class="text-2xl text-indigo-700 font-bold mb-4">{{ name || "Enter your name above" }}</h2>
        
        <div class="bg-gray-50 rounded-lg p-6 border border-gray-200 shadow-sm mb-6">
          <h3 class="text-lg text-gray-700 font-medium mb-3 flex items-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2 text-indigo-600" viewBox="0 0 20 20" fill="currentColor">
              <path d="M10.707 2.293a1 1 0 00-1.414 0l-7 7a1 1 0 001.414 1.414L4 10.414V17a1 1 0 001 1h2a1 1 0 001-1v-2a1 1 0 011-1h2a1 1 0 011 1v2a1 1 0 001 1h2a1 1 0 001-1v-6.586l.293.293a1 1 0 001.414-1.414l-7-7z" />
            </svg>
            Cities List
          </h3>
          <ol class="list-decimal pl-6 space-y-2">
            <li 
              v-for="(city, index) in capitalCities" 
              :key="index" 
              class="text-gray-700 hover:text-indigo-700 transition-colors duration-300"
            >
              {{ city }}
            </li>
          </ol>
        </div>

        <div class="flex flex-col sm:flex-row sm:items-center sm:space-x-4 mb-6">
          <h1 v-if="isTrue" class="bg-blue-100 text-blue-800 px-4 py-2 rounded-lg mb-3 sm:mb-0">
            Kuala Lumpur is my city
          </h1>
          <button 
            @click="isTrue = !isTrue" 
            class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg transition-colors duration-300 shadow-md flex items-center justify-center"
          >
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd" d="M4 2a1 1 0 011 1v2.101a7.002 7.002 0 0111.601 2.566 1 1 0 11-1.885.666A5.002 5.002 0 005.999 7H9a1 1 0 010 2H4a1 1 0 01-1-1V3a1 1 0 011-1zm.008 9.057a1 1 0 011.276.61A5.002 5.002 0 0014.001 13H11a1 1 0 110-2h5a1 1 0 011 1v5a1 1 0 11-2 0v-2.101a7.002 7.002 0 01-11.601-2.566 1 1 0 01.61-1.276z" clip-rule="evenodd" />
            </svg>
            Toggle City
          </button>
        </div>
      </div>

      <!-- Prayer Times Section -->
      <div class="mb-6">
        <button 
          @click="getPrayerTime" 
          class="w-full bg-teal-600 hover:bg-teal-700 text-white py-3 rounded-lg transition-colors duration-300 shadow-md flex items-center justify-center"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
            <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-6-3a2 2 0 11-4 0 2 2 0 014 0zm-2 4a5 5 0 00-4.546 2.916A5.986 5.986 0 0010 16a5.986 5.986 0 004.546-2.084A5 5 0 0010 11z" clip-rule="evenodd" />
          </svg>
          Get Prayer Time
        </button>
      </div>

      <!-- Prayer Time Table -->
      <div class="rounded-xl overflow-hidden shadow-lg">
        <div class="bg-teal-600 text-white py-4 px-6">
          <h2 class="text-xl font-bold text-center">Prayer Times</h2>
        </div>
        
        <div class="overflow-x-auto bg-white">
          <table class="w-full">
            <thead>
              <tr class="bg-teal-50 text-teal-700 border-b border-teal-100">
                <th class="px-4 py-3 font-semibold text-center">Fajr</th>
                <th class="px-4 py-3 font-semibold text-center">Dhuha</th>
                <th class="px-4 py-3 font-semibold text-center">Dhuhr</th>
                <th class="px-4 py-3 font-semibold text-center">Asr</th>
                <th class="px-4 py-3 font-semibold text-center">Maghrib</th>
                <th class="px-4 py-3 font-semibold text-center">Isha</th>
              </tr>
            </thead>
            <tbody>
              <tr class="border-b border-gray-100">
                <td class="px-4 py-4 text-center font-medium text-gray-700">{{ prayerTime.fajr || '–' }}</td>
                <td class="px-4 py-4 text-center font-medium text-gray-700">{{ prayerTime.dhuha || '–' }}</td>
                <td class="px-4 py-4 text-center font-medium text-gray-700">{{ prayerTime.dhuhr || '–' }}</td>
                <td class="px-4 py-4 text-center font-medium text-gray-700">{{ prayerTime.asr || '–' }}</td>
                <td class="px-4 py-4 text-center font-medium text-gray-700">{{ prayerTime.maghrib || '–' }}</td>
                <td class="px-4 py-4 text-center font-medium text-gray-700">{{ prayerTime.isha || '–' }}</td>
              </tr>
            </tbody>
          </table>
        </div>
        
        <div class="bg-teal-50 p-4 flex items-center justify-between">
          <div>
            <h3 class="text-lg font-medium text-teal-800">Fajr</h3>
            <h2 class="text-2xl font-bold text-teal-700">{{ prayerTime.fajr || 'Loading...' }}</h2>
          </div>
          <div class="bg-teal-100 p-3 rounded-full">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 text-teal-700" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
            </svg>
          </div>
        </div>
      </div>
    </div>
  </div>

  
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>