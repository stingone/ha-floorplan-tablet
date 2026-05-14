# NDR Floorplan — Home Assistant Tablet Dashboard

A full-screen tablet dashboard for Home Assistant built on `picture-elements` with absolute positioning. Features a persistent sidebar with clock, navigation, people presence, and weather, combined with swipeable content areas per view.

> **Optimised for tablet use at 1280×800 (16:10 ratio).** The `blurredFloorplan.png` base image defines the aspect ratio of the entire dashboard — all absolute positioning in percentages is calibrated against it. If you use a different screen resolution, replace the base image with one matching your own ratio.

---

## Preview

<!-- Add GIF or video here -->
> 📹 *Screenshot / GIF coming soon*

---

## Features

- Animated floorplan with day/night cycle and dynamic sunlight opacity
- Weather overlay (rain/storm GIF effects on the floorplan)
- Custom weather icons in the sidebar
- Swipeable device tiles (HomeKit-style)
- Spotify + Sonos media control
- Climate control with mini-graph temperature history
- Energy dashboard (usage, solar, distribution)
- Live camera feeds (WebRTC)
- Calendar view with weather integration
- Buienradar rain radar

---

## Views

| View | Path | Description |
|---|---|---|
| Home | `/home` | Animated floorplan with light controls |
| Apparaten | `/devices` | HomeKit-style device tiles (2 swipe pages) |
| Media | `/media` | Spotify + Sonos control |
| Klimaat | `/climate` | Mini-climate cards + temperature graphs |
| Energie | `/energie` | Energy usage, solar, distribution |
| Buienradar | `/buienradar` | Weather forecast + rain radar |
| Camera | `/camera` | 4x WebRTC camera feeds |
| Agenda | `/agenda` | Calendar with weather |

---

## Dependencies

### HACS — Lovelace Cards

| Card | Repository |
|---|---|
| button-card | `custom-cards/button-card` |
| layout-card | `thomasloven/lovelace-layout-card` |
| card-mod | `thomasloven/lovelace-card-mod` |
| config-template-card | `iantrich/config-template-card` |
| mini-graph-card | `kalkih/mini-graph-card` |
| mini-media-player | `kalkih/mini-media-player` |
| mini-climate-card | `artem-sedykh/mini-climate-card` |
| simple-swipe-card | `benct/lovelace-simple-swipe-card` |
| spotifyplus-card | `thlucas1/spotifyplus_card` |
| custom-sonos-card | `punxaphil/custom-sonos-card` |
| calendar-card-pro | `FamousWolf/calendar-card-pro` |
| weather-forecast-card | `bramkragten/weather-forecast-card` |
| buien-rain-forecast | `kurvenmacher/lovelace-buien-rain-card` |
| webrtc-camera | `AlexxIT/WebRTC` |
| homekit-card | `DBuit/homekit-card` |
| text-element | `custom-cards/text-element` |

### HACS — Integrations

| Integration | Use |
|---|---|
| SpotifyPlus | Spotify media player |

---

## Required Entities / Sensors

### Built-in HA sensors (enable in settings)

| Entity | Description |
|---|---|
| `sensor.time` | Current time (HH:MM) |
| `sun.sun` | Sun position for day/night cycle |

### Custom template sensors

Defined in `packages/sensor_floorplan.yaml` (included in this repo). Enable packages in `configuration.yaml`:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

| Sensor | Description |
|---|---|
| `sensor.date_string` | Dutch formatted date for the sidebar (e.g. "Donderdag, 14 Mei 2026"), updated hourly |
| `sensor.sunlight_pct` | Sunlight percentage based on sun elevation and cloud coverage from PirateWeather |
| `sensor.sunlight_opacity` | Opacity value (0.0–1.0) for the day/night floorplan overlay, minimum 0.6 above horizon |

### Required integrations

| Integration | Entity | Description |
|---|---|---|
| Buienradar | `weather.buienradar` | Weather + temperature |
| PirateWeather | `weather.pirateweather` | Cloud coverage for sunlight sensor |
| Any thermostat integration | `climate.YOUR_THERMOSTAT` | Central heating thermostat (used in climate view) |

The indoor temperature sensor (`sensor.ebusd_ctlv2_z1roomtemp` in this config) can be any temperature sensor in your setup. Replace it with your own in the climate cards and the sidebar mini-graph.

---

## Configuration

### 1. Buienradar coordinates

In the `buienradar` view, replace the placeholder coordinates with your own location:

```yaml
- type: custom:buien-rain-forecast
  long: YOUR_LONGITUDE   # e.g. 4.8945
  lat: YOUR_LATITUDE     # e.g. 52.3667
```

### 2. Calendar entity

In the `agenda` view, replace with your own Google Calendar entity:

```yaml
- type: custom:calendar-card-pro
  entities:
    - YOUR_CALENDAR_ENTITY   # e.g. calendar.your_name_gmail_com
```

### 3. People

Replace the person entities and image paths with your own:

```yaml
entity: person.YOUR_NAME
state_image:
  home: /local/ndr_floorplan/people/YOUR_NAME.png
  not_home: /local/ndr_floorplan/people/YOUR_NAME_gone.png
```

### 4. SpotifyPlus

Replace the SpotifyPlus entity and card ID with your own from the SpotifyPlus integration:

```yaml
entity: media_player.spotifyplus_YOUR_SPOTIFY
cardUniqueId: YOUR_CARD_UNIQUE_ID
```

---

## File Structure

```
config/
├── ndr_floorplan_clean.yaml        # This dashboard
├── packages/
│   └── sensor_floorplan.yaml       # Template sensors (date_string, sunlight_pct, sunlight_opacity)
└── www/
    └── ndr_floorplan/
        ├── floorplan/
        │   ├── blurredFloorplan.png
        │   ├── house-day.png
        │   ├── house-night.png
        │   ├── interior-day.png
        │   ├── interior-night.png
        │   ├── transparent.png
        │   ├── kitchen_off.png
        │   ├── rain.gif
        │   ├── rain2.gif
        │   ├── rainstorm.gif
        │   └── weather/
        │       ├── sunny.png
        │       ├── clear_night.png
        │       ├── cloudy.png
        │       ├── mostly_cloudy.png
        │       ├── fog.png
        │       ├── lightning.png
        │       ├── storm.png
        │       ├── rainy.png
        │       ├── heavy_rain.png
        │       ├── snowy.png
        │       ├── heavy_snow.png
        │       ├── windy.svg
        │       └── mixed_rain.png
        └── people/
            ├── YOUR_NAME.png
            └── YOUR_NAME_gone.png
```

---

## Credits

Based on the excellent work of [NdR91](https://github.com/NdR91) and his [NdR-FloorplanUI](https://github.com/NdR91/NdR-FloorplanUI) whose floorplan dashboard was the original inspiration. This version is a significantly simplified rebuild with three key differences: reusable `button_card_templates` for sidebar elements instead of duplicating configuration across every view; all `card_mod` styling updated to current standards (using CSS variables like `--ha-card-background` instead of legacy overrides); and a cleaner overall structure that is easier to maintain and adapt.

---

## License

MIT — feel free to use, adapt, and build upon this. If you make something even better with it, I'd love to see it!
