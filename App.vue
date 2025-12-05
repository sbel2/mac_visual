<template>
  <div class="app">
    <div class="main-layout">
      <!-- Left Column: Interactive Chart -->
      <div class="chart-container">
        <div class="chart-header">
          <div class="chart-hint" :class="{ 'shift-active': isShiftPressed }">{{ isShiftPressed ? '🔄 Triangle Move Mode Active' : 'Tip: Hold Shift while dragging to move the entire triangle' }}</div>
          <button @click="resetPoints" class="chart-reset-button">Reset</button>
        </div>
        <div ref="chartRef" class="chart" :class="{ 'shift-mode': isShiftPressed }"></div>
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
          
          <div class="preset-buttons">
            <button @click="loadSmartphonePreset" class="preset-button">
              📱 Load Smartphone Preset
            </button>
          </div>
          
          <div class="control-group">
            <h4>Domain</h4>
            <div class="input-row">
              <label>Name:</label>
              <input 
                v-model="params.domain" 
                type="text" 
                class="number-input"
                placeholder="e.g., Apartment Rental (or NA)"
                @keydown="handleInputKeydown"
              />
            </div>
          </div>
          
          <div class="control-group">
            <h4>X-Axis (Horizontal)</h4>
            <div class="input-row">
              <label>Name:</label>
              <input 
                v-model="params.xName" 
                type="text" 
                class="number-input"
                placeholder="e.g., Square Footage"
                @keydown="handleInputKeydown"
              />
            </div>
            <div class="input-row">
              <label>Unit:</label>
              <input 
                v-model="params.xUnit" 
                type="text" 
                class="number-input"
                placeholder="e.g., sq. ft."
                @keydown="handleInputKeydown"
              />
            </div>
            <div class="input-row">
              <label>Min:</label>
              <input 
                v-model.number="params.xMin" 
                type="number" 
                :step="params.xDecimals > 0 ? Math.pow(10, -params.xDecimals) : 1"
                class="number-input"
                @keydown="handleInputKeydown"
              />
            </div>
            <div class="input-row">
              <label>Max:</label>
              <input 
                v-model.number="params.xMax" 
                type="number" 
                :step="params.xDecimals > 0 ? Math.pow(10, -params.xDecimals) : 1"
                class="number-input"
                @keydown="handleInputKeydown"
              />
            </div>
            <div class="input-row">
              <label>Decimals:</label>
              <input 
                v-model.number="params.xDecimals" 
                type="number" 
                min="0"
                max="5"
                step="1"
                class="number-input"
                @keydown="handleInputKeydown"
                title="Number of decimal places (0-5)"
              />
            </div>
            <div class="input-row checkbox-row">
              <label>
                <input 
                  v-model="params.xInverse" 
                  type="checkbox"
                  class="checkbox-input"
                  @keydown="handleInputKeydown"
                  title="Press Space to toggle"
                />
                Inverse (lower is better)
              </label>
            </div>
          </div>
          
          <div class="control-group">
            <h4>Y-Axis (Vertical)</h4>
            <div class="input-row">
              <label>Name:</label>
              <input 
                v-model="params.yName" 
                type="text" 
                class="number-input"
                placeholder="e.g., Price"
                @keydown="handleInputKeydown"
              />
            </div>
            <div class="input-row">
              <label>Unit:</label>
              <input 
                v-model="params.yUnit" 
                type="text" 
                class="number-input"
                placeholder="e.g., $/month"
                @keydown="handleInputKeydown"
              />
            </div>
            <div class="input-row">
              <label>Min:</label>
              <input 
                v-model.number="params.yMin" 
                type="number" 
                :step="params.yDecimals > 0 ? Math.pow(10, -params.yDecimals) : 1"
                class="number-input"
                @keydown="handleInputKeydown"
              />
            </div>
            <div class="input-row">
              <label>Max:</label>
              <input 
                v-model.number="params.yMax" 
                type="number" 
                :step="params.yDecimals > 0 ? Math.pow(10, -params.yDecimals) : 1"
                class="number-input"
                @keydown="handleInputKeydown"
              />
            </div>
            <div class="input-row">
              <label>Decimals:</label>
              <input 
                v-model.number="params.yDecimals" 
                type="number" 
                min="0"
                max="5"
                step="1"
                class="number-input"
                @keydown="handleInputKeydown"
                title="Number of decimal places (0-5)"
              />
            </div>
            <div class="input-row checkbox-row">
              <label>
                <input 
                  v-model="params.yInverse" 
                  type="checkbox"
                  class="checkbox-input"
                  @keydown="handleInputKeydown"
                  title="Press Space to toggle"
                />
                Inverse (lower is better)
              </label>
            </div>
          </div>
          
          <button @click="resetPoints" class="reset-button">Reset Points</button>
        </div>

        <!-- Generated Output -->
        <div class="generated-output">
          <h3>{{ params.domain && params.domain.toUpperCase() !== 'NA' ? params.domain + ' Problem' : 'Problem' }}</h3>
          
          <div class="problem-description">
            <div class="option-cards">
              <div class="option-card">
                <div class="option-header">Option A</div>
                <div class="option-details">
                  <div>{{ params.xName }}: {{ transformedValues.A.x }} {{ params.xUnit }}</div>
                  <div>{{ params.yName }}: {{ transformedValues.A.y }} {{ params.yUnit }}</div>
                </div>
              </div>
              
              <div class="option-card">
                <div class="option-header">Option B</div>
                <div class="option-details">
                  <div>{{ params.xName }}: {{ transformedValues.B.x }} {{ params.xUnit }}</div>
                  <div>{{ params.yName }}: {{ transformedValues.B.y }} {{ params.yUnit }}</div>
                </div>
              </div>
              
              <div class="option-card">
                <div class="option-header">Option C</div>
                <div class="option-details">
                  <div>{{ params.xName }}: {{ transformedValues.C.x }} {{ params.xUnit }}</div>
                  <div>{{ params.yName }}: {{ transformedValues.C.y }} {{ params.yUnit }}</div>
                </div>
              </div>
            </div>
          </div>
        </div>
        
        <!-- Coordinate Controls -->
        <div class="coordinate-controls">
          <div class="coordinates-grid">
            <div class="coordinate-item">
              <label>Point A:</label>
              <div class="coord-inputs">
                <div class="input-group">
                  <label class="axis-label">{{ params.xUnit }}</label>
                  <input 
                    v-model.number="points.A.x" 
                    type="number" 
                    min="0" 
                    max="1" 
                    step="0.01"
                    class="coord-input"
                    @blur="validateCoordinate('A', 'x', $event)"
                    placeholder="0.00"
                  />
                </div>
                <div class="input-group">
                  <label class="axis-label">{{ params.yUnit }}</label>
                  <input 
                    v-model.number="points.A.y" 
                    type="number" 
                    min="0" 
                    max="1" 
                    step="0.01"
                    class="coord-input"
                    @blur="validateCoordinate('A', 'y', $event)"
                    placeholder="0.00"
                  />
                </div>
              </div>
            </div>
            
            <div class="coordinate-item">
              <label>Point B:</label>
              <div class="coord-inputs">
                <div class="input-group">
                  <label class="axis-label">{{ params.xUnit }}</label>
                  <input 
                    v-model.number="points.B.x" 
                    type="number" 
                    min="0" 
                    max="1" 
                    step="0.01"
                    class="coord-input"
                    @blur="validateCoordinate('B', 'x', $event)"
                    placeholder="0.00"
                  />
                </div>
                <div class="input-group">
                  <label class="axis-label">{{ params.yUnit }}</label>
                  <input 
                    v-model.number="points.B.y" 
                    type="number" 
                    min="0" 
                    max="1" 
                    step="0.01"
                    class="coord-input"
                    @blur="validateCoordinate('B', 'y', $event)"
                    placeholder="0.00"
                  />
                </div>
              </div>
            </div>
            
            <div class="coordinate-item">
              <label>Point C:</label>
              <div class="coord-inputs">
                <div class="input-group">
                  <label class="axis-label">{{ params.xUnit }}</label>
                  <input 
                    v-model.number="points.C.x" 
                    type="number" 
                    min="0" 
                    max="1" 
                    step="0.01"
                    class="coord-input"
                    @blur="validateCoordinate('C', 'x', $event)"
                    placeholder="0.00"
                  />
                </div>
                <div class="input-group">
                  <label class="axis-label">{{ params.yUnit }}</label>
                  <input 
                    v-model.number="points.C.y" 
                    type="number" 
                    min="0" 
                    max="1" 
                    step="0.01"
                    class="coord-input"
                    @blur="validateCoordinate('C', 'y', $event)"
                    placeholder="0.00"
                  />
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
const isShiftPressed = ref(false)
let chartInstance = null

