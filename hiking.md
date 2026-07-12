---
layout: page
title: Hiking
permalink: /hiking/
---

<div class="leaf">
  <h1 class="chapter-title sc">Hiking</h1>
  <p class="dek">a field log of trails traced by hand</p>

  <div id="map"></div>
</div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />

<style>
.map-loading {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: var(--paper-tint);
  color: var(--ink);
  padding: 10px 20px;
  border-radius: 3px;
  z-index: 1000;
}
</style>

<div class="hike-list">
  <p class="dek" style="margin-top: 2rem;">click a trail below to find it on the map</p>
  <div id="hike-list-entries"></div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const map = L.map('map').setView([37.7013197, -122.217696], 9);

  const terrainLayer = L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Terrain_Base/MapServer/tile/{z}/{y}/{x}', {
    attribution: 'Tiles &copy; Esri &mdash; Source: USGS, Esri, TANA, DeLorme, and NPS',
    maxZoom: 13
  }).addTo(map);

  const loadingIndicator = document.createElement('div');
  loadingIndicator.className = 'map-loading';
  loadingIndicator.textContent = 'Loading hiking trails...';
  document.getElementById('map').appendChild(loadingIndicator);

  const entriesEl = document.getElementById('hike-list-entries');

  fetch('{{ site.baseurl }}/data/hikes.json')
    .then(response => {
      if (!response.ok) {
        throw new Error('Network response was not ok: ' + response.statusText);
      }
      return response.json();
    })
    .then(hikingTrails => {
      loadingIndicator.remove();

      const trailColor = getComputedStyle(document.documentElement).getPropertyValue('--trail-color').trim();

      hikingTrails.forEach(trail => {
        const path = trail.path.map(point => L.latLng(point[0], point[1]));

        const polyline = L.polyline(path, {
          color: trailColor,
          weight: 4,
          opacity: 1.0
        }).addTo(map);

        const popupContent = `
          <div class="trail-popup">
            <h3>${trail.name}</h3>
            <div class="trail-info"><strong>Distance:</strong> ${trail.distance}</div>
            <div class="trail-info"><strong>Elevation Gain:</strong> ${trail.elevation}</div>
            <div class="trail-info"><strong>Difficulty:</strong> ${trail.difficulty}</div>
            <div class="trail-info"><strong>Notes:</strong> ${trail.notes}</div>
          </div>
        `;
        polyline.bindPopup(popupContent);

        polyline.on('mouseover', function() { this.setStyle({ weight: 8 }); });
        polyline.on('mouseout', function() { this.setStyle({ weight: 4 }); });

        const noteEl = document.createElement('div');
        noteEl.className = 'field-note';

        const nameEl = document.createElement('h3');
        nameEl.className = 'trail-name';
        nameEl.textContent = trail.name;
        nameEl.tabIndex = 0;
        nameEl.setAttribute('role', 'button');
        nameEl.addEventListener('click', () => {
          map.fitBounds(polyline.getBounds(), { padding: [24, 24] });
          polyline.openPopup(polyline.getBounds().getCenter());
        });
        nameEl.addEventListener('keydown', (e) => {
          if (e.key === 'Enter' || e.key === ' ') { e.preventDefault(); nameEl.click(); }
        });

        const statsEl = document.createElement('p');
        statsEl.className = 'trail-stats';
        statsEl.innerHTML = `${trail.distance}<span class="sep">&middot;</span>${trail.elevation} gain<span class="sep">&middot;</span>${trail.difficulty}`;

        const noteText = document.createElement('p');
        noteText.className = 'trail-note';
        noteText.textContent = trail.notes;

        noteEl.appendChild(nameEl);
        noteEl.appendChild(statsEl);
        noteEl.appendChild(noteText);
        entriesEl.appendChild(noteEl);
      });
    })
    .catch(error => {
      console.error('Error loading hiking trails:', error);
      loadingIndicator.textContent = 'Error loading hiking trails. Please try again later.';
    });
});
</script>
