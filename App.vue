<template>
  <div class="app">
    <div class="main-layout">
      <!-- Left Column: Interactive Chart -->
      <div class="chart-container">
        <div ref="chartRef" class="chart"></div>
        
        <!-- Coordinate Controls -->
        <div class="coordinate-controls">
          <div class="coordinates-grid">
            <div class="coordinate-item">
              <label>Point A:</label>
              <div class="coord-inputs">
                <div class="input-group">
                  <label class="axis-label">Sq Ft</label>
                  <input 
                    v-model.number="points.A.x" 
                    type="number" 
                    min="0" 
                    max="1" 
                    step="0.001"
                    class="coord-input"
                    @input="validateCoordinate('A', 'x', $event)"
                    placeholder="0.000"
                  />
                </div>
                <div class="input-group">
                  <label class="axis-label">Econ</label>
                  <input 
                    v-model.number="points.A.y" 
                    type="number" 
                    min="0" 
                    max="1" 
                    step="0.001"
                    class="coord-input"
                    @input="validateCoordinate('A', 'y', $event)"
                    placeholder="0.000"
                  />
                </div>
              </div>
            </div>
            
            <div class="coordinate-item">
              <label>Point B:</label>
              <div class="coord-inputs">
                <div class="input-group">
                  <label class="axis-label">Sq Ft</label>
                  <input 
                    v-model.number="points.B.x" 
                    type="number" 
                    min="0" 
                    max="1" 
                    step="0.001"
                    class="coord-input"
                    @input="validateCoordinate('B', 'x', $event)"
                    placeholder="0.000"
                  />
                </div>
                <div class="input-group">
                  <label class="axis-label">Econ</label>
                  <input 
                    v-model.number="points.B.y" 
                    type="number" 
                    min="0" 
                    max="1" 
                    step="0.001"
                    class="coord-input"
                    @input="validateCoordinate('B', 'y', $event)"
                    placeholder="0.000"
                  />
                </div>
              </div>
            </div>
            
            <div class="coordinate-item">
              <label>Point C:</label>
              <div class="coord-inputs">
                <div class="input-group">
                  <label class="axis-label">Sq Ft</label>
                  <input 
                    v-model.number="points.C.x" 
                    type="number" 
                    min="0" 
                    max="1" 
                    step="0.001"
                    class="coord-input"
                    @input="validateCoordinate('C', 'x', $event)"
                    placeholder="0.000"
                  />
                </div>
                <div class="input-group">
                  <label class="axis-label">Econ</label>
                  <input 
                    v-model.number="points.C.y" 
                    type="number" 
                    min="0" 
                    max="1" 
                    step="0.001"
                    class="coord-input"
                    @input="validateCoordinate('C', 'y', $event)"
                    placeholder="0.000"
                  />
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      
      <!-- Right Column: Controls and Output -->
      <div class="controls-output">
        <!-- Settings Button -->
        <button @click="showSettings = !showSettings" class="settings-button">
          {{ showSettings ? 'Hide' : 'Show' }} Settings
        </button>
        
        <!-- Parameter Controls (Collapsible) -->
        <div v-if="showSettings" class="parameter-controls">
          <h3>Parameter Controls</h3>
          
          <div class="control-group">
            <h4>Square Footage (sq. ft.)</h4>
            <div class="input-row">
              <label>Min:</label>
              <input 
                v-model.number="params.sqftMin" 
                type="number" 
                min="100" 
                step="50"
                class="number-input"
              />
            </div>
            <div class="input-row">
              <label>Max:</label>
              <input 
                v-model.number="params.sqftMax" 
                type="number" 
                min="params.sqftMin + 100" 
                step="50"
                class="number-input"
              />
            </div>
          </div>
          
          <div class="control-group">
            <h4>Price ($/month)</h4>
            <div class="input-row">
              <label>Min:</label>
              <input 
                v-model.number="params.priceMin" 
                type="number" 
                min="100" 
                step="50"
                class="number-input"
              />
            </div>
            <div class="input-row">
              <label>Max:</label>
              <input 
                v-model.number="params.priceMax" 
                type="number" 
                min="params.priceMin + 100" 
                step="50"
                class="number-input"
              />
            </div>
          </div>
          
          <button @click="resetPoints" class="reset-button">Reset Points</button>
        </div>

        <!-- Generated Output -->
        <div class="generated-output">
          <h3>Problem</h3>
          
          <!-- Preset Trial Buttons -->
          <div class="preset-trials">
            <h4>Preset Trials</h4>
            <div class="trial-buttons">
              <button @click="loadTrial('similarity')" class="trial-button">
                <div class="trial-name">Similarity</div>
              </button>
              <button @click="loadTrial('compromise')" class="trial-button">
                <div class="trial-name">Compromise</div>
              </button>
              <button @click="loadTrial('attraction')" class="trial-button">
                <div class="trial-name">Attraction</div>
              </button>
            </div>
          </div>
          
          <div class="problem-description">
            <div class="option-cards">
              <div class="option-card">
                <div class="option-header">Option A</div>
                <div class="option-details">
                  <div>Sq ft: {{ transformedValues.A.sqft }} sq ft</div>
                  <div>Price: ${{ transformedValues.A.price }}</div>
                </div>
              </div>
              
              <div class="option-card">
                <div class="option-header">Option B</div>
                <div class="option-details">
                  <div>Sq ft: {{ transformedValues.B.sqft }} sq ft</div>
                  <div>Price: ${{ transformedValues.B.price }}</div>
                </div>
              </div>
              
              <div class="option-card">
                <div class="option-header">Option C</div>
                <div class="option-details">
                  <div>Sq ft: {{ transformedValues.C.sqft }} sq ft</div>
                  <div>Price: ${{ transformedValues.C.price }}</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed, onMounted, onUnmounted, watch, nextTick } from 'vue'
