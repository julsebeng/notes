# Blender Notes
### [Quick Reference](#quick-reference)
### [Specifics](#details)
---
## Quick Reference
### (QR) Navigating in 3D Space 
| Mouse Controls ||
|-----|-----|
`middle mouse button (MMB)` | rotate camera
| `MMB + ctrl` | zoom |
| `MMB + shift` | pan |

| Numpad Controls ||
|-----|-----|
| `num 5` | orthogonal/isographic toggle |
| `num 1` | front view |
| `num 1 + ctrl` | back view |
| `num 3` | right view |
| `num 3 + ctrl` | left view |
| `num 7` | top view |
| `num 7 + ctrl` | bottom view |

| Additional View Controls ||
|-----|-----|
| `shift + c` | capture all objects in view |
| `ctrl + alt + q` | toggle quad view |


### (QR) Selecting Objects
| Selection Controls ||
|-----|-----|
| `RMB` | select object |
| `RMB + shift` | select multiple |
| `a` | select/deselect everything toggle |
| `b` | border select |
| `c` | circle select |

### (QR) Moving Objects
| Movement Controls ||
|-----|-----|
| `g` | translate selection |
| `x, y, z` | constrain change to axis |
Movement can be finely adjusted via the Transform panel on the right side of the viewport
(hidden by default).

### (QR) Rotating Objects
| Rotation Controls ||
|-----|-----|
| `r` | rotate selection |
| `rr` | alternate rotation mode |
| `x, y, z` | constrain change to axis |
Rotation can be finely adjusted via the Transform panel on the right side of the viewport
(hidden by default).

### (QR) Scaling Objects
| Scale Controls ||
|-----|-----|
| `s` | scale selection |
| `x, y, z` | constrain change to axis |
Scaling can be finely adjusted via the Transform panel on the right side of the viewport
(hidden by default).

### (QR) Transform Orientation
- By default, transformations are made based on global axes.
- This behavior can be changed via the Transform Orientation dropdown box, next to the transform 
  gizmos present at the bottom of the view pane.

| Scale Controls ||
|-----|-----|
| ***Global* | transformations based on global X, Y, Z axes |
| **Local** | transformations made relative to the orientation of the transformed object |
| **Gimbal** | used when working with bones |
| **Normal** | aligns transformations to the normals of the object |
| **View** | transformations always made relative to the camera |

### (QR) Changing an Object's Origin
- The Origin is the orange dot that each object has attached to it.
- Some transformations are based on this location so it's position is important.

| Changing the Origin ||
|-----|-----|
| `Object -> Transform -> Geometry to Origin` | moves object so origin is at its center |
| `Object -> Transform -> Origin to Geometry` | sets the origin to the center of the object |
| `Object -> Transform -> Origin to 3D Cursor` | sets the origin to the center of the object |
| `LMB` | set 3D cursor to mouse pointer |

### (QR) Selecting Pivot Points
- When manipulating multiple objects, how the transformation is applied to each object can be 
  adjusted.
- This behavior can be adjusted in the Pivot Point dropdown at the bottom of the view panel.

| Pivot Modes ||
|-----|-----|
| **Bounding Box Center** | draws a bounding box around all objects then transforms around that center point |
| **3D Cursor** | transforms around the 3D cursor |
| **Individual Origins** | transforms each object around its individual origin |
| **Median Point** | similar to Bounding Box, but takes into account the volume of each object |
| **Active Object** | transforms around the last object selected |

### (QR) Snapping Options
- Magnet icon at the bottom of the viewport toggles snap-to-grid.
    - Grid can be adjusted in the Properties panel hidden to the right of the viewport.
- snapping is based on the origin of the transformed object.

| Snap Elements ||
|-----|-----|
| **Face** | snaps to faces |
| **Edge** | snaps to edges |
| **Vertex** | snaps to vertices |
| **Increment** | default snap-to-grid behavior |
| `Object -> Snap -> Selection to 3D Cursor` | snap selection to the 3D cursor |

The *3D cursor* can also be snapped to various locations in the `Object -> Snap` menu.

## Specifics