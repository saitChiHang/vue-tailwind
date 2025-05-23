<script setup>
import { ref } from 'vue';
import { ChevronUpIcon, ChevronDownIcon } from '@heroicons/vue/24/outline';

const isExpanded = ref(true);
const activeMainTab = ref(0);
const activeRightTab = ref(0);

const mainTabs = ['Tab 1', 'Tab 2'];
const rightTabs = ['Chart', 'Table'];

const togglePanel = () => {
  isExpanded.value = !isExpanded.value;
};

const mockTableData = [
  { id: 1, name: 'Item 1', value: 100 },
  { id: 2, name: 'Item 2', value: 200 },
  { id: 3, name: 'Item 3', value: 300 },
];

const navItems = [
  { id: 1, name: 'Navigation 1' },
  { id: 2, name: 'Navigation 2' },
  { id: 3, name: 'Navigation 3' },
];
</script>

<template>
  <div class="fixed bottom-0 left-0 right-0 bg-white dark:bg-gray-800 shadow-lg transition-all duration-300"
       :class="{ 'h-[500px]': isExpanded, 'h-12': !isExpanded }">
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
        <div class="w-48 border-r dark:border-gray-700 p-4">
          <ul>
            <li v-for="item in navItems" 
                :key="item.id"
                class="py-2 px-3 rounded-lg cursor-pointer text-gray-600 dark:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-700">
              {{ item.name }}
            </li>
          </ul>
        </div>

        <!-- Middle Table -->
        <div class="flex-1 p-4 border-r dark:border-gray-700">
          <table class="min-w-full">
            <thead>
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

        <!-- Right Section -->
        <div class="w-96">
          <!-- Right Tabs -->
          <div class="flex border-b dark:border-gray-700">
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
          <div class="p-4">
            <div v-if="activeRightTab === 0" class="text-gray-600 dark:text-gray-400">
              Chart View (Placeholder)
            </div>
            <div v-else class="text-gray-600 dark:text-gray-400">
              <table class="min-w-full">
                <thead>
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
</template>