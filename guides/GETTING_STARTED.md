# Getting Started

## Install

We start with the assumption that you have [Node.js](https://nodejs.org/en) installed and [npm](https://www.npmjs.com/) (node package manager).

You can install the Terra Draw into your project like so:

```shell
npm install terra-draw
```

Be aware Terra Draw is currently in alpha, the initial API is still being finalised. It is strongly advised to pin your installation to a specific version i.e. not using carat, asterix or tilde for versions but giving a version explicitly in your `package.json`

```
  "terra-draw": "0.0.1-alpha.22"
```

Once terra-draw is out of alpha this suggestion will be removed as we will aim to move to semantic versioning.

## API Docs

You can find the full autogenerated [API docs on the terra draw website](https://terradraw.io/#/api)

## Common Patterns

A selection of common things you may want to do with Terra Draw is available via the [common patterns guide in this folder](./COMMON_PATTERNS.md). If you think something is missing please raise an issue!

## Adapters

Terra Draw can be used with a set of different adapters to allow you to use it with your mapping library of choice. Each adapter has slightly different configuration but the API is the same for them all. For a deeper explanation about what adapters are and how to write you own see the [development guide](./DEVELOPMENT.md).

### Leaflet

Here is some simple starter code for using Terra Draw with the popular open source mapping library Leaflet. It assumes you are going instantiate your map in a div with and id of `map`, which is assumed to exist within your HTML.

```typescript
// Import the leaflet mapping library
import * as L from "leaflet";

// Import Terra Draw, we will import the leaflet adapter, select mode and polygon mode
import {
	TerraDraw,
	TerraDrawLeafletAdapter,
	TerraDrawSelectMode,
	TerraDrawPolygonMode,
} from "terra-draw";

// Initialize a new Leaflet map, providing the id of the div to display the map
// Set the initial center (latitude, longitude) and the zoom level
const leafletMap = L.map("map", {
	center: [lat, lng],
	zoom: 4, // starting zoom,
	minZoom: 3,
	maxZoom: 20,
	tapTolerance: 10,
});

// Add a basemap from OpenStreetMap to the Leaflet map
L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
	maxZoom: 19,
	attribution:
		'&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>',
}).addTo(leafletMap);

// Instantiate the TerraDraw API with a Leaflet adapter and custom modes
const draw = new TerraDraw({
	adapter: new TerraDrawLeafletAdapter({
		// The leaflet library object
		lib: leaflet,

		// The leaflet map object we created
		map: leafletMap,

		// The decimal precision of the coordinates created
		coordinatePrecision: 9,
	}),

	// Modes is an object containing all the modes we wish to
	// instantiate Terra Draw with
	modes: [
		new TerraDrawSelectMode({
			flags: {
				// Following flags determine what you can do in
				// select mode for features of a given mode - in this case polygon
				polygon: {
					feature: {
						scaleable: true,
						rotateable: true,
						draggable: true,
						coordinates: {
							midpoints: true,
							draggable: true,
							deletable: true,
						},
					},
				},
			},
		}),
		new TerraDrawPolygonMode({
			allowSelfIntersections: false,
			pointerDistance: 30,
		}),
	]
});

// Start drawing
draw.start();

// Set the mode to polygon
draw.setMode("polygon");
```

### Google Maps API

Here is some starter code for using Terra Draw with the Google Maps API. It assumes you are going instantiate your map in a div with and id of `map`, which is assumed to exist within your HTML.

```typescript
// Import the google maps api loader
import { Loader } from "@googlemaps/js-api-loader";

// Import Terra Draw, we will import the leaflet adapter, select mode and polygon mode
import {
	TerraDraw,
	TerraDrawLeafletAdapter,
	TerraDrawSelectMode,
	TerraDrawPolygonMode,
} from "terra-draw";

const loader = new Loader({
	apiKey: "INSERT_YOUR_API_KEY_HERE", // You need to provide your own API key here
	version: "weekly",
});

// We need to await the google maps api
loader.load().then((google) => {
	// Create the map
	const map = new google.maps.Map(document.getElementById("map"), {
		disableDefaultUI: true,
		center: { lat: this.lat, lng: this.lng },
		zoom: this.zoom,

		//Setting clickableIcons to false is used to disable the default behavior of the
		// Google Maps API, which is to allow users to interact with clickable icons on the
		// map, such as points of interest (POIs) or other interactive map elements.
		clickableIcons: false,
	});

	// When using Google Maps, it's important to wrap the creation of custom overlays or other // map-related initializations inside the 'projection_changed' event listener to ensure
	// that the map's projection is fully loaded before your code runs.
	map.addListener("projection_changed", () => {
		const draw = new TerraDraw({
			adapter: new TerraDrawGoogleMapsAdapter({
				lib: google.maps,
				map,
				coordinatePrecision: 9,
			}),
			modes: [
				new TerraDrawSelectMode({
					flags: {
						// Following flags determine what you can do in
						// select mode for features of a given mode - in this case polygon
						polygon: {
							feature: {
								scaleable: true,
								rotateable: true,
								draggable: true,
								coordinates: {
									midpoints: true,
									draggable: true,
									deletable: true,
								},
							},
						},
					},
				}),
				new TerraDrawPolygonMode({
					allowSelfIntersections: false,
					pointerDistance: 30,
				}),
			]
		});

		// Start drawing
		draw.start();

		// Set the mode to polygon
		draw.setMode("polygon");
	});
});
```

### OpenLayers

Coming soon - please see development folder for example for now.

### MapboxGL JS

Coming soon - please see development folder for example for now.

### MapLibreGL JS

Coming soon - please see development folder for example for now.

## Other Examples

There are a few other working examples that you can use as points of reference for creating a new app using Terra Draw.

### Development Example

The [development folder features an example](https://github.com/JamesLMilner/terra-draw/tree/main/development) of using Terra Draw with all the adapters. It is meant for local development but can also be used as a guide of how to use Terra Draw with each adapter without any frame work.

### Full Example

The [Terra Draw official website](https://github.com/JamesLMilner/terra-draw-website) is open source acts as a full working implementation of how to use Terra Draw. In this case it is used with popular framework Preact (has very similar API to React).