import * as echarts from 'echarts'

// ===== STATE =====
const chartRef = ref(null)
const showSettings = ref(false)
let chartInstance = null

// Points data (0-1 range for chart coordinates)
const points = reactive({
  A: { x: 0.250, y: 0.800 },
  B: { x: 0.500, y: 0.500 },
  C: { x: 0.850, y: 0.200 }
})

// Parameter controls for real-world transformation
const params = reactive({
  sqftMin: 300,
  sqftMax: 2500,
  priceMin: 500,
  priceMax: 4000
})

// Hardcoded trial configurations
const HARDCODED_TRIALS = {
  similarity: {
    ID: "similarity_ABD",
    "Context Category": "similarity",
    arate: 0.31, aecon: 0.79,
    brate: 0.79, becon: 0.31,
    drate: 0.85, decon: 0.25
  },
  compromise: {
    ID: "compromise_ABD",
    "Context Category": "compromise",
    arate: 0.32, aecon: 0.78,
    brate: 0.78, becon: 0.32,
    drate: 0.55, decon: 0.55
  },
  attraction: {
    ID: "attraction_ABD",
    "Context Category": "attraction",
    arate: 0.33, aecon: 0.77,
    brate: 0.77, becon: 0.33,
    drate: 0.85, decon: 0.30
  }
}

// ===== UTILITY FUNCTIONS =====
function transformSquareFootage(q, min, max) {
  const sqft = q * (max - min) + min
  return Math.round(sqft)
}

function transformPrice(q, min, max) {
  const price = (1 - q) * (max - min) + min
  return Math.round(price)
}

function roundToThreeDecimals(value) {
  return Math.round(value * 1000) / 1000
}

// ===== COMPUTED PROPERTIES =====
const transformedValues = computed(() => ({
  A: {
    sqft: transformSquareFootage(points.A.x, params.sqftMin, params.sqftMax),
    price: transformPrice(points.A.y, params.priceMin, params.priceMax)
  },
  B: {
    sqft: transformSquareFootage(points.B.x, params.sqftMin, params.sqftMax),
    price: transformPrice(points.B.y, params.priceMin, params.priceMax)
  },
  C: {
    sqft: transformSquareFootage(points.C.x, params.sqftMin, params.sqftMax),
    price: transformPrice(points.C.y, params.priceMin, params.priceMax)
  }
}))

