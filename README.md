# V-LARA Airspace Data

This repository contains airspace data for the V-LARA system.

- [Pilot Manual](https://wiki.vatsim-scandinavia.org/books/finnish-airports-charts/page/v-lara-airspace-reservation) 
- [ATC Manual](https://wiki.vatsim-scandinavia.org/books/special-procedures/page/v-lara-atc-guide) 

If you'd like to add your country's FIR or a new airspace dataset, follow the steps below.

---

## 📁 Create the two files for your FIR

You will need to create **two files** named after your FIR (replace `xxxx` with the FIR name in **lowercase**):

1. `xxxx.geojson` – for airspace geometry
2. `xxxx.json` – for FIR configuration

---

## 🌍 Add `.geojson` File (`xxxx.geojson`)

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
        "lowerFL": 95,
        "mustBeBookedWith": ["TSA1"],
        "activationLimits": [
          {"time": ["0900", "2000"], "month": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12], "weekday": [1, 2, 3, 4, 5, 6, 7]},
          {"time": ["2000", "2200"], "month": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12], "weekday": [1, 2, 3, 4, 5]}
        ]
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

| Field                 | Type   | Description                                  |
|-----------------------|--------|----------------------------------------------|
| `name`                | string | airspace name (e.g., `"TSA1"`). The airspace must match with the name in TopSky areas.txt!  |
| `type`                | string | Type of airspace (e.g., `"TSA"`, `"Local TRA"`). Keep the type as short as possible.        |
| `lowerFL`             | number | Lower flight level (e.g., SFC → `0`. 500FT → `5`. 9500FT → `95`)                         |
| `upperFL`             | number | Upper flight level (e.g., 10700 FT → `107`. UNL → `999`)                                  |
| (`mustBeBookedWith`)  | array  | Optional list of airspace names that must be booked together or prior (e.g., `["TSA1", "TSA2"]`) |
| (`activationLimits`)  | array  | Optional list of time + month + weekday limitations (see example json above) |


Make sure that:
- Each airspace has a **distinct `name`** and it shall **match with TopSky areas.txt**.
- Note! If you leave the name empty `"name": ""`, the shape will be visible on the map but not as a reservable airspace. This might be useful in order to display country borders etc.
- Coordinates are listed in the `[longitude, latitude]` format.
- The polygon is **closed** — the first and last coordinate pairs should match.

---

## 📦 Add .json File (`xxxx.json`)

This file defines how the FIR is displayed in the app and how airspaces are grouped.

### ✅ Example Structure

```json
{
  "mapCenter": [24.3, 58.5510],
  "mapZoom": 5.4,
  "pilotDocs": "https://wiki.vatsim-scandinavia.org/books/finnish-airports-charts/page/v-lara-airspace-reservation",
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
| (`pilotDocs`)  | `string`         | Optional URL to local procedures for Area Reservations                 |
| `groups`     | `object`           | A collection of airspace groups for organizing layers and filtering    |

Each entry in the `groups` object defines a preset selection of many airspace blocks. This makes it faster for the user to select commonly grouped airspace selections.

### 🧱 Group Object Structure

Each group must include:

| Field        | Type       | Description                                                  |
|--------------|------------|--------------------------------------------------------------|
| `airspaces`  | `string[]` | A list of airspace `name`s that belong to this group         |
| `lowerFL`    | `number`   | Preset Lower FL (e.g., `30`)         |
| `upperFL`    | `number`   | Preset Upper FL (e.g., `999`)        |

Make sure that the `airspaces` name's matches with the names defined in the .geojson file.

**Example with multiple groups:**

```json
{
  "mapCenter": [24.3, 58.5510],
  "mapZoom": 6,
  "groups": {
    "TRIDENT": {
      "airspaces": ["TSA1", "TSA2"],
      "lowerFL": 0,
      "upperFL": 245
    },
    "POKKA": {
      "airspaces": ["TSA3", "TSA4", "TRA87W"],
      "lowerFL": 95,
      "upperFL": 999
    }
  }
}
```

If your FIR does not have grouped airspace reservations:

```json
{
  "mapCenter": [24.3, 58.5510],
  "mapZoom": 6,
  "groups": {
    "No Presets": {
      "airspaces": [""],
      "lowerFL": 0,
      "upperFL": 999
    }
  }
}
```

### ✅ Validation Tips

Before submitting a Pull Request, ensure:

- [ ] Each airspace `Feature` in your `.geojson` has a unique and descriptive `name` and the name matches with an airspace defined in TopSky areas.txt.
- [ ] All `name`s in your `.json` file’s `groups.airspaces` array **match exactly** with the `name` values in your `.geojson`
- [ ] Your `.geojson` file is valid [GeoJSON](https://geojson.org/) (you can use [geojson.io](https://geojson.io) or [geojsonlint.com](https://geojsonlint.com) to validate)
- [ ] Polygons in the `.geojson` are properly **closed** (i.e. the first and last coordinate pair are the same)
- [ ] Your `.json` file is valid JSON (no trailing commas, properly quoted strings, etc.)
- [ ] Flight levels (`lowerFL` and `upperFL`) are correct
- [ ] `mapCenter` and `mapZoom` are appropriate to display the full FIR area. You may login to V-LARA profile `EACCZAMC (test environment)` to view boundaries of different FIR's. The coordinates are shown in the top-right corner of the Map Display.

---

## 🔗 Connect with EuroScope TopSky plugin

After submitting and Approved Pull Request:
- Navigate to [lara-backend.lusep.fi/topsky/xxxx.txt](https://lara-backend.lusep.fi/topsky/lara.txt) (replace xxxx with your FIR code)
- The file shows all **currently ongoing** reservations. By default it includes a dummy reservation `VLARA:350101:350101:0:1000:1001:0:100:VLARA:`. This makes sure that TopSky is able to read the file at all times even when no reservations are present.
- Navigate to plugins/ `TopSkySettings.txt` and add the following details:
  - `HTTP_Areas_Remote_URL=https://lara-backend.lusep.fi/topsky/xxxx.txt`
  - `Areas_PreActiveTime=900` use at least 900s (15min)
- For automatic area loading, the `Airspace Management Window` shall be set to open automatically on startup
    
---

## ❓ Questions and manuals

You may contact me on Discord for any questions.

- [Pilot Manual](https://wiki.vatsim-scandinavia.org/books/finnish-airports-charts/page/v-lara-airspace-reservation) 
- [ATC Manual](https://wiki.vatsim-scandinavia.org/books/special-procedures/page/v-lara-atc-guide) 



