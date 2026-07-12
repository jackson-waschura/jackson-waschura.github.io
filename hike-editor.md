---
layout: page
title: Hike Editor
permalink: /hike-editor/
---

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />

<style>
/* This page is a wider workbench than the site's usual prose column
   (.leaf tops out at 50rem, too narrow for a map + sidebar), so it
   gets its own wrapper — same padding rhythm as .leaf, just roomier.
   Tile retinting for #map is already handled globally by site.css's
   .leaflet-tile-pane img rule, so it isn't repeated here. */
.editor-wrap {
  max-width: 68rem;
  margin: 0 auto;
  padding: 3.2rem 1.4rem 3.6rem;
}

.editor-layout {
  display: flex;
  flex-wrap: wrap;
  gap: 1.5rem;
  align-items: flex-start;
}

#map {
  flex: 3 1 480px;
  min-height: 70vh;
  height: 70vh;
  border: 1px solid var(--rule);
  border-radius: 3px;
}

.editor-sidebar {
  flex: 1 1 320px;
  min-width: 280px;
  max-width: 420px;
  background-color: var(--paper-tint);
  border: 1px solid var(--rule);
  border-radius: 3px;
  padding: 1.5rem 1.6rem;
  color: var(--ink-soft);
}

.editor-sidebar h3 {
  color: var(--ink);
  margin-top: 0;
  margin-bottom: 0.6rem;
  padding-bottom: 0.4rem;
  font-size: 1rem;
  font-weight: 600;
  border-bottom: 1px solid var(--rule);
}

.editor-section {
  margin-bottom: 1.5rem;
}

.editor-section:last-child {
  margin-bottom: 0;
}

.editor-field {
  margin-bottom: 0.75rem;
}

.editor-field label {
  display: block;
  font-weight: 600;
  margin-bottom: 0.3rem;
  color: var(--ink-soft);
  font-size: 0.92rem;
}

.editor-field input[type="text"],
.editor-field select,
.editor-field textarea {
  width: 100%;
  box-sizing: border-box;
  padding: 0.4rem 0.55rem;
  border-radius: 2px;
  border: 1px solid var(--rule);
  background-color: var(--paper);
  color: var(--ink);
  font-family: inherit;
  font-size: 0.95em;
}

.editor-field textarea {
  resize: vertical;
}

.editor-sidebar input[type="radio"] {
  accent-color: var(--green);
}

.field-with-computed {
  display: flex;
  gap: 0.45rem;
  align-items: center;
}

.field-with-computed input {
  flex: 1 1 auto;
}

.reset-btn {
  flex: 0 0 auto;
  cursor: pointer;
  background-color: var(--paper);
  color: var(--ink-soft);
  border: 1px solid var(--rule);
  border-radius: 2px;
  padding: 0.4rem 0.6rem;
  font-family: inherit;
  font-size: 0.95em;
  line-height: 1;
}

.reset-btn:hover {
  color: var(--green);
  border-color: var(--green);
}

.computed-label {
  font-size: 0.8em;
  font-style: italic;
  color: var(--ink-soft);
  margin-top: 0.25rem;
}

.path-type-options {
  display: flex;
  flex-wrap: wrap;
  gap: 0.7rem;
}

.path-type-options label {
  font-weight: normal;
  display: flex;
  align-items: center;
  gap: 0.35rem;
  color: var(--ink);
}

.editor-buttons {
  display: flex;
  flex-wrap: wrap;
  gap: 0.55rem;
  margin-bottom: 0.55rem;
}

.editor-buttons.follow-up-buttons {
  margin-top: 0.45rem;
}

.editor-actions {
  margin-top: 0.55rem;
}

.editor-buttons button,
.editor-actions button {
  cursor: pointer;
  background-color: var(--green);
  color: var(--paper);
  border: 1px solid var(--green);
  border-radius: 2px;
  padding: 0.5rem 0.8rem;
  font-family: inherit;
  font-weight: 600;
  font-size: 0.9em;
}

.editor-buttons button:hover:not(:disabled),
.editor-actions button:hover:not(:disabled) {
  background-color: var(--ink);
  border-color: var(--ink);
}

.editor-buttons button:disabled,
.editor-actions button:disabled {
  background-color: var(--paper-tint);
  color: var(--ink-soft);
  border-color: var(--rule);
  cursor: not-allowed;
}

