# Hiking Map Data Schema

This directory contains JSON files with hiking trail data displayed on the [Hiking page](../hiking.md).

## Schema Documentation

Each hiking trail in `hikes.json` follows this schema:

```json
{
  "name": "Trail Name",         // String: The name of the hiking trail
  "path": [                     // Array of [latitude, longitude] pairs marking the trail path
    [37.1234, -122.5678],       // First point [latitude, longitude]
    [37.1235, -122.5679],       // Additional points...
    ...
  ],
  "distance": "X.X miles",      // String: Trail distance with unit
  "elevation": "X,XXX ft",      // String: Elevation gain with unit
  "difficulty": "Easy|Moderate|Hard", // String: Difficulty rating
  "notes": "Description text"   // String: Personal notes about the trail
}
```

## Example Entry

```json
{
  "name": "Mission Peak",
  "path": [
    [37.5079, -121.9086],
    [37.5103, -121.9042],
    [37.5142, -121.8998],
    [37.5167, -121.8956],
    [37.5197, -121.8901],
    [37.5229, -121.8864]
  ],
  "distance": "6.2 miles",
  "elevation": "2,100 ft",
  "difficulty": "Hard",
  "notes": "Steep climb but worth it for the summit views. Bring plenty of water."
}
```

## Potential Future Extensions

The schema may be extended in the future to include:

- `date`: Date when the hike was completed
- `photos`: Array of photo URLs or paths to local images
- `season`: Best season to hike this trail (e.g., "Spring", "Year-round")
- `region`: Geographic region (e.g., "Peninsula", "East Bay", "Marin")
- `trailhead`: GPS coordinates of the starting point
- `url`: Link to official trail information
- `tags`: Array of keywords (e.g., ["waterfall", "redwoods", "dog-friendly"])

## Adding New Hikes

To add a new hike, simply append a new JSON object to the array in `hikes.json`. Make sure to:

1. Follow the schema format above
2. Add a comma after the previous entry
3. Maintain valid JSON syntax
4. Provide accurate GPS coordinates for the path

## Coordinates

Path coordinates are stored as [latitude, longitude] pairs. You can obtain these coordinates from:

- GPX files from GPS devices (converted to JSON)
- Tracing routes on mapping services like Google Maps or Caltopo
- Recording your route with hiking/tracking apps 