// ===== CHART CONFIGURATION =====
const getChartOptions = () => ({
  grid: {
    left: '15%',
    right: '10%',
    bottom: '15%',
    top: '10%'
  },
  xAxis: {
    type: 'value',
    min: 0,
    max: 1,
    name: 'Square Footage',
    nameLocation: 'middle',
    nameGap: 25,
    nameTextStyle: {
      fontSize: 12
    },
    axisLabel: {
      formatter: (value) => value.toFixed(1)
    }
  },
  yAxis: {
    type: 'value',
    min: 0,
    max: 1,
    name: 'Economy',
    nameLocation: 'middle',
    nameGap: 40,
    nameTextStyle: {
      fontSize: 12
    },
    axisLabel: {
      formatter: (value) => value.toFixed(1)
    }
  },
  series: [
    {
      type: 'scatter',
      data: [
        { name: 'A', value: [points.A.x, points.A.y], itemStyle: { color: '#0066cc' } },
        { name: 'B', value: [points.B.x, points.B.y], itemStyle: { color: '#009900' } },
        { name: 'C', value: [points.C.x, points.C.y], itemStyle: { color: '#ff6600' } }
      ],
      symbolSize: 20,
      label: {
        show: true,
        position: 'top',
        formatter: '{b}',
        fontSize: 14,
        fontWeight: 'normal',
        color: '#333'
      },
      emphasis: {
        focus: 'series',
        scale: 1.1
      }
    },
    {
      type: 'line',
      data: [
        [points.A.x, points.A.y],
        [points.B.x, points.B.y],
        [points.C.x, points.C.y],
        [points.A.x, points.A.y] // Close the triangle
      ],
      lineStyle: {
        color: '#666',
        width: 2
      },
      areaStyle: {
        color: 'rgba(200, 200, 200, 0.3)'
      },
      symbol: 'none',
      silent: true
    }
  ],
  tooltip: {
    trigger: 'item',
    formatter: (params) => {
      if (params.seriesType === 'scatter') {
        const point = params.data
        return `${point.name}<br/>Sq Ft: ${point.value[0].toFixed(3)}<br/>Econ: ${point.value[1].toFixed(3)}`
      }
      return ''
    }
  }
})

// ===== CHART FUNCTIONS =====
// Initialize chart
const initChart = () => {
  if (chartRef.value) {
    // Create a wrapper div for the chart
    const chartWrapper = document.createElement('div')
    chartWrapper.style.width = '100%'
    chartWrapper.style.height = '100%'
    chartWrapper.style.position = 'absolute'
    chartWrapper.style.top = '0'
    chartWrapper.style.left = '0'
    
    chartRef.value.appendChild(chartWrapper)
    chartInstance = echarts.init(chartWrapper)
    updateChart()
    
    // Handle dragging interaction
    let isDragging = false
    let dragPointIndex = -1
    
    chartInstance.getZr().on('mousedown', (e) => {
      const pointInPixel = [e.offsetX, e.offsetY]
      const pointInGrid = chartInstance.convertFromPixel('grid', pointInPixel)
      
      if (pointInGrid && pointInGrid[0] >= 0 && pointInGrid[0] <= 1 && 
          pointInGrid[1] >= 0 && pointInGrid[1] <= 1) {
        
        // Find which point is closest
        const distances = [
          Math.sqrt(Math.pow(pointInGrid[0] - points.A.x, 2) + Math.pow(pointInGrid[1] - points.A.y, 2)),
          Math.sqrt(Math.pow(pointInGrid[0] - points.B.x, 2) + Math.pow(pointInGrid[1] - points.B.y, 2)),
          Math.sqrt(Math.pow(pointInGrid[0] - points.C.x, 2) + Math.pow(pointInGrid[1] - points.C.y, 2))
        ]
        
        const minDistance = Math.min(...distances)
        if (minDistance < 0.15) {
          dragPointIndex = distances.indexOf(minDistance)
          isDragging = true
        }
      }
    })
    
    chartInstance.getZr().on('mousemove', (e) => {
      if (isDragging && dragPointIndex >= 0) {
        const pointInPixel = [e.offsetX, e.offsetY]
        const pointInGrid = chartInstance.convertFromPixel('grid', pointInPixel)
        
        if (pointInGrid && pointInGrid[0] >= 0 && pointInGrid[0] <= 1 && 
            pointInGrid[1] >= 0 && pointInGrid[1] <= 1) {
          
          // Update the dragged point
          const pointNames = ['A', 'B', 'C']
          const pointName = pointNames[dragPointIndex]
          points[pointName].x = roundToThreeDecimals(Math.max(0, Math.min(1, pointInGrid[0])))
          points[pointName].y = roundToThreeDecimals(Math.max(0, Math.min(1, pointInGrid[1])))
        }
      }
    })
    
    chartInstance.getZr().on('mouseup', () => {
      isDragging = false
      dragPointIndex = -1
    })
  }
}

