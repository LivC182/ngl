
# Interaction controls

## Camera

### Viewer

The translation, zoom and rotation of the scene can be set via the [ViewerControls](../class/src/controls/viewer-controls.js~ViewerControls.html) class which is available as a property of the stage: [Stage.viewerControls](../class/src/stage/stage.js~Stage.html#instance-member-viewerControls).

Getting and setting the orientation for the whole scene:
```
var orientationMatrix = stage.viewerControls.getOrientation();
stage.viewerControls.orient( orientationMatrix );
```


### Animation

The scene can be smoothly rotated, moved and zoomed via the [AnimationControls](../class/src/controls/animation-controls.js~AnimationControls.html) class which is available as a property of the stage: [Stage.animationControls](../class/src/stage/stage.js~Stage.html#instance-member-animationControls).


### Automatic view

For the whole stage (see [Stage.autoView](../class/src/stage/stage.js~Stage.html#instance-method-autoView)):
```
stage.loadFile( "rcsb://3pqr" ).then( function( o ){
    o.addRepresentation( "cartoon" );
    stage.autoView();  // focus on all representations in all components
} );
```

For individual components (see [Component.autoView](../class/src/component/component.js~Component.html#instance-method-autoView)):
```
stage.loadFile( "rcsb://3pqr" ).then( function( o ){
    o.addRepresentation( "cartoon" );
    var duration = 1000; // optional duration for animation, defaults to zero
    o.autoView( duration );  // focus on the whole structure
} );
```

For structure components using a [selection string](./usage/selection-language.html) (see [StructureComponent.autoView](../class/src/component/structure-component.js~StructureComponent.html#instance-method-autoView)):
```
stage.loadFile( "rcsb://3pqr" ).then( function( o ){
    o.addRepresentation( "cartoon" );
    o.autoView( "RET" );  // focus on retinal
} );
```


### Principal axes

Animate structure to align with principal axes:
```
stage.loadFile( "rcsb://3pqr" ).then( function( o ){
    o.addRepresentation( "cartoon" );
    var pa = o.structure.getPrincipalAxes();
    stage.animationControls.rotate( pa.getRotationQuaternion(), 1500 );
} );
```


### Spin

Spin the whole scene around the y-axis (see [Stage.setSpin](../class/src/stage/stage.js~Stage.html#instance-method-setSpin)):

```
stage.setSpin( [ 0, 1, 0 ], 0.01 );
```


## Picking

Whenever the user clicks or hovers over the viewer canvas the appropriate [StageSignal](../typedef/index.html#static-typedef-StageSignals) is dispatched from [Stage.signals](../class/src/stage/stage.js~Stage.html#instance-member-signals). Any function added to those those signals is then called with a [PickingProxy](../class/src/controls/picking-proxy.js~PickingProxy.html) instance that provides access to what was picked.


### Clicked

```
stage.signals.clicked.add( function( pickingProxy ){ ... } );
```


### Hovered

Basis usage:

```
stage.signals.hovered.add( function( pickingProxy ){ ... } );
```

Example for showing a tooltip when hovering over atoms or bonds:

```
// create tooltip element and add to the viewer canvas
var tooltip = document.createElement( "div" );
Object.assign( tooltip.style, {
    display: "none",
    position: "absolute",
    zIndex: 10,
    pointerEvents: "none",
    backgroundColor: "rgba( 0, 0, 0, 0.6 )",
    color: "lightgrey",
    padding: "0.5em",
    fontFamily: "sans-serif"
} );
stage.viewer.container.appendChild( tooltip );

// load a structure file
stage.loadFile( "data://1blu.mmtf", { defaultRepresentation: true } );

// listen to `hovered` signal to move tooltip around and change its text
stage.signals.hovered.add( function( pickingProxy ){
    if( pickingProxy && ( pickingProxy.atom || pickingProxy.bond ) ){
        var atom = pickingProxy.atom || pickingProxy.closestBondAtom;
        var cp = pickingProxy.canvasPosition;
        tooltip.innerText = "ATOM: " + atom.qualifiedName();
        tooltip.style.bottom = cp.y + 3 + "px";
        tooltip.style.left = cp.x + 3 + "px";
        tooltip.style.display = "block";
    }else{
        tooltip.style.display = "none";
    }
} );
```


## Mouse

For convenience, there is a [MouseObserver](../class/src/stage/mouse-observer.js~MouseObserver.html) class which is available as a property of the stage: [Stage.mouseObserver](../class/src/stage/stage.js~Stage.html#instance-member-mouseObserver) and dispatches [MouseSignals](../typedef/index.html#static-typedef-MouseSignals) originating from the viewer canvas.

```
stage.mouseObserver.signals.scroll.add( function( delta ){ ... } );
```