<script setup>
import { ref } from 'vue';
import { ChevronUpIcon, ChevronDownIcon } from '@heroicons/vue/24/outline';

const isExpanded = ref(true);
const activeMainTab = ref(0);
const activeRightTab = ref(0);

const mainTabs = ['Data Explorer', 'Map Explorer'];
const rightTabs = ['Chart', 'Table'];

const togglePanel = () => {
  isExpanded.value = !isExpanded.value;
};

const mockTableData = Array.from({ length: 20 }, (_, i) => ({
  id: i + 1,
  name: `Item ${i + 1}`,
  value: (i + 1) * 100
}));

const navItems = Array.from({ length: 15 }, (_, i) => ({
  id: i + 1,
  name: `Navigation ${i + 1}`
}));

const dropdownOptions = [
  'Option 1',
  'Option 2',
  'Option 3',
  'Option 4',
  'Option 5'
];

const selectedOption1 = ref(dropdownOptions[0]);
const selectedOption2 = ref(dropdownOptions[0]);
const selectedOption3 = ref(dropdownOptions[0]);
</script>

<template>
  <div class="relative bg-white dark:bg-gray-800 shadow-lg transition-all duration-300"
       :class="{ 'h-[40vh]': isExpanded, 'h-12': !isExpanded }">
    <!-- Header -->
    <div class="flex items-center justify-between px-4 h-12 border-b dark:border-gray-700">
      <h3 class="text-gray-800 dark:text-white font-medium">Panel</h3>
      <button @click="togglePanel" class="p-2 hover:bg-gray-100 dark:hover:bg-gray-700 rounded-lg">
        <ChevronDownIcon v-if="isExpanded" class="h-5 w-5 text-gray-600 dark:text-gray-300" />
        <ChevronUpIcon v-else class="h-5 w-5 text-gray-600 dark:text-gray-300" />
      </button>
    </div>

    <!-- Content -->
    <div v-show="isExpanded" class="h-[calc(100%-3rem)]">
      <!-- Main Tabs -->
      <div class="flex border-b dark:border-gray-700">
        <button v-for="(tab, index) in mainTabs" 
                :key="index"
                @click="activeMainTab = index"
                class="px-4 py-2 font-medium transition-colors duration-200"
                :class="activeMainTab === index 
                  ? 'text-blue-600 border-b-2 border-blue-600 dark:text-blue-400 dark:border-blue-400' 
                  : 'text-gray-600 dark:text-gray-400 hover:text-gray-800 dark:hover:text-white'">
          {{ tab }}
        </button>
      </div>

      <!-- Tab Content -->
      <div class="flex h-[calc(100%-2.5rem)]">
        <!-- Left Navigation -->
        <div class="w-48 border-r dark:border-gray-700 overflow-y-auto">
          <div v-if="activeMainTab === 1" class="p-4">
            <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
              First Column Dropdown
            </label>
            <select v-model="selectedOption1"
                    class="w-full p-2 border rounded-lg bg-white dark:bg-gray-700 text-gray-800 dark:text-white border-gray-300 dark:border-gray-600">
              <option v-for="option in dropdownOptions" :key="option" :value="option">
                {{ option }}
              </option>
            </select>
          </div>
          <ul v-else class="p-4">
            <li v-for="item in navItems" 
                :key="item.id"
                class="py-2 px-3 rounded-lg cursor-pointer text-gray-600 dark:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-700">
              {{ item.name }}
            </li>
          </ul>
        </div>

        <!-- Middle Table -->
        <div class="flex-1 border-r dark:border-gray-700 overflow-y-auto">
          <!-- Data Explorer Content -->
          <div v-if="activeMainTab === 0" class="p-4">
            <div class="mb-4">
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
                Select an option
              </label>
              <select v-model="selectedOption1"
                      class="w-full p-2 border rounded-lg bg-white dark:bg-gray-700 text-gray-800 dark:text-white border-gray-300 dark:border-gray-600">
                <option v-for="option in dropdownOptions" :key="option" :value="option">
                  {{ option }}
                </option>
              </select>
            </div>
            <table class="min-w-full">
              <thead class="bg-white dark:bg-gray-800">
                <tr>
                  <th class="text-left py-2 px-4 border-b dark:border-gray-700 text-gray-800 dark:text-white">Name</th>
                  <th class="text-left py-2 px-4 border-b dark:border-gray-700 text-gray-800 dark:text-white">Value</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="row in mockTableData" :key="row.id">
                  <td class="py-2 px-4 text-gray-600 dark:text-gray-400">{{ row.name }}</td>
                  <td class="py-2 px-4 text-gray-600 dark:text-gray-400">{{ row.value }}</td>
                </tr>
              </tbody>
            </table>
          </div>

          <!-- Map Explorer Content -->
          <div v-else class="p-4">
            <div class="mb-4">
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
                Second Column Dropdown
              </label>
              <select v-model="selectedOption2"
                      class="w-full p-2 border rounded-lg bg-white dark:bg-gray-700 text-gray-800 dark:text-white border-gray-300 dark:border-gray-600">
                <option v-for="option in dropdownOptions" :key="option" :value="option">
                  {{ option }}
                </option>
              </select>
            </div>
          </div>
        </div>

        <!-- Right Section -->
        <div class="w-96">
          <div v-if="activeMainTab === 1" class="p-4">
            <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
              Third Column Dropdown
            </label>
            <select v-model="selectedOption3"
                    class="w-full p-2 border rounded-lg bg-white dark:bg-gray-700 text-gray-800 dark:text-white border-gray-300 dark:border-gray-600">
              <option v-for="option in dropdownOptions" :key="option" :value="option">
                {{ option }}
              </option>
            </select>
          </div>
          <div v-else>
            <!-- Right Tabs -->
            <div class="flex border-b dark:border-gray-700 sticky top-0 bg-white dark:bg-gray-800">
              <button v-for="(tab, index) in rightTabs" 
                      :key="index"
                      @click="activeRightTab = index"
                      class="px-4 py-2 font-medium transition-colors duration-200"
                      :class="activeRightTab === index 
                        ? 'text-blue-600 border-b-2 border-blue-600 dark:text-blue-400 dark:border-blue-400' 
                        : 'text-gray-600 dark:text-gray-400 hover:text-gray-800 dark:hover:text-white'">
                {{ tab }}
              </button>
            </div>

            <!-- Right Tab Content -->
            <div class="overflow-y-auto h-[calc(100%-2.5rem)] p-4">
              <!-- Chart View -->
              <div v-if="activeRightTab === 0" class="text-gray-600 dark:text-gray-400">
                Chart View (Placeholder)
              </div>

              <!-- Table View -->
              <div v-else>
                <table class="min-w-full">
                  <thead class="sticky top-0 bg-white dark:bg-gray-800">
                    <tr>
                      <th class="text-left py-2 px-4 border-b dark:border-gray-700 text-gray-800 dark:text-white">Name</th>
                      <th class="text-left py-2 px-4 border-b dark:border-gray-700 text-gray-800 dark:text-white">Value</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="row in mockTableData" :key="row.id">
                      <td class="py-2 px-4 text-gray-600 dark:text-gray-400">{{ row.name }}</td>
                      <td class="py-2 px-4 text-gray-600 dark:text-gray-400">{{ row.value }}</td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>