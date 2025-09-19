# Interactive Trade-off Visualization Tool

A clean, practical Vue 3 application for visualizing trade-offs between three options using an interactive 2D chart with draggable points.

## Features

- **Interactive 2D Chart**: Drag points A, B, and C to explore different decision scenarios
- **Real-time Updates**: Problem options update automatically as you move points
- **Precise Coordinates**: Direct coordinate input with 3-decimal precision
- **Parameter Controls**: Collapsible settings to adjust square footage and price ranges
- **Clean Interface**: Minimal design focused on functionality

## Tech Stack

- **Framework**: Vue 3 with Composition API (`<script setup>`)
- **Build Tool**: Vite
- **Charting**: Apache ECharts
- **Styling**: Plain CSS

## Installation

1. Install dependencies:
```bash
npm install
```

2. Start the development server:
```bash
npm run dev
```

3. Open your browser to `http://localhost:3000`

## Usage

1. **Drag Points**: Click and drag the colored points (A, B, C) on the chart
2. **Direct Input**: Enter exact coordinates in the input fields below the chart
3. **Adjust Parameters**: Click "Show Settings" to modify min/max ranges
4. **View Results**: The problem options update in real-time on the right panel

## Project Structure

```
├── App.vue          # Main application component
├── main.js          # Application entry point
├── index.html       # HTML template
├── package.json     # Dependencies and scripts
├── vite.config.js   # Vite configuration
└── README.md        # This file
```

## Building for Production

```bash
npm run build
```

The built files will be in the `dist` directory.