// Update chart
const updateChart = () => {
  if (chartInstance) {
    chartInstance.setOption(getChartOptions())
  }
}

// ===== USER INTERACTION FUNCTIONS =====
// Reset points to initial positions
const resetPoints = () => {
  points.A = { x: 0.250, y: 0.800 }
  points.B = { x: 0.500, y: 0.500 }
  points.C = { x: 0.850, y: 0.200 }
  updateChart()
}

// Load a preset trial configuration
const loadTrial = (trialType) => {
  const trial = HARDCODED_TRIALS[trialType]
  if (trial) {
    points.A.x = roundToThreeDecimals(trial.arate)
    points.A.y = roundToThreeDecimals(trial.aecon)
    points.B.x = roundToThreeDecimals(trial.brate)
    points.B.y = roundToThreeDecimals(trial.becon)
    points.C.x = roundToThreeDecimals(trial.drate)
    points.C.y = roundToThreeDecimals(trial.decon)
    updateChart()
  }
}

// Validate coordinate input
const validateCoordinate = (point, axis, event) => {
  let value = parseFloat(event.target.value)
  if (isNaN(value)) {
    value = 0
  }
  value = Math.max(0, Math.min(1, value))
  value = roundToThreeDecimals(value)
  points[point][axis] = value
  event.target.value = value.toFixed(3)
}

// ===== WATCHERS =====
// Watch for changes in points and update chart
watch(points, () => {
  updateChart()
}, { deep: true })

// Watch for changes in params and update chart
watch(params, () => {
  updateChart()
}, { deep: true })

// ===== LIFECYCLE HOOKS =====
// Handle window resize
const handleResize = () => {
  if (chartInstance) {
    chartInstance.resize()
  }
}

// Lifecycle
onMounted(() => {
  nextTick(() => {
    initChart()
    window.addEventListener('resize', handleResize)
  })
})

// Cleanup
onUnmounted(() => {
  if (chartInstance) {
    chartInstance.dispose()
  }
  window.removeEventListener('resize', handleResize)
})
</script>

<style scoped>
.app {
  height: 80vh;
  background: white;
  font-family: Arial, sans-serif;
  padding: 0.75rem;
  box-sizing: border-box;
}

.main-layout {
  display: flex;
  gap: 0.75rem;
  max-width: 1400px;
  margin: 0 auto;
  height: calc(80vh - 1.5rem);
}

.chart-container {
  flex: 0 0 50%;
  background: white;
  border: 1px solid #ddd;
  padding: 0.75rem;
  display: flex;
  flex-direction: column;
}

.chart {
  width: 100%;
  height: 0;
  padding-bottom: 75%;
  position: relative;
  border: 1px solid #ccc;
  flex: 1;
}

.chart > div {
  position: absolute;
  top: 0;
  left: 0;
  width: 100% !important;
  height: 100% !important;
}

.coordinate-controls {
  margin-top: 0.75rem;
  padding: 0.75rem;
  background: white;
  border: 1px solid #ddd;
  flex-shrink: 0;
}

.coordinate-controls h4 {
  margin: 0 0 0.75rem 0;
  color: #333;
  font-size: 1rem;
  font-weight: normal;
}

.coordinates-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 0.75rem;
}

