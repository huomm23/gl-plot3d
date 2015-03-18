gl-plot3d
=========
This is the core module for 3D plotting in gl-vis. It is compatible with the following modules:

* [gl-scatter](https://github.com/gl-vis/gl-scatter-plot): 3D scatter plots
* [gl-line-plot](https://github.com/gl-vis/gl-line-plot): 3D line plots
* [gl-surface-plot](https://github.com/gl-vis/gl-surface-plot): 3D surface plots
* [gl-simplicial-complex](https://github.com/gl-vis/gl-simplicial-complex): General mesh drawing
* [gl-error-bars](https://github.com/gl-vis/gl-error-bars): Error bars

This module (and this whole subecosystem) skew more towards the easy-side of the simple vs. easy tradeoff spectrum.  It has lots of options, but has opinionated and reasonable defaults which should make it suitable for small projects like mesh viewers or knocking out one-off data visualizations.

# Examples

### Scatter plot

```javascript
var createScene   = require('gl-plot3d')
var createScatter = require('gl-scatter-plot')
var bunny         = require('bunny')

var scene = createScene()

var scatter = createScatter({
  gl:           scene.gl,
  position:     bunny.positions,
  size:         10,
  glyph:        '★',
  orthographic: true,
  lineColor:    [0,0,0],
  color:        [1,0,0],
  lineWidth:    1
})

scene.add(scatter)
```

### Line plot

```javascript
var createScene = require('gl-plot3d')
var createLine  = require('gl-line-plot')

var scene = createScene()

var points = []
for(var t = 0; t< 1000; ++t) {
  var theta = Math.PI * t / 200.0
  points.push([Math.cos(theta), 0.002 * t, Math.sin(theta)])
}

var linePlot = createLine(scene.gl, {
  gl:        scene.gl,
  position:  points,
  lineWidth: 5,
  color:     [1,0,0],
  opacity:   0.5
})

scene.add(linePlot)
```

### Surface plot

```javascript
var createScene       = require('gl-plot3d')
var createSurfacePlot = require('gl-surface-plot')
var ndarray           = require('ndarray')
var fill              = require('ndarray-fill')
var diric             = require('dirichlet')

var scene = createScene()

//Create field
var field = ndarray(new Float32Array(512*512), [512,512])
fill(field, function(x,y) {
  return 128 * diric(10, 10.0*(x-256)/512) * diric(10, 10.0*(y-256)/512)
})

//Create surface plot
var surface = createSurfacePlot({
  gl:    scene.gl,
  field: field
})

scene.add(surface)
```

### Drawing a mesh

```javascript
var createScene = require('gl-plot3d')
var createMesh  = require('gl-simplicial-complex')
var bunny       = require('bunny')

//Create the scene
var scene = createScene()

//Create a mesh object for drawing a bunny
var mesh = createMesh({
  gl:         scene.gl,
  cells:      bunny.cells,
  positions:  bunny.positions,
  colormap:   'jet'
})

//Add the mesh to the scene
scene.add(mesh)
```

# Install

```
npm i gl-plot3d
```

# API

## Constructor

#### `var scene = require('gl-plot3d')(canvas[, options])`

Creates a new scene object.

* `canvas` is an HTML canvas element into which the scene is inserted. (If not specified, a new fullscreen canvas is created and appended to the document)
* `gl` is a WebGL context (If not specified, a new context is created)
* `glOptions` is a set of options passed to the new WebGL context, `gl` is not specified
* `camera` an object storing camera options.  See [orbiter](https://github.com/mikolalysenko/orbiter) for more details
* `axes` options passed to the axes object.  See [gl-axes](https://github.com/mikolalysenko/gl-axes) for more details
* `spikes` options passed to the axes spikes.  See [gl-spikes](https://github.com/mikolalysenko/gl-spikes) for more details
* `clearColor` a length 4 array of color values for the clear
* `fovy` the vertical field of view
* `zNear` near clip plane distance
* `zFar` far clip plane distance
* `pickRadius` the distance for mouse picking
* `autoBounds` a flag, if set automatically recalculates object bounds (default `true`)
* `autoScale` a flag, if set automatically scales the data set to unit length, preserving aspect ratio (default `true`)
* `autoCenter` a flag, if set translates data to the center of the coordinate system (default `true`)
* `clipToBounds` clip data points to remain within the axes bounds
* `snapToData` snap selections to data points
* `onselect` called whenever the currently highlighted data point changes
* `onrender` called whenever the scene is drawn

## Methods

#### `scene.addObject(obj)`
Adds a new object to the scene

#### `scene.removeObject(obj)`
Removes an object from the scene

#### `scene.dispose()`
Destroys the scene and releases all associated resources.  Also destroys all attached objects.

## Properties

#### `scene.selection`
Information about the currently selected object in the scene.

#### `scene.objects`
A list of all objects in the scene.

#### `scene.canvas`
The canvas element associated with the scene

#### `scene.gl`
The WebGL context associated with the scene.

#### `scene.axes`
A reference to the axes object for the scene

#### `scene.camera`
A reference to the camera object for the scene

#### `scene.bounds`
Bounds for the scene

# License
(c) 2015 Mikola Lysenko. MIT License

Development support by [plot.ly](http://plot.ly)