.secondary-btn {
  background-color: var(--paper) !important;
  color: var(--ink) !important;
  border: 1px solid var(--rule) !important;
}

.secondary-btn:hover:not(:disabled) {
  color: var(--green) !important;
  border-color: var(--green) !important;
}

.hint-text {
  font-size: 0.8em;
  color: var(--ink-soft);
  margin-top: 0.35rem;
}

.error-text {
  /* Semantic error accent — a muted rust kept in the parchment's warm
     family rather than a clashing stock red. */
  font-size: 0.8em;
  color: #8C2F1F;
  margin-top: 0.35rem;
}

.copied-flash {
  color: var(--green);
  font-weight: 600;
}

#json-preview {
  width: 100%;
  box-sizing: border-box;
  min-height: 220px;
  background-color: var(--paper);
  color: var(--ink);
  border: 1px solid var(--rule);
  border-radius: 2px;
  padding: 0.6rem;
  font-family: "SFMono-Regular", Consolas, "Liberation Mono", Menlo, monospace;
  font-size: 0.8em;
  white-space: pre;
}

.usage-hint {
  margin-top: 1.2rem;
  font-size: 0.9em;
  font-style: italic;
  color: var(--ink-soft);
}

.trail-pin-icon {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  background-color: var(--trail-color);
  border: 2px solid var(--paper);
}

.stat-count {
  font-size: 0.85em;
  color: var(--ink-soft);
  margin-top: 0.3rem;
}

@media (max-width: 640px) {
  .editor-sidebar {
    padding: 1.2rem 1.3rem;
  }
}
</style>