.coordinate-item {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.coordinate-item label {
  font-weight: normal;
  color: #333;
  font-size: 0.9rem;
}

.coord-inputs {
  display: flex;
  gap: 0.5rem;
}

.input-group {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.axis-label {
  font-size: 0.75rem;
  color: #666;
  text-align: center;
  font-weight: normal;
}

.coord-input {
  padding: 0.4rem;
  border: 1px solid #ccc;
  font-size: 0.9rem;
  background: white;
  text-align: center;
}

.coord-input:focus {
  outline: none;
  border-color: #666;
}

.controls-output {
  flex: 0 0 50%;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.settings-button {
  padding: 0.5rem;
  background: #e0e0e0;
  color: #333;
  border: 1px solid #ccc;
  font-size: 0.8rem;
  cursor: pointer;
  white-space: nowrap;
}

.settings-button:hover {
  background: #d0d0d0;
}

.parameter-controls,
.generated-output {
  background: white;
  border: 1px solid #ddd;
  padding: 0.75rem;
}

.parameter-controls h3,
.generated-output h3 {
  margin: 0 0 0.75rem 0;
  color: #333;
  font-size: 1rem;
  font-weight: normal;
  border-bottom: 1px solid #ddd;
  padding-bottom: 0.5rem;
}

.control-group {
  margin-bottom: 0.75rem;
}

.control-group h4 {
  margin: 0 0 0.5rem 0;
  color: #333;
  font-size: 0.9rem;
  font-weight: normal;
}

.input-row {
  display: flex;
  align-items: center;
  margin-bottom: 0.3rem;
  gap: 0.5rem;
}

.input-row label {
  min-width: 40px;
  color: #333;
  font-size: 0.8rem;
}

.number-input {
  flex: 1;
  padding: 0.4rem;
  border: 1px solid #ccc;
  font-size: 0.8rem;
  background: white;
}

.number-input:focus {
  outline: none;
  border-color: #666;
}

.reset-button {
  width: 100%;
  padding: 0.5rem;
  background: #e0e0e0;
  color: #333;
  border: 1px solid #ccc;
  font-size: 0.8rem;
  cursor: pointer;
}

.reset-button:hover {
  background: #d0d0d0;
}

.problem-description {
  margin-bottom: 0.75rem;
}

.preset-trials {
  margin-bottom: 1rem;
}

.preset-trials h4 {
  margin: 0 0 0.5rem 0;
  color: #333;
  font-size: 0.9rem;
  font-weight: normal;
}

.trial-buttons {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
}

.trial-button {
  flex: 1;
  min-width: 100px;
  padding: 0.5rem;
  background: #f0f8ff;
  border: 2px solid #ccc;
  border-radius: 6px;
  cursor: pointer;
  text-align: center;
  transition: all 0.2s ease;
}

.trial-button:hover {
  background: #e6f3ff;
  border-color: #0066cc;
  transform: translateY(-1px);
}

.trial-name {
  font-weight: bold;
  font-size: 0.8rem;
  color: #333;
  margin-bottom: 0.2rem;
}

.trial-desc {
  font-size: 0.7rem;
  color: #666;
}

.option-cards {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.option-card {
  background: #f8f8f8;
  border: 2px solid #ddd;
  border-radius: 8px;
  padding: 0.75rem;
  text-align: center;
}

.option-header {
  font-weight: bold;
  font-size: 1.1rem;
  margin-bottom: 0.5rem;
  color: #333;
}

.option-details {
  color: #666;
  font-size: 0.9rem;
}

.option-details div {
  margin: 0.2rem 0;
}

/* Responsive design */
@media (max-width: 1200px) {
  .main-layout {
    flex-direction: column;
    height: auto;
  }
  
  .chart-container,
  .controls-output {
    flex: none;
  }
  
  .chart-container {
    order: 1;
  }
  
  .controls-output {
    order: 2;
  }
  
  .chart {
    padding-bottom: 80%;
  }
}

@media (max-width: 768px) {
  .app {
    padding: 0.5rem;
  }
  
  .main-layout {
    gap: 0.5rem;
  }
  
  .coordinates-grid {
    grid-template-columns: 1fr;
  }
  
  .coordinate-item {
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
  }
  
  .coordinate-item label {
    min-width: 80px;
  }
  
  .coord-inputs {
    max-width: 150px;
  }
  
  .chart {
    padding-bottom: 100%;
  }
}
</style>