// Points data (0-1 range for chart coordinates)
const points = reactive({
  A: { x: 0.25, y: 0.75 },
  B: { x: 0.75, y: 0.25 },
  C: { x: 0.5, y: 0.5 }
})

// Parameter controls for real-world transformation
const params = reactive({
  domain: '',
  xName: 'Square Footage',
  xUnit: 'sq. ft.',
  xMin: 300,
  xMax: 2500,
  xInverse: false,
  xDecimals: 0,
  yName: 'Price',
  yUnit: '$/month',
  yMin: 500,
  yMax: 4000,
  yInverse: true,
  yDecimals: 0
})

// ===== UTILITY FUNCTIONS =====
function transformValue(q, min, max, inverse, decimals = 0) {
  // Ensure all inputs are numbers
  const numQ = typeof q === 'number' ? q : parseFloat(q) || 0
  const numMin = typeof min === 'number' ? min : parseFloat(min) || 0
  const numMax = typeof max === 'number' ? max : parseFloat(max) || 0
  const numDecimals = typeof decimals === 'number' ? decimals : parseInt(decimals) || 0
  
  const value = inverse ? (1 - numQ) * (numMax - numMin) + numMin : numQ * (numMax - numMin) + numMin
  
  // Always return a number, not a string
  if (numDecimals > 0) {
    return parseFloat(value.toFixed(numDecimals))
  }
  return Math.round(value)
}

