<script setup>
import { ref, computed } from 'vue';
import { ChevronUpIcon, ChevronDownIcon } from '@heroicons/vue/24/outline';
import { Bar } from 'vue-chartjs';
import { 
  Chart as ChartJS, 
  CategoryScale, 
  LinearScale, 
  BarElement, 
  Title, 
  Tooltip, 
  Legend,
  BarController 
} from 'chart.js';
import hazardData from '../assets/data.json';

ChartJS.register(
  CategoryScale,
  LinearScale,
  BarElement,
  Title,
  Tooltip,
  Legend,
  BarController
);

const isExpanded = ref(true);
const activeMainTab = ref(0);
const activeRightTab = ref(0);
const selectedYear = ref('2030');
const selectedHazard = ref('');
const selectedRow = ref(null);
const isDragging = ref(false);
const startY = ref(0);
const startHeight = ref(0);
const panelHeight = ref(40); // Default height in vh

const mainTabs = ['Data Explorer', 'Map Explorer'];
const rightTabs = ['Chart', 'Table'];
const years = ['2030', '2050', '2080'];

const emit = defineEmits(['panel-toggle']);

// Get unique hazard types
const hazardTypes = computed(() => {
  const types = new Set();
  hazardData.forEach(item => {
    if (item['Hazard Type']) {
      types.add(item['Hazard Type']);
    }
  });
  return Array.from(types);
});

// Get filtered data based on selected hazard and year
const filteredData = computed(() => {
  if (!selectedHazard.value) return [];
  return hazardData.filter(item => 
    item['Hazard Type'] === selectedHazard.value
  ).map(item => ({
    index: item['Index Name'],
    value: item[`${selectedYear.value}s.(${getYearRange(selectedYear.value)}).Estimated.Value`],
    range: item[`Projected.Range.(${getYearRange(selectedYear.value)})`]
  }));
});

// Chart data for selected row
const chartData = computed(() => {
  if (selectedRow.value === null || !selectedHazard.value) return null;
  
  const selectedItem = hazardData.find(item => 
    item['Hazard Type'] === selectedHazard.value && 
    item['Index Name'] === filteredData.value[selectedRow.value].index
  );
  
  if (!selectedItem) return null;

  return {
    labels: ['2030s', '2050s', '2080s'],
    datasets: [{
      label: selectedItem['Index Name'],
      data: [
        [
          JSON.parse(selectedItem['Projected.Range.(2015-2044)'])[0],
          JSON.parse(selectedItem['Projected.Range.(2015-2044)'])[1]
        ],
        [
          JSON.parse(selectedItem['Projected.Range.(2035-2064)'])[0],
          JSON.parse(selectedItem['Projected.Range.(2035-2064)'])[1]
        ],
        [
          JSON.parse(selectedItem['Projected.Range.(2070-2099)'])[0],
          JSON.parse(selectedItem['Projected.Range.(2070-2099)'])[1]
        ]
      ],
      backgroundColor: 'rgba(59, 130, 246, 0.5)',
      borderColor: 'rgb(59, 130, 246)',
      borderWidth: 1
    }]
  };
});

const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  scales: {
    y: {
      beginAtZero: true
    }
  },
  plugins: {
    tooltip: {
      callbacks: {
        label: (context) => {
          const range = context.raw;
          return `Range: ${range[0]} - ${range[1]}`;
        }
      }
    }
  }
};

function getYearRange(year) {
  const ranges = {
    '2030': '2015-2044',
    '2050': '2035-2064',
    '2080': '2070-2099'
  };
  return ranges[year];
}

const startDragging = (e) => {
  isDragging.value = true;
  startY.value = e.clientY;
  startHeight.value = panelHeight.value;
  document.addEventListener('mousemove', onDrag);
  document.addEventListener('mouseup', stopDragging);
};

const onDrag = (e) => {
  if (!isDragging.value) return;
  const delta = startY.value - e.clientY;
  const newHeight = startHeight.value + (delta / window.innerHeight * 100);
  panelHeight.value = Math.min(Math.max(20, newHeight), 80); // Limit between 20vh and 80vh
};

const stopDragging = () => {
  isDragging.value = false;
  document.removeEventListener('mousemove', onDrag);
  document.removeEventListener('mouseup', stopDragging);
};

