---
layout: page
title: Hiking
permalink: /hiking/
---

<div id="map" style="height: 600px; width: 100%;"></div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />

<style>
.trail-popup h3 {
  margin-top: 0;
  margin-bottom: 10px;
}
.trail-info {
  margin-bottom: 5px;
}
.loading-indicator {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: rgba(255, 255, 255, 0.8);
  padding: 10px 20px;
  border-radius: 5px;
  z-index: 1000;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}
</style>

<script>
document.addEventListener('DOMContentLoaded', function() {
  // Initialize the map centered on the SF Bay Area
  const map = L.map('map').setView([37.7749, -122.4194], 9);
  
  // Add ESRI Terrain tile layer (focused on terrain and elevation)
  const terrainLayer = L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Terrain_Base/MapServer/tile/{z}/{y}/{x}', {
    attribution: 'Tiles &copy; Esri &mdash; Source: USGS, Esri, TANA, DeLorme, and NPS',
    maxZoom: 13
  }).addTo(map);
  
  // Create a loading indicator
  const loadingIndicator = document.createElement('div');
  loadingIndicator.className = 'loading-indicator';
  loadingIndicator.textContent = 'Loading hiking trails...';
  document.getElementById('map').appendChild(loadingIndicator);
  
  // Load hiking data from JSON file
  fetch('{{ site.baseurl }}/map_data/hikes.json')
    .then(response => {
      if (!response.ok) {
        throw new Error('Network response was not ok: ' + response.statusText);
      }
      return response.json();
    })
    .then(hikingTrails => {
      // Remove loading indicator
      loadingIndicator.remove();
      
      // Add the hiking trails to the map
      hikingTrails.forEach(trail => {
        // Convert path to Leaflet LatLng objects
        const path = trail.path.map(point => L.latLng(point[0], point[1]));
        
        // Create a random color for each trail
        const colors = ['#FF5733', '#33A8FF', '#8C33FF', '#33FF57', '#FF33A8'];
        const color = colors[Math.floor(Math.random() * colors.length)];
        
        // Create a polyline for the trail
        const polyline = L.polyline(path, {
          color: color,
          weight: 5,
          opacity: 0.8
        }).addTo(map);
        
        // Create popup content
        const popupContent = `
          <div class="trail-popup">
            <h3>${trail.name}</h3>
            <div class="trail-info"><strong>Distance:</strong> ${trail.distance}</div>
            <div class="trail-info"><strong>Elevation Gain:</strong> ${trail.elevation}</div>
            <div class="trail-info"><strong>Difficulty:</strong> ${trail.difficulty}</div>
            <div class="trail-info"><strong>Notes:</strong> ${trail.notes}</div>
          </div>
        `;
        
        // Bind popup to the polyline
        polyline.bindPopup(popupContent);
        
        // Add hover effect
        polyline.on('mouseover', function() {
          this.setStyle({
            weight: 8,
            opacity: 1
          });
        });
        
        polyline.on('mouseout', function() {
          this.setStyle({
            weight: 5,
            opacity: 0.8
          });
        });
      });
    })
    .catch(error => {
      console.error('Error loading hiking trails:', error);
      loadingIndicator.textContent = 'Error loading hiking trails. Please try again later.';
      loadingIndicator.style.backgroundColor = 'rgba(255, 100, 100, 0.8)';
    });
});
</script>

## Hiking Log

This page displays a collection of trails I've hiked throughout the San Francisco Bay Area. Hover over a trail on the map to see details, and click to view more information about each hike.

In the future, I plan to expand this map with more hikes and additional features:
- Photos from each hike
- Filtering by difficulty, length, and region
- Seasonal recommendations
- Links to trail resources

Stay tuned as I add more hikes and improve this visualization!