function roundToThreeDecimals(value) {
  return Math.round(value * 1000) / 1000
}

// ===== COMPUTED PROPERTIES =====
const transformedValues = computed(() => {
  // Ensure all point values are numbers
  const safePoints = {
    A: {
      x: typeof points.A.x === 'number' ? points.A.x : parseFloat(points.A.x) || 0,
      y: typeof points.A.y === 'number' ? points.A.y : parseFloat(points.A.y) || 0
    },
    B: {
      x: typeof points.B.x === 'number' ? points.B.x : parseFloat(points.B.x) || 0,
      y: typeof points.B.y === 'number' ? points.B.y : parseFloat(points.B.y) || 0
    },
    C: {
      x: typeof points.C.x === 'number' ? points.C.x : parseFloat(points.C.x) || 0,
      y: typeof points.C.y === 'number' ? points.C.y : parseFloat(points.C.y) || 0
    }
  }
  
  return {
    A: {
      x: transformValue(safePoints.A.x, params.xMin, params.xMax, params.xInverse, params.xDecimals),
      y: transformValue(safePoints.A.y, params.yMin, params.yMax, params.yInverse, params.yDecimals)
    },
    B: {
      x: transformValue(safePoints.B.x, params.xMin, params.xMax, params.xInverse, params.xDecimals),
      y: transformValue(safePoints.B.y, params.yMin, params.yMax, params.yInverse, params.yDecimals)
    },
    C: {
      x: transformValue(safePoints.C.x, params.xMin, params.xMax, params.xInverse, params.xDecimals),
      y: transformValue(safePoints.C.y, params.yMin, params.yMax, params.yInverse, params.yDecimals)
    }
  }
})