const togglePanel = () => {
  isExpanded.value = !isExpanded.value;
  emit('panel-toggle');
};

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
       :class="{ 'h-12': !isExpanded }"
       :style="isExpanded ? `height: ${panelHeight}vh` : ''">
    <!-- Drag Handle -->
    <div v-if="isExpanded"
         @mousedown="startDragging"
         class="absolute top-0 left-0 right-0 h-1 cursor-ns-resize bg-transparent hover:bg-blue-500 transition-colors duration-200"
         :class="{ 'bg-blue-500': isDragging }">
    </div>

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
          <ul v-else class="p-4 space-y-2">
            <li v-for="hazard in hazardTypes" 
                :key="hazard"
                @click="selectedHazard = hazard"
                class="py-2 px-3 rounded-lg cursor-pointer transition-colors duration-200"
                :class="selectedHazard === hazard 
                  ? 'bg-blue-100 dark:bg-blue-900 text-blue-800 dark:text-blue-200' 
                  : 'text-gray-600 dark:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-700'">
              {{ hazard }}
            </li>
          </ul>
        </div>

        <!-- Middle Table -->
        <div class="flex-1 border-r dark:border-gray-700 overflow-y-auto">
          <!-- Data Explorer Content -->
          <div v-if="activeMainTab === 0" class="p-4">
            <div class="mb-4">
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
                Select Year
              </label>
              <select v-model="selectedYear"
                      class="w-full p-2 border rounded-lg bg-white dark:bg-gray-700 text-gray-800 dark:text-white border-gray-300 dark:border-gray-600">
                <option v-for="year in years" :key="year" :value="year">
                  {{ year }}s
                </option>
              </select>
            </div>
            <table class="min-w-full">
              <thead class="bg-white dark:bg-gray-800">
                <tr>
                  <th class="text-left py-2 px-4 border-b dark:border-gray-700 text-gray-800 dark:text-white">Index</th>
                  <th class="text-left py-2 px-4 border-b dark:border-gray-700 text-gray-800 dark:text-white">Value</th>
                  <th class="text-left py-2 px-4 border-b dark:border-gray-700 text-gray-800 dark:text-white">Range</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(row, index) in filteredData" 
                    :key="index"
                    @click="selectedRow = index"
                    class="cursor-pointer hover:bg-gray-50 dark:hover:bg-gray-700"
                    :class="{'bg-blue-50 dark:bg-blue-900': selectedRow === index}">
                  <td class="py-2 px-4 text-gray-600 dark:text-gray-400">{{ row.index }}</td>
                  <td class="py-2 px-4 text-gray-600 dark:text-gray-400">{{ row.value }}</td>
                  <td class="py-2 px-4 text-gray-600 dark:text-gray-400">{{ row.range }}</td>
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
              Select Option
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
              <div v-if="activeRightTab === 0" class="h-full">
                <Bar v-if="chartData" 
                     :data="chartData" 
                     :options="chartOptions" 
                     class="h-full" />
                <div v-else class="flex items-center justify-center h-full text-gray-500">
                  Select a row to view the chart
                </div>
              </div>

              <!-- Table View -->
              <div v-else>
                <table v-if="selectedRow !== null" class="min-w-full">
                  <thead class="bg-white dark:bg-gray-800">
                    <tr>
                      <th class="text-left py-2 px-4 border-b dark:border-gray-700 text-gray-800 dark:text-white">Year</th>
                      <th class="text-left py-2 px-4 border-b dark:border-gray-700 text-gray-800 dark:text-white">Value</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="year in ['2030', '2050', '2080']" :key="year">
                      <td class="py-2 px-4 text-gray-600 dark:text-gray-400">{{ year }}s</td>
                      <td class="py-2 px-4 text-gray-600 dark:text-gray-400">
                        {{ hazardData.find(item => 
                          item['Hazard Type'] === selectedHazard && 
                          item['Index Name'] === filteredData[selectedRow].index
                        )?.[`${year}s.(${getYearRange(year)}).Estimated.Value`] }}
                      </td>
                    </tr>
                  </tbody>
                </table>
                <div v-else class="text-gray-500 text-center mt-4">
                  Select a row to view the data
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>