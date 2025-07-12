# V-LARA Airspace Data

This repository contains airspace data for the V-LARA system.

## ✈️ Contribute a New FIR (Flight Information Region)

If you'd like to add your country's FIR or a new airspace dataset, follow the steps below.

---

## 📁 Step 1: Create Two Files

You will need to create **two files** named after your FIR (replace `xxxx` with the FIR name in **lowercase**):

1. `xxxx.geojson` – for airspace geometry
2. `xxxx.json` – for FIR configuration

---

## 🌍 1. The `.geojson` File (Airspace Geometry)

This file should follow the GeoJSON format and define each airspace volume as a `Feature`.

**Example: `myfir.geojson`**

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {
        "name": "TSA1",
        "type": "TSA",
        "upperFL": 660,
        "lowerFL": 95
      },
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          [
            [27.1153, 57.7875],
            [26.5861, 57.6333],
            [26.1611, 57.9083],
            [25.2217, 58.2014],
            [25.2222, 58.2736],
            [25.7456, 58.3797],
            [26.75, 58.3667],
            [27.1675, 57.9175],
            [27.1153, 57.7875]
          ]
        ]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "name": "TSA2",
        "type": "TSA",
        "upperFL": 245,
        "lowerFL": 95
      },
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          [
            [23.2075, 59.1261],
            [24.2236, 59.3049],
            [24.2611, 59.2283],
            [23.4333, 58.9],
            [23.2075, 59.1261]
          ]
        ]
      }
    }
  ]
}
```

### ✅ Required GeoJSON Properties

Each `Feature` must contain the following fields inside its `properties` object:

| Field     | Type   | Description                                  |
|-----------|--------|----------------------------------------------|
| `name`    | string | airspace name (e.g., `"TSA1"`), **must be same as defined in TopSky areas.txt!**  |
| `type`    | string | Type of airspace (e.g., `"TSA"`, `"TRA"`, `"CTR"`) |
| `lowerFL` | number | Lower flight level (e.g., `95`)              |
| `upperFL` | number | Upper flight level (e.g., `660`)             |

Make sure that:
- Each airspace has a **distinct `name`**.
- Coordinates are listed in the `[longitude, latitude]` format.
- The polygon is **closed** — the first and last coordinate pairs should match.

---

## 📦 JSON Configuration File (`xxxx.json`)

This file defines how the FIR is displayed in the app and how airspaces are grouped.

### ✅ Example Structure

```json
{
  "mapCenter": [24.3, 58.5510],
  "mapZoom": 5.4,
  "groups": {
    "GROUP1": {
      "airspaces": ["TSA1", "TSA2"],
      "lowerFL": 30,
      "upperFL": 999
    }
  }
}
```

### 🔍 JSON Field Descriptions

| Field        | Type               | Description                                                            |
|--------------|--------------------|------------------------------------------------------------------------|
| `mapCenter`  | `[number, number]` | Map center `[longitude, latitude]` for the default view                |
| `mapZoom`    | `number`           | Initial zoom level (e.g., `5.4`)                                       |
| `groups`     | `object`           | A collection of airspace groups for organizing layers and filtering    |

Each entry in the `groups` object defines a display group on the map. You can create one or more groups depending on how you want the airspaces to be displayed or filtered.

### 🧱 Group Object Structure

Each group must include:

| Field        | Type       | Description                                                  |
|--------------|------------|--------------------------------------------------------------|
| `airspaces`  | `string[]` | A list of airspace `name`s that belong to this group         |
| `lowerFL`    | `number`   | The minimum flight level for this group (e.g., `30`)         |
| `upperFL`    | `number`   | The maximum flight level for this group (e.g., `999`)        |

These values define the vertical range in which the group is visible in the app.

**Example with multiple groups:**

```json
{
  "mapCenter": [24.3, 58.5510],
  "mapZoom": 6,
  "groups": {
    "LOW LEVEL": {
      "airspaces": ["TSA1", "TSA2"],
      "lowerFL": 0,
      "upperFL": 245
    },
    "HIGH LEVEL": {
      "airspaces": ["TSA3"],
      "lowerFL": 246,
      "upperFL": 660
    }
  }
}
```

### ✅ Validation Tips

Before submitting a Pull Request, ensure:

- [ ] Each airspace `Feature` in your `.geojson` has a unique and descriptive `name`
- [ ] All `name`s in your `.json` file’s `groups.airspaces` array **match exactly** with the `name` values in your `.geojson`
- [ ] Your `.geojson` file is valid [GeoJSON](https://geojson.org/) (you can use [geojson.io](https://geojson.io) or [geojsonlint.com](https://geojsonlint.com) to validate)
- [ ] Polygons in the `.geojson` are properly **closed** (i.e. the first and last coordinate pair are the same)
- [ ] Your `.json` file is valid JSON (no trailing commas, properly quoted strings, etc.)
- [ ] Flight levels (`lowerFL` and `upperFL`) are correct and consistent across files
- [ ] `mapCenter` and `mapZoom` are appropriate to display the full FIR area

---