// ===== CHART CONFIGURATION =====
const getChartOptions = () => {
  // Get canonical A/B points for dominance buckets (matching the Python code)
  const canonicalA = { x: 0.25, y: 0.75 }
  const canonicalB = { x: 0.75, y: 0.25 }
  
  return {
    title: {
      text: params.domain && params.domain.toUpperCase() !== 'NA' ? params.domain : '',
      left: 'center',
      top: '2%',
      textStyle: {
        fontSize: 16,
        fontWeight: 'normal'
      }
    },
    legend: {
      data: ['Inferior', 'Attraction A', 'Attraction B', 'Comp/Sim', 'Superior'],
      bottom: '1%',
      itemWidth: 15,
      itemHeight: 10,
      textStyle: {
        fontSize: 9
      },
      itemGap: 8
    },
    grid: {
      left: '15%',
      right: '10%',
      bottom: '18%',
      top: '10%'
    },
    xAxis: {
      type: 'value',
      min: 0,
      max: 1,
      name: params.xUnit,
      nameLocation: 'middle',
      nameGap: 25,
      nameTextStyle: {
        fontSize: 12
      },
      axisLabel: {
        formatter: (value) => {
          const numValue = typeof value === 'number' ? value : parseFloat(value) || 0
          return numValue.toFixed(1)
        }
      }
    },
    yAxis: {
      type: 'value',
      min: 0,
      max: 1,
      name: params.yUnit,
      nameLocation: 'middle',
      nameGap: 40,
      nameTextStyle: {
        fontSize: 12
      },
      axisLabel: {
        formatter: (value) => {
          const numValue = typeof value === 'number' ? value : parseFloat(value) || 0
          return numValue.toFixed(1)
        }
      }
    },
    series: [
      // Background regions (dominance buckets)
      {
        name: 'Inferior',
        type: 'custom',
        color: 'rgb(144, 238, 144)', // lightgreen - solid color for legend
        renderItem: (params, api) => {
          const x0 = api.coord([0, 0])
          const x1 = api.coord([canonicalA.x, canonicalB.y])
          return {
            type: 'rect',
            shape: {
              x: x0[0],
              y: x1[1],
              width: x1[0] - x0[0],
              height: x0[1] - x1[1]
            },
            style: {
              fill: 'rgba(144, 238, 144, 0.5)' // lightgreen
            },
            z: -2
          }
        },
        data: [0],
        silent: true
      },
      {
        name: 'Attraction A',
        type: 'custom',
        color: 'rgb(135, 206, 250)', // light sky blue - solid color for legend
        renderItem: (params, api) => {
          const x0 = api.coord([0, canonicalB.y])
          const x1 = api.coord([canonicalA.x, canonicalA.y])
          return {
            type: 'rect',
            shape: {
              x: x0[0],
              y: x1[1],
              width: x1[0] - x0[0],
              height: x0[1] - x1[1]
            },
            style: {
              fill: 'rgba(135, 206, 250, 0.5)' // light sky blue
            },
            z: -2
          }
        },
        data: [0],
        silent: true
      },
      {
        name: 'Comp/Sim',
        type: 'custom',
        color: 'rgb(255, 255, 0)', // yellow - solid color for legend
        renderItem: (params, api) => {
          const x0 = api.coord([canonicalA.x, canonicalB.y])
          const x1 = api.coord([canonicalB.x, canonicalA.y])
          return {
            type: 'rect',
            shape: {
              x: x0[0],
              y: x1[1],
              width: x1[0] - x0[0],
              height: x0[1] - x1[1]
            },
            style: {
              fill: 'rgba(255, 255, 0, 0.5)' // yellow
            },
            z: -2
          }
        },
        data: [0],
        silent: true
      },
      {
        name: 'Attraction B',
        type: 'custom',
        color: 'rgb(70, 130, 180)', // steel blue - solid color for legend
        renderItem: (params, api) => {
          const x0 = api.coord([canonicalA.x, 0])
          const x1 = api.coord([canonicalB.x, canonicalB.y])
          return {
            type: 'rect',
            shape: {
              x: x0[0],
              y: x1[1],
              width: x1[0] - x0[0],
              height: x0[1] - x1[1]
            },
            style: {
              fill: 'rgba(70, 130, 180, 0.5)' // steel blue
            },
            z: -2
          }
        },
        data: [0],
        silent: true
      },
      {
        name: 'Superior',
        type: 'custom',
        color: 'rgb(250, 128, 114)', // salmon - solid color for legend
        renderItem: (params, api) => {
          const x0 = api.coord([canonicalB.x, canonicalA.y])
          const x1 = api.coord([1, 1])
          return {
            type: 'rect',
            shape: {
              x: x0[0],
              y: x1[1],
              width: x1[0] - x0[0],
              height: x0[1] - x1[1]
            },
            style: {
              fill: 'rgba(250, 128, 114, 0.5)' // salmon
            },
            z: -2
          }
        },
        data: [0],
        silent: true
      },
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
        disabled: true
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
        color: isShiftPressed.value ? '#0066cc' : '#666',
        width: isShiftPressed.value ? 3 : 2
      },
      areaStyle: {
        color: isShiftPressed.value ? 'rgba(0, 102, 204, 0.2)' : 'rgba(200, 200, 200, 0.3)'
      },
      symbol: 'none',
      silent: true
    }
  ],
  tooltip: {
    trigger: 'item',
    formatter: (p) => {
      if (p.seriesType === 'scatter') {
        const point = p.data
        const xVal = typeof point.value[0] === 'number' ? point.value[0] : parseFloat(point.value[0]) || 0
        const yVal = typeof point.value[1] === 'number' ? point.value[1] : parseFloat(point.value[1]) || 0
        return `${point.name}<br/>${params.xUnit}: ${xVal.toFixed(3)}<br/>${params.yUnit}: ${yVal.toFixed(3)}`
      }
      return ''
    }
  }
  }
}

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
    let isDraggingTriangle = false
    let lastMousePos = null
    
    // Helper function to check if point is inside triangle
    const isPointInTriangle = (px, py, ax, ay, bx, by, cx, cy) => {
      const v0x = cx - ax
      const v0y = cy - ay
      const v1x = bx - ax
      const v1y = by - ay
      const v2x = px - ax
      const v2y = py - ay
      
      const dot00 = v0x * v0x + v0y * v0y
      const dot01 = v0x * v1x + v0y * v1y
      const dot02 = v0x * v2x + v0y * v2y
      const dot11 = v1x * v1x + v1y * v1y
      const dot12 = v1x * v2x + v1y * v2y
      
      const invDenom = 1 / (dot00 * dot11 - dot01 * dot01)
      const u = (dot11 * dot02 - dot01 * dot12) * invDenom
      const v = (dot00 * dot12 - dot01 * dot02) * invDenom
      
      return (u >= 0) && (v >= 0) && (u + v <= 1)
    }
    
    chartInstance.getZr().on('mousedown', (e) => {
      const pointInPixel = [e.offsetX, e.offsetY]
      const pointInGrid = chartInstance.convertFromPixel('grid', pointInPixel)
      
      if (pointInGrid && pointInGrid[0] >= 0 && pointInGrid[0] <= 1 && 
          pointInGrid[1] >= 0 && pointInGrid[1] <= 1) {
        
        // Check if shift is pressed and point is inside triangle
        if (e.event.shiftKey && isPointInTriangle(
          pointInGrid[0], pointInGrid[1],
          points.A.x, points.A.y,
          points.B.x, points.B.y,
          points.C.x, points.C.y
        )) {
          // Enable triangle dragging from anywhere inside triangle
          isDragging = true
          isDraggingTriangle = true
          dragPointIndex = 0 // Use any point as reference
          lastMousePos = pointInGrid
        } else {
          // Find which point is closest for individual point dragging
          const distances = [
            Math.sqrt(Math.pow(pointInGrid[0] - points.A.x, 2) + Math.pow(pointInGrid[1] - points.A.y, 2)),
            Math.sqrt(Math.pow(pointInGrid[0] - points.B.x, 2) + Math.pow(pointInGrid[1] - points.B.y, 2)),
            Math.sqrt(Math.pow(pointInGrid[0] - points.C.x, 2) + Math.pow(pointInGrid[1] - points.C.y, 2))
          ]
          
          const minDistance = Math.min(...distances)
          if (minDistance < 0.15) {
            dragPointIndex = distances.indexOf(minDistance)
            isDragging = true
            isDraggingTriangle = false
            lastMousePos = pointInGrid
          }
        }
      }
    })
    
    chartInstance.getZr().on('mousemove', (e) => {
      if (isDragging && dragPointIndex >= 0) {
        const pointInPixel = [e.offsetX, e.offsetY]
        const pointInGrid = chartInstance.convertFromPixel('grid', pointInPixel)
        
        if (pointInGrid) {
          if (isDraggingTriangle && lastMousePos) {
            // Move entire triangle
            const deltaX = pointInGrid[0] - lastMousePos[0]
            const deltaY = pointInGrid[1] - lastMousePos[1]
            
            // Calculate new positions
            const newA = {
              x: points.A.x + deltaX,
              y: points.A.y + deltaY
            }
            const newB = {
              x: points.B.x + deltaX,
              y: points.B.y + deltaY
            }
            const newC = {
              x: points.C.x + deltaX,
              y: points.C.y + deltaY
            }
            
            // Check if all points stay within bounds
            if (newA.x >= 0 && newA.x <= 1 && newA.y >= 0 && newA.y <= 1 &&
                newB.x >= 0 && newB.x <= 1 && newB.y >= 0 && newB.y <= 1 &&
                newC.x >= 0 && newC.x <= 1 && newC.y >= 0 && newC.y <= 1) {
              points.A.x = roundToThreeDecimals(newA.x)
              points.A.y = roundToThreeDecimals(newA.y)
              points.B.x = roundToThreeDecimals(newB.x)
              points.B.y = roundToThreeDecimals(newB.y)
              points.C.x = roundToThreeDecimals(newC.x)
              points.C.y = roundToThreeDecimals(newC.y)
              lastMousePos = pointInGrid
            }
          } else if (pointInGrid[0] >= 0 && pointInGrid[0] <= 1 && 
                     pointInGrid[1] >= 0 && pointInGrid[1] <= 1) {
            // Move single point
            const pointNames = ['A', 'B', 'C']
            const pointName = pointNames[dragPointIndex]
            points[pointName].x = roundToThreeDecimals(Math.max(0, Math.min(1, pointInGrid[0])))
            points[pointName].y = roundToThreeDecimals(Math.max(0, Math.min(1, pointInGrid[1])))
          }
        }
      }
    })
    
    chartInstance.getZr().on('mouseup', () => {
      isDragging = false
      dragPointIndex = -1
      isDraggingTriangle = false
      lastMousePos = null
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
// Load smartphone preset
const loadSmartphonePreset = () => {
  // Set domain
  params.domain = 'Smartphone'
  
  // Set X-axis (Price)
  params.xName = 'Price'
  params.xUnit = '$'
  params.xMin = 450
  params.xMax = 1050
  params.xDecimals = 0
  params.xInverse = true
  
  // Set Y-axis (Battery life)
  params.yName = 'Battery life'
  params.yUnit = 'hour'
  params.yMin = 4
  params.yMax = 11
  params.yDecimals = 0
  params.yInverse = false
  
  // Reset points to default
  points.A = { x: 0.25, y: 0.75 }
  points.B = { x: 0.75, y: 0.25 }
  points.C = { x: 0.5, y: 0.5 }
  
  updateChart()
}

// Reset points to initial positions
const resetPoints = () => {
  points.A = { x: 0.25, y: 0.75 }
  points.B = { x: 0.750, y: 0.25 }
  points.C = { x: 0.5, y: 0.5 }
  updateChart()
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
  const numValue = typeof value === 'number' ? value : parseFloat(value) || 0
  event.target.value = numValue.toFixed(3)
}

// ===== LOCAL STORAGE =====
// Save settings to localStorage
const saveToLocalStorage = () => {
  try {
    localStorage.setItem('mac_visual_points', JSON.stringify(points))
    localStorage.setItem('mac_visual_params', JSON.stringify(params))
  } catch (e) {
    console.error('Failed to save to localStorage:', e)
  }
}

// Load settings from localStorage
const loadFromLocalStorage = () => {
  try {
    const savedPoints = localStorage.getItem('mac_visual_points')
    const savedParams = localStorage.getItem('mac_visual_params')
    
    if (savedPoints) {
      const parsed = JSON.parse(savedPoints)
      // Validate and ensure all point values are numbers
      if (parsed.A) {
        points.A.x = typeof parsed.A.x === 'number' ? parsed.A.x : parseFloat(parsed.A.x) || 0.25
        points.A.y = typeof parsed.A.y === 'number' ? parsed.A.y : parseFloat(parsed.A.y) || 0.75
      }
      if (parsed.B) {
        points.B.x = typeof parsed.B.x === 'number' ? parsed.B.x : parseFloat(parsed.B.x) || 0.75
        points.B.y = typeof parsed.B.y === 'number' ? parsed.B.y : parseFloat(parsed.B.y) || 0.25
      }
      if (parsed.C) {
        points.C.x = typeof parsed.C.x === 'number' ? parsed.C.x : parseFloat(parsed.C.x) || 0.5
        points.C.y = typeof parsed.C.y === 'number' ? parsed.C.y : parseFloat(parsed.C.y) || 0.5
      }
    }
    
    if (savedParams) {
      const parsed = JSON.parse(savedParams)
      // Validate and ensure all param values are correct types
      if (parsed.domain !== undefined) params.domain = String(parsed.domain || '')
      if (parsed.xName !== undefined) params.xName = String(parsed.xName || 'Square Footage')
      if (parsed.xUnit !== undefined) params.xUnit = String(parsed.xUnit || 'sq. ft.')
      if (parsed.xMin !== undefined) params.xMin = typeof parsed.xMin === 'number' ? parsed.xMin : parseFloat(parsed.xMin) || 300
      if (parsed.xMax !== undefined) params.xMax = typeof parsed.xMax === 'number' ? parsed.xMax : parseFloat(parsed.xMax) || 2500
      if (parsed.xInverse !== undefined) params.xInverse = Boolean(parsed.xInverse)
      if (parsed.xDecimals !== undefined) params.xDecimals = typeof parsed.xDecimals === 'number' ? parsed.xDecimals : parseInt(parsed.xDecimals) || 0
      if (parsed.yName !== undefined) params.yName = String(parsed.yName || 'Price')
      if (parsed.yUnit !== undefined) params.yUnit = String(parsed.yUnit || '$/month')
      if (parsed.yMin !== undefined) params.yMin = typeof parsed.yMin === 'number' ? parsed.yMin : parseFloat(parsed.yMin) || 500
      if (parsed.yMax !== undefined) params.yMax = typeof parsed.yMax === 'number' ? parsed.yMax : parseFloat(parsed.yMax) || 4000
      if (parsed.yInverse !== undefined) params.yInverse = Boolean(parsed.yInverse)
      if (parsed.yDecimals !== undefined) params.yDecimals = typeof parsed.yDecimals === 'number' ? parsed.yDecimals : parseInt(parsed.yDecimals) || 0
    }
  } catch (e) {
    console.error('Failed to load from localStorage:', e)
  }
}

// Handle keyboard navigation in settings inputs
const handleInputKeydown = (event) => {
  // For checkboxes, Space toggles and Enter moves to next field
  if (event.target.type === 'checkbox') {
    if (event.key === 'Enter' || event.key === 'ArrowDown') {
      event.preventDefault()
      const inputs = Array.from(document.querySelectorAll('.parameter-controls .number-input, .parameter-controls .checkbox-input'))
      const currentIndex = inputs.indexOf(event.target)
      if (currentIndex >= 0 && currentIndex < inputs.length - 1) {
        inputs[currentIndex + 1].focus()
      }
    } else if (event.key === 'ArrowUp') {
      event.preventDefault()
      const inputs = Array.from(document.querySelectorAll('.parameter-controls .number-input, .parameter-controls .checkbox-input'))
      const currentIndex = inputs.indexOf(event.target)
      if (currentIndex > 0) {
        inputs[currentIndex - 1].focus()
      }
    }
    // Space key is handled natively by checkbox - no need to prevent default
  } else {
    // For text/number inputs
    if (event.key === 'Enter' || event.key === 'ArrowDown') {
      event.preventDefault()
      const inputs = Array.from(document.querySelectorAll('.parameter-controls .number-input, .parameter-controls .checkbox-input'))
      const currentIndex = inputs.indexOf(event.target)
      if (currentIndex >= 0 && currentIndex < inputs.length - 1) {
        inputs[currentIndex + 1].focus()
      }
    } else if (event.key === 'ArrowUp') {
      event.preventDefault()
      const inputs = Array.from(document.querySelectorAll('.parameter-controls .number-input, .parameter-controls .checkbox-input'))
      const currentIndex = inputs.indexOf(event.target)
      if (currentIndex > 0) {
        inputs[currentIndex - 1].focus()
      }
    }
  }
}

// ===== WATCHERS =====
// Watch for changes in points and update chart
watch(points, () => {
  updateChart()
  saveToLocalStorage()
}, { deep: true })

// Watch for changes in params and update chart
watch(params, () => {
  updateChart()
  saveToLocalStorage()
}, { deep: true })

// Watch for shift key state changes and update chart
watch(isShiftPressed, () => {
  updateChart()
})

// ===== LIFECYCLE HOOKS =====
// Handle window resize
const handleResize = () => {
  if (chartInstance) {
    chartInstance.resize()
  }
}

// Handle shift key press
const handleKeyDown = (e) => {
  if (e.key === 'Shift') {
    // Don't activate shift mode if user is typing in an input field
    const activeElement = document.activeElement
    if (activeElement && (activeElement.tagName === 'INPUT' || activeElement.tagName === 'TEXTAREA')) {
      return
    }
    isShiftPressed.value = true
  }
}

// Handle shift key release
const handleKeyUp = (e) => {
  if (e.key === 'Shift') {
    isShiftPressed.value = false
  }
}

// Lifecycle
onMounted(() => {
  loadFromLocalStorage()
  nextTick(() => {
    initChart()
    window.addEventListener('resize', handleResize)
    window.addEventListener('keydown', handleKeyDown)
    window.addEventListener('keyup', handleKeyUp)
  })
})

// Cleanup
onUnmounted(() => {
  if (chartInstance) {
    chartInstance.dispose()
  }
  window.removeEventListener('resize', handleResize)
  window.removeEventListener('keydown', handleKeyDown)
  window.removeEventListener('keyup', handleKeyUp)
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
  padding: 0.75rem;
  display: flex;
  flex-direction: column;
}

.chart-header {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 0.5rem;
}

.chart-hint {
  flex: 1;
  font-size: 0.75rem;
  color: #666;
  text-align: center;
  padding: 0.25rem;
  background: #f9f9f9;
  border-radius: 4px;
  transition: all 0.2s ease;
}

.chart-hint.shift-active {
  background: #e6f3ff;
  color: #0066cc;
  font-weight: bold;
  border: 1px solid #0066cc;
}

.chart-reset-button {
  padding: 0.25rem 0.75rem;
  background: #f0f0f0;
  color: #333;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 0.75rem;
  cursor: pointer;
  white-space: nowrap;
  transition: all 0.2s ease;
}

.chart-reset-button:hover {
  background: #e0e0e0;
  border-color: #999;
}

.chart {
  width: 100%;
  aspect-ratio: 1 / 1;
  position: relative;
  border: 1px solid #ccc;
  transition: all 0.2s ease;
}

.chart.shift-mode {
  border-color: #0066cc;
  box-shadow: 0 0 15px rgba(0, 102, 204, 0.3);
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

.problem-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 0.75rem;
  border-bottom: 1px solid #ddd;
  padding-bottom: 0.5rem;
}

.problem-header h3 {
  margin: 0;
  border-bottom: none;
  padding-bottom: 0;
}

.decimals-control {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.decimals-control label {
  font-size: 0.8rem;
  color: #666;
}

.decimals-input {
  width: 50px;
  padding: 0.25rem;
  border: 1px solid #ccc;
  font-size: 0.8rem;
  text-align: center;
}

.decimals-input:focus {
  outline: none;
  border-color: #666;
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

.checkbox-row {
  margin-top: 0.5rem;
}

.checkbox-row label {
  min-width: auto;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  cursor: pointer;
}

.checkbox-input {
  cursor: pointer;
}

.checkbox-input:focus {
  outline: 2px solid #0066cc;
  outline-offset: 2px;
}

.checkbox-row label:has(.checkbox-input:focus)::after {
  content: ' (Press Space to toggle)';
  color: #999;
  font-size: 0.75rem;
  font-style: italic;
  margin-left: 0.5rem;
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

.preset-buttons {
  margin-bottom: 1rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid #ddd;
}

.preset-button {
  width: 100%;
  padding: 0.6rem;
  background: #4CAF50;
  color: white;
  border: 1px solid #45a049;
  border-radius: 4px;
  font-size: 0.85rem;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.2s ease;
}

.preset-button:hover {
  background: #45a049;
  transform: translateY(-1px);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.preset-button:active {
  transform: translateY(0);
}

.problem-description {
  margin-bottom: 0.75rem;
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
