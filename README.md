# Google Map Drawing plugins

Google Map script with drawing library and basic following functions:

- Custom UI and css
- No jQuery
- Support drawing shapes and exporting coordinates

Some sample pictures:

<img src="https://taitrd.github.io/assets/images/taitrdgmapsample1.jpeg" height="400px" >
<img src="https://taitrd.github.io/assets/images/taitrdgmapsample2.jpeg" height="700px" >

Requirements

- `Google Map API Javascript`: include drawing library.
- Font Awesome > 4 for icons

## **Definitions**

- **Options**: Gmap drawer options on html head with object variable `gmapDrawerOptions` includes following options.
  - `visible`: visible map at beginning.
    - type: boolean, default: true
  - `enableSelection`: enable selection when clicking on elements.
    - type: boolean, default value: true
  - `drawingButtons`: configure control buttons and visible drawing modes.
    - type: object array, default config
  - `drawingToolOptions`: override drawing tool options.

- **Events**
  - `feature_selected`
  - `shape_selected`
  - `polygon_selected`
  - `marker_selected`
  - `polygon_completed`
  - `arrow_completed`
  - `marker_completed`
  - `draw_items_change`
  - `on_delete_item`
  - `on_save_selection`

- **Methods**
  - `addControls(controls-element)`: add map controls from DOM element
  - `attachEvent(name, callback)`: listen events
  - `listenEvent(name, callback)`: listen events
  - `clearMap()`: clear selection, controls, geo layers and reset default configuration
  - `clearSelection()`: clear selections both google elements and geo layers
  - `deleteSelectedShape()`: clear selected shape/geo layer
  - `rotateSelectedMarker(degree)`: rotate marker/point (geo layer)
  - `loadGeoJson(geo-json-format, autoDraw)`: load geo layers
  - `initialize()`: creating drawer tool
  - `setMap(google-map-instance)`: set map instance
  - `setEditMode(boolean)`: set edit mode flag state
  - `setOption(string, boolean)`: set tool's single option (check option description above)
    - `visible` key:  set visible flag value for logics and show/hide tool and elements
    - `enableSelection` key: set selection flag for logics (prevent selection) and clear selection if false value
  - `setGeoOption(string, boolean)`: set geo layer option
    - `visibleArrows` key: set visible value (boolean) to show/hide point arrows
  
## Browser guide

Include this to link stylesheet and script

```html
<!-- head -->
<link rel="stylesheet" href="path/to/library/@taitrd/gmap/css/gmap.css" />
```

```html
<!-- body -->
<script src="https://maps.google.com/maps/api/js?key=<your-key>&libraries=places,drawing"></script>
<script src="path/to/library/@taitrd/gmap/js/gmap.js">
```

## Yii1 client script guide

Set path of alias `npm` in `main.php` to npm assets directory.

```php
Yii::setPathOfAlias('npm', dirname(__FILE__) . '/../../node_modules');
```

add this package to `CClientScript` component configuration.

```php
 /** @var CClientScript $cs */
$cs = Yii::app()->getClientScript();
$cs->addPackage('gmap', array(
    'basePath' => 'npm.@taitrd.gmap',
    'js'=>array('js/gmap.js'),
    'css'=>array('css/gmap.css'),
    'depends' => array(),
));

$drawingButtons = json_encode(array(
    array('name' => 'drawAShape', 'label' => Yii::t('main', 'Draw polygon')),
    array('name' => 'drawAnArrow', 'label' => Yii::t('SchlagModule.main', 'Draw an arrow')),
));
$cs->registerScript('gmapDrawerOptions', <<<JAVASCRIPT
 var gmapDrawerOptions = {
    drawingButtons: {$drawingButtons},
}
JAVASCRIPT, CClientScript::POS_HEAD);
// include package for usage
$cs->registerPackage('gmap');
```

## Usage

Notes: Include this script to your script and change it to fit your environment.

```javascript
var gmapDrawerOptions = {
    drawingButtons: [
      {name: 'drawAShap', label: 'Draw a polygon'},
    ],
}
```

```html
<div class="gmap-drawer show-drawing-control-label">
  <div id="map"></div>
</div>
```

Bind your events of buttons with jquery or something with javascript.

```javascript
this.btnDeletePolygon.on('click', function(e) {
    gmapDrawer.deleteSelectedShape();
})
this.btnCancelSelection.on('click', function(e) {
    gmapDrawer.clearSelection();
})
```

```html
<script>
  // Set existing map instance
  if (typeof gmapDrawer !== "undefined") {
    gmapDrawer.setMap(map);
  }

  // Initialize drawer
  gmapDrawer.initialize();
  // Bind event to your function
  gmapDrawer.listenEvent("shape_selected", (data) => {});

  const json = {
    type: "FeatureCollection",
    features: [
      {
        type: "Feature",
        properties: {
          id: 1,
        },
        geometry: {
          type: "Polygon",
          coordinates: [
            [
              {
                lat: 51.24713447076167,
                lng: 13.046661619128418,
              },
              {
                lat: 51.241277877758016,
                lng: 13.055158857287598,
              },
              {
                lat: 51.241116676579914,
                lng: 13.03636193651123,
              },
              {
                lat: 51.24536145250726,
                lng: 13.044515851916504,
              },
              {
                lat: 51.24713447076167,
                lng: 13.046661619128418,
              },
            ].map((i) => [i.lng, i.lat]),
          ],
        },
      },
      {
        type: "Feature",
        properties: {
          id: 2,
        },
        geometry: {
          type: "Polygon",
          coordinates: [
            [
              {
                lat: 51.25223823260805,
                lng: 13.054386381091309,
              },
              {
                lat: 51.25116380350319,
                lng: 13.056017164172363,
              },
              {
                lat: 51.2499281789958,
                lng: 13.055416349353028,
              },
              {
                lat: 51.250411688191974,
                lng: 13.053785566271973,
              },
              {
                lat: 51.25111008138892,
                lng: 13.053184751452637,
              },
              {
                lat: 51.25223823260805,
                lng: 13.054386381091309,
              },
            ].map((i) => [i.lng, i.lat]),
          ],
        },
      },
    ],
  };
  gmapDrawer.loadGeoJson(json, true);

  // Empty data and remove drawer from map

  gmapDrawer.clearMap();
</script>
```

## Change logs

- 2023-03-11 1.0.5
  - Updated visible and optional additions options for GEO features
  - Update styles: added `.gmap-hidden` class for hiding another elements on edit mode (`.editMode` class), addition `.gmap-controls` class for bootstrap button styles
  - Added `setGeoOption` function for updating feature styles
  - Updated google marker functions: `resetArrowIconMarker`
- 2022-10-10 1.0.4
  - Fix bugs and styles.
  - Add arrow color picker
  - Integrate create and remove events: arrow, polygon.
  - Handle map element's data.
  - Relocate structure, prototypes

- 2022-10-05 1.0.3
  - Handling options, drawing options, modes, open methods
  - Arrow marker & data layer feature: highlight arrow marker, draggable, rotation
  - Add bootstrap icons library: css, buttons, fontawesome
  - Update selection, controls element, confirmation to delete item
  - Relocate structure, prototypes

- 2022-09-12 1.0.2
  - Open/Close principle.
  - Describe functions.

- 2022-09-11 1.0.1
  - Create specific private events
  - Update button control styles

- 2022-09-10 1.0.0
  - Initialize package