<div class="editor-wrap">
  <h1 class="chapter-title sc">Hike Editor</h1>
  <p class="dek">trace a route, then paste the record into data/hikes.json</p>

  <div class="editor-layout">
    <div id="map"></div>

    <div class="editor-sidebar">
      <div class="editor-section">
        <h3 class="sc">Route</h3>
        <div class="editor-buttons">
          <button type="button" id="undo-pin-btn" class="secondary-btn">Undo last pin</button>
          <button type="button" id="clear-pins-btn" class="secondary-btn">Clear all pins</button>
        </div>
        <div class="stat-count" id="pin-count">0 pins placed</div>

        <div class="editor-field">
          <label for="path-type-select">Path type</label>
          <div class="path-type-options">
            <label><input type="radio" name="path-type" value="linear"> Linear</label>
            <label><input type="radio" name="path-type" value="out-and-back"> Out-and-back</label>
            <label><input type="radio" name="path-type" value="loop" checked> Loop</label>
          </div>
        </div>
      </div>

      <div class="editor-section">
        <h3 class="sc">Details</h3>
        <div class="editor-field">
          <label for="name-input">Name</label>
          <input type="text" id="name-input" placeholder="e.g. Mount Tam Loop">
        </div>

        <div class="editor-field">
          <label for="difficulty-select">Difficulty</label>
          <select id="difficulty-select">
            <!-- options populated below via JS from a hardcoded list; keep in sync with the difficulty values used in data/hikes.json -->
          </select>
        </div>

        <div class="editor-field">
          <label for="distance-input">Distance</label>
          <div class="field-with-computed">
            <input type="text" id="distance-input" placeholder="e.g. 3.8 miles">
            <button type="button" class="reset-btn" id="distance-reset-btn" title="Reset to computed value">&#8634;</button>
          </div>
          <div class="computed-label" id="distance-computed-label">computed: &ndash;</div>
          <div class="error-text" id="distance-error" style="display: none;"></div>
        </div>

        <div class="editor-field">
          <label for="elevation-input">Elevation Gain</label>
          <div class="field-with-computed">
            <input type="text" id="elevation-input" placeholder="e.g. 1,600 ft">
            <button type="button" class="reset-btn" id="elevation-reset-btn" title="Reset to computed value">&#8634;</button>
          </div>
          <div class="computed-label" id="elevation-computed-label">computed: &ndash;</div>
          <div class="editor-buttons follow-up-buttons">
            <button type="button" id="fetch-elevation-btn" class="secondary-btn">Fetch elevation</button>
          </div>
          <div class="error-text" id="elevation-error" style="display: none;"></div>
        </div>

        <div class="editor-field">
          <label for="notes-input">Notes</label>
          <textarea id="notes-input" rows="4" placeholder="Notes about the hike..."></textarea>
        </div>
      </div>

      <div class="editor-section">
        <h3 class="sc">JSON Preview</h3>
        <textarea id="json-preview" readonly></textarea>
        <div class="editor-actions">
          <button type="button" id="copy-json-btn">Copy JSON to clipboard</button>
          <button type="button" id="download-json-btn">Download JSON</button>
        </div>
        <div class="hint-text" id="export-hint">Add at least 2 pins to enable copy/download.</div>
        <div class="hint-text">Paste this object into the array in <code>data/hikes.json</code> (remember a trailing comma if it's not the last entry), then commit.</div>
      </div>
    </div>
  </div>

  <p class="usage-hint">
    <strong>Usage:</strong> Left-click drag to pan, scroll to zoom. Right-click empty map to add a pin, right-click a pin to remove it, left-click drag a pin to move it.
  </p>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
  // ---------------------------------------------------------------------
  // Map setup
  // ---------------------------------------------------------------------
  const map = L.map('map').setView([37.7013197, -122.217696], 11);

  L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Topo_Map/MapServer/tile/{z}/{y}/{x}', {
    attribution: 'Tiles &copy; Esri',
    maxZoom: 19
  }).addTo(map);

  const trailColor = getComputedStyle(document.documentElement).getPropertyValue('--trail-color').trim() || '#f9ae45';

  // ---------------------------------------------------------------------
  // Reference layer: existing hikes, dimmed and non-interactive
  // ---------------------------------------------------------------------
  fetch('{{ site.baseurl }}/data/hikes.json')
    .then(response => {
      if (!response.ok) {
        throw new Error('Network response was not ok: ' + response.statusText);
      }
      return response.json();
    })
    .then(hikingTrails => {
      hikingTrails.forEach(trail => {
        const path = trail.path.map(point => L.latLng(point[0], point[1]));
        L.polyline(path, {
          color: trailColor,
          weight: 2,
          opacity: 0.35,
          interactive: false
        }).addTo(map);
      });
    })
    .catch(error => {
      console.error('Error loading reference hikes:', error);
    });

  // ---------------------------------------------------------------------
  // Pin state
  // ---------------------------------------------------------------------
  // Each pin: { lat, lng, marker }
  const pins = [];

  let routeLine = null;
  let previewLine = null; // dashed preview segment(s) for out-and-back / loop

  function round6(n) {
    return Math.round(n * 1e6) / 1e6;
  }

  function pinDivIcon() {
    return L.divIcon({
      className: '',
      html: '<div class="trail-pin-icon"></div>',
      iconSize: [14, 14],
      iconAnchor: [7, 7]
    });
  }

  function makeMarker(lat, lng) {
    const marker = L.marker([lat, lng], {
      icon: pinDivIcon(),
      draggable: true
    }).addTo(map);

    // Live-update the stored position while dragging (full precision, for a
    // smooth line preview), then snap to 6 decimal places (~0.1m) on
    // dragend to match the precision used throughout data/hikes.json.
    marker.on('drag', function() {
      const pin = pins.find(p => p.marker === marker);
      if (!pin) return;
      const pos = marker.getLatLng();
      pin.lat = pos.lat;
      pin.lng = pos.lng;
      onPinsChanged();
    });

    marker.on('dragend', function() {
      const pin = pins.find(p => p.marker === marker);
      if (!pin) return;
      const rounded = L.latLng(round6(pin.lat), round6(pin.lng));
      pin.lat = rounded.lat;
      pin.lng = rounded.lng;
      marker.setLatLng(rounded);
      onPinsChanged();
    });

    marker.on('contextmenu', function(e) {
      if (e.originalEvent) {
        e.originalEvent.preventDefault();
      }
      L.DomEvent.stopPropagation(e);
      const idx = pins.findIndex(p => p.marker === marker);
      if (idx !== -1) {
        map.removeLayer(pins[idx].marker);
        pins.splice(idx, 1);
        onPinsChanged();
      }
    });

    return marker;
  }

  map.on('contextmenu', function(e) {
    if (e.originalEvent) {
      e.originalEvent.preventDefault();
    }
    const lat = round6(e.latlng.lat);
    const lng = round6(e.latlng.lng);
    const marker = makeMarker(lat, lng);
    pins.push({ lat: lat, lng: lng, marker: marker });
    onPinsChanged();
  });

  // ---------------------------------------------------------------------
  // Derived export path (depends on pins + path type)
  // ---------------------------------------------------------------------
  function getPathType() {
    const checked = document.querySelector('input[name="path-type"]:checked');
    return checked ? checked.value : 'loop';
  }

  function computeExportPath() {
    const base = pins.map(p => [p.lat, p.lng]);
    const type = getPathType();

    if (base.length === 0) return [];

    if (type === 'linear') {
      return base.slice();
    }

    if (type === 'out-and-back') {
      if (base.length < 2) return base.slice();
      const reversed = base.slice(0, base.length - 1).reverse();
      return base.concat(reversed);
    }

    // loop
    if (base.length < 2) return base.slice();
    const first = base[0];
    const last = base[base.length - 1];
    if (first[0] === last[0] && first[1] === last[1]) {
      return base.slice();
    }
    return base.concat([first]);
  }

  // ---------------------------------------------------------------------
  // Haversine distance
  // ---------------------------------------------------------------------
  function haversineMiles(lat1, lon1, lat2, lon2) {
    const R = 3958.8; // earth radius in miles
    const toRad = deg => deg * Math.PI / 180;
    const dLat = toRad(lat2 - lat1);
    const dLon = toRad(lon2 - lon1);
    const a = Math.sin(dLat / 2) * Math.sin(dLat / 2) +
      Math.cos(toRad(lat1)) * Math.cos(toRad(lat2)) *
      Math.sin(dLon / 2) * Math.sin(dLon / 2);
    const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    return R * c;
  }

  function computeDistanceMiles(path) {
    let total = 0;
    for (let i = 1; i < path.length; i++) {
      total += haversineMiles(path[i - 1][0], path[i - 1][1], path[i][0], path[i][1]);
    }
    return total;
  }

  function formatDistance(miles) {
    return miles.toFixed(1) + ' miles';
  }

  function formatElevation(feet) {
    return Math.round(feet).toLocaleString('en-US') + ' ft';
  }

  // ---------------------------------------------------------------------
  // Auto-fill tracking for Distance / Elevation inputs
  // ---------------------------------------------------------------------
  const distanceInput = document.getElementById('distance-input');
  const elevationInput = document.getElementById('elevation-input');
  const distanceComputedLabel = document.getElementById('distance-computed-label');
  const elevationComputedLabel = document.getElementById('elevation-computed-label');

  let lastAutoFilledDistance = null;
  let lastAutoFilledElevation = null;
  let lastComputedElevationFeet = null; // most recent fetched elevation gain, in feet

  function maybeAutoFill(input, lastAutoFilledValue, newValue) {
    if (input.value === '' || input.value === lastAutoFilledValue) {
      input.value = newValue;
      return newValue;
    }
    return lastAutoFilledValue;
  }

  function updateDistanceStat() {
    const exportPath = computeExportPath();
    const miles = computeDistanceMiles(exportPath);
    const formatted = exportPath.length >= 2 ? formatDistance(miles) : '';
    distanceComputedLabel.textContent = 'computed: ' + (formatted || '–');
    if (formatted) {
      lastAutoFilledDistance = maybeAutoFill(distanceInput, lastAutoFilledDistance, formatted);
    }
  }

  const distanceErrorEl = document.getElementById('distance-error');

  document.getElementById('distance-reset-btn').addEventListener('click', function() {
    distanceErrorEl.style.display = 'none';
    distanceErrorEl.textContent = '';

    const exportPath = computeExportPath();
    if (exportPath.length < 2) {
      distanceErrorEl.textContent = 'Add at least 2 pins before resetting distance.';
      distanceErrorEl.style.display = 'block';
      return;
    }

    const miles = computeDistanceMiles(exportPath);
    const formatted = formatDistance(miles);
    distanceInput.value = formatted;
    lastAutoFilledDistance = formatted;
    updatePreview();
  });

  document.getElementById('elevation-reset-btn').addEventListener('click', function() {
    if (lastComputedElevationFeet !== null) {
      const formatted = formatElevation(lastComputedElevationFeet);
      elevationInput.value = formatted;
      lastAutoFilledElevation = formatted;
      updatePreview();
    }
  });

  const elevationErrorEl = document.getElementById('elevation-error');

  document.getElementById('fetch-elevation-btn').addEventListener('click', function() {
    elevationErrorEl.style.display = 'none';
    elevationErrorEl.textContent = '';

    const exportPath = computeExportPath();
    if (exportPath.length < 2) {
      elevationErrorEl.textContent = 'Add at least 2 pins before fetching elevation.';
      elevationErrorEl.style.display = 'block';
      return;
    }

    const lats = exportPath.map(p => p[0]).join(',');
    const lons = exportPath.map(p => p[1]).join(',');
    const url = 'https://api.open-meteo.com/v1/elevation?latitude=' + encodeURIComponent(lats) +
      '&longitude=' + encodeURIComponent(lons);

    fetch(url)
      .then(response => {
        if (!response.ok) {
          throw new Error('Network response was not ok: ' + response.statusText);
        }
        return response.json();
      })
      .then(data => {
        if (!data || !Array.isArray(data.elevation)) {
          throw new Error('Unexpected response shape');
        }
        const elevations = data.elevation;
        let gainMeters = 0;
        for (let i = 1; i < elevations.length; i++) {
          const diff = elevations[i] - elevations[i - 1];
          if (diff > 0) gainMeters += diff;
        }
        const gainFeet = gainMeters * 3.28084;
        lastComputedElevationFeet = gainFeet;
        const formatted = formatElevation(gainFeet);
        elevationComputedLabel.textContent = 'computed: ' + formatted;
        lastAutoFilledElevation = maybeAutoFill(elevationInput, lastAutoFilledElevation, formatted);
        updatePreview();
      })
      .catch(error => {
        console.error('Error fetching elevation:', error);
        elevationErrorEl.textContent = 'Could not fetch elevation data. Try again later or enter manually.';
        elevationErrorEl.style.display = 'block';
      });
  });

  // ---------------------------------------------------------------------
  // Drawing route + preview lines
  // ---------------------------------------------------------------------
  function redrawLines() {
    if (routeLine) {
      map.removeLayer(routeLine);
      routeLine = null;
    }
    if (previewLine) {
      map.removeLayer(previewLine);
      previewLine = null;
    }

    if (pins.length < 2) return;

    const solidPoints = pins.map(p => L.latLng(p.lat, p.lng));
    routeLine = L.polyline(solidPoints, {
      color: trailColor,
      weight: 4,
      opacity: 1.0
    }).addTo(map);

    const type = getPathType();
    if (type === 'out-and-back') {
      // Dashed return leg, overlaying the forward path (expected).
      const returnPoints = pins.slice().reverse().map(p => L.latLng(p.lat, p.lng));
      previewLine = L.polyline(returnPoints, {
        color: trailColor,
        weight: 3,
        opacity: 0.7,
        dashArray: '6, 8'
      }).addTo(map);
    } else if (type === 'loop') {
      const first = pins[0];
      const last = pins[pins.length - 1];
      if (first.lat !== last.lat || first.lng !== last.lng) {
        previewLine = L.polyline([L.latLng(last.lat, last.lng), L.latLng(first.lat, first.lng)], {
          color: trailColor,
          weight: 3,
          opacity: 0.7,
          dashArray: '6, 8'
        }).addTo(map);
      }
    }
  }

  // ---------------------------------------------------------------------
  // JSON preview (hand-formatted to match data/hikes.json style)
  // ---------------------------------------------------------------------
  function slugify(name) {
    return name
      .toLowerCase()
      .trim()
      .replace(/[^a-z0-9]+/g, '-')
      .replace(/^-+|-+$/g, '') || 'hike';
  }

  function formatPathBlock(path) {
    if (path.length === 0) return '[]';
    // Match data/hikes.json's convention of 6 decimal places per coordinate,
    // with path items at 6-space indent and the closing bracket at 4-space
    // indent (aligned with the "path" key), matching a hike object nested
    // inside the top-level array.
    const lines = path.map(point => '      [' + point[0].toFixed(6) + ', ' + point[1].toFixed(6) + ']');
    return '[\n' + lines.join(',\n') + '\n    ]';
  }

  function buildHikeObject() {
    return {
      name: document.getElementById('name-input').value,
      path: computeExportPath(),
      distance: distanceInput.value,
      elevation: elevationInput.value,
      difficulty: document.getElementById('difficulty-select').value,
      notes: document.getElementById('notes-input').value
    };
  }

  function buildFormattedJson(hike) {
    // Indented as a sibling entry inside the top-level array in
    // data/hikes.json (object at 2 spaces, keys at 4 spaces), so the
    // copied block can be pasted directly into that array.
    return '  {\n' +
      '    "name": ' + JSON.stringify(hike.name) + ',\n' +
      '    "path": ' + formatPathBlock(hike.path) + ',\n' +
      '    "distance": ' + JSON.stringify(hike.distance) + ',\n' +
      '    "elevation": ' + JSON.stringify(hike.elevation) + ',\n' +
      '    "difficulty": ' + JSON.stringify(hike.difficulty) + ',\n' +
      '    "notes": ' + JSON.stringify(hike.notes) + '\n' +
      '  }';
  }

  const jsonPreviewEl = document.getElementById('json-preview');
  const copyBtn = document.getElementById('copy-json-btn');
  const downloadBtn = document.getElementById('download-json-btn');
  const exportHint = document.getElementById('export-hint');
  const pinCountEl = document.getElementById('pin-count');

  function updatePreview() {
    const hike = buildHikeObject();
    jsonPreviewEl.value = buildFormattedJson(hike);

    pinCountEl.textContent = pins.length + (pins.length === 1 ? ' pin placed' : ' pins placed');

    const enabled = pins.length >= 2;
    copyBtn.disabled = !enabled;
    downloadBtn.disabled = !enabled;
    exportHint.style.display = enabled ? 'none' : 'block';

    const hasPins = pins.length > 0;
    document.getElementById('undo-pin-btn').disabled = !hasPins;
    document.getElementById('clear-pins-btn').disabled = !hasPins;
  }

  function onPinsChanged() {
    // The route changed, so any previously fetched elevation gain no longer
    // corresponds to the current path; invalidate it rather than letting it
    // be silently reused (see distance, which is recomputed live above).
    lastComputedElevationFeet = null;
    elevationComputedLabel.textContent = 'computed: –';
    redrawLines();
    updateDistanceStat();
    updatePreview();
  }

  // Re-derive everything when path type changes.
  document.querySelectorAll('input[name="path-type"]').forEach(radio => {
    radio.addEventListener('change', onPinsChanged);
  });

  // Metadata field changes update the JSON preview live.
  ['name-input', 'difficulty-select', 'notes-input'].forEach(id => {
    document.getElementById(id).addEventListener('input', updatePreview);
  });
  distanceInput.addEventListener('input', updatePreview);
  elevationInput.addEventListener('input', updatePreview);

  // ---------------------------------------------------------------------
  // Difficulty options (hardcoded list; keep in sync with the values
  // actually used in data/hikes.json)
  // ---------------------------------------------------------------------
  const difficultySelect = document.getElementById('difficulty-select');
  ['Easy', 'Moderate', 'Hard'].forEach(value => {
    const option = document.createElement('option');
    option.value = value;
    option.textContent = value;
    difficultySelect.appendChild(option);
  });

  // ---------------------------------------------------------------------
  // Undo / Clear
  // ---------------------------------------------------------------------
  document.getElementById('undo-pin-btn').addEventListener('click', function() {
    if (pins.length === 0) return;
    const last = pins.pop();
    map.removeLayer(last.marker);
    onPinsChanged();
  });

  document.getElementById('clear-pins-btn').addEventListener('click', function() {
    if (pins.length === 0) return;
    if (!confirm('Clear all pins? This cannot be undone.')) return;
    pins.forEach(p => map.removeLayer(p.marker));
    pins.length = 0;
    onPinsChanged();
  });

  // ---------------------------------------------------------------------
  // Copy / Download
  // ---------------------------------------------------------------------
  copyBtn.addEventListener('click', function() {
    if (copyBtn.disabled) return;
    navigator.clipboard.writeText(jsonPreviewEl.value).then(function() {
      const original = copyBtn.textContent;
      copyBtn.textContent = 'Copied!';
      copyBtn.classList.add('copied-flash');
      setTimeout(function() {
        copyBtn.textContent = original;
        copyBtn.classList.remove('copied-flash');
      }, 1500);
    }).catch(function(error) {
      console.error('Could not copy to clipboard:', error);
    });
  });

  downloadBtn.addEventListener('click', function() {
    if (downloadBtn.disabled) return;
    const hike = buildHikeObject();
    const filename = slugify(hike.name) + '.json';
    const blob = new Blob([jsonPreviewEl.value], { type: 'application/json' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = filename;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(url);
  });

  // Initial render.
  updatePreview();
});
</script>
