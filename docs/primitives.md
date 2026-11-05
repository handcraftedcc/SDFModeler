# Primitives

Prims carry both their shape and the settings for how they combine with earlier objects. By default, separate prims enter the assembly one by one. Use a [group](workflow.md#groups-build-the-cutter-first) when several prims should combine with the rest of the model as one object.

## SDFM Prim

This is the main starting point. **Primitive Type** switches between seven built-in shapes:

| Shape | Useful for |
| --- | --- |
| Box | Bodies, panels, blocks, and rectangular cutters |
| Sphere | Rounded forms, dimples, and spherical cutouts |
| Tube | Cylindrical forms and holes |
| Capsule | Rounded bars and slots |
| Torus | Rings and circular grooves |
| Pyramid | Pointed forms and tapered cutters |
| Octahedron | Diamond-like forms |

**Center**, **Size**, **Uniform Scale**, and **Rotate** place the shape. **Lock Radius** keeps the relevant radial dimensions tied together. The viewport handle lets you work with the shapes through a common interface.

Box edges can be **Sharp**, **Chamfer**, **Bevel**, or **Combined**. Combined uses both Radius and Radius 2. Tube edges offer Sharp, Chamfer, and Bevel. These controls shape the primitive itself. They are separate from the Radius under Combine Settings, which changes the transition where two shapes meet.

**Taper** narrows the primitive along the chosen X, Y, or Z direction. **Taper Amount** controls the strength and sign; the two axis toggles choose which transverse dimensions change. Build up the amount gradually, especially near a fully collapsed end.

**Shell** turns the primitive's surface into a wall. See [Combining shapes](combining-shapes.md) for combination modes and shell thickness behavior.

## SDFM Curve Wire

Use Curve Wire for cables, pipes, rounded linework, and similar shapes. Connect an open or closed curve. Unlike an extruded profile, a wire can follow a fully 3D path.

The wire has a circular cross section with round ends and joins. **Wire Radius** sets its base radius. A source `pscale` attribute multiplies that radius along the curve. Vertex `pscale` takes priority over point `pscale`; with neither, the multiplier is 1. Negative or nonfinite multipliers are rejected.

| Control | What it does |
| --- | --- |
| Radius Interpolation | Linear, Smoothstep, Monotone Cubic, or Bezier Control Values |
| Corner Style | Keeps polygon corners as authored, chamfers them, or adds a round fillet |
| Corner Radius | Sets the polygon corner treatment size, limited by neighboring edge lengths |
| Treat All Curves as One Primitive | Evaluates all input wires as one shape, then applies Shell and Combine once |

By default, multiple input curves stay separate prims, so their combination settings are applied between them. Enable **Treat All Curves as One Primitive** for a hard union of the wires before combining them with the rest of the model. This is useful for a connected grid or several disconnected cables that should act as one object. It doesn't weld endpoints or add connections between unrelated curves.

Polygon and cubic Bezier curves are supported. There is also an internal NURBS-to-Bezier conversion path. Bezier corners come from the control handles; polygon Corner Style doesn't replace those handles. Bezier Control Values uses the radius values on the Bezier handles too; on polygons it behaves like linear interpolation.

## SDFM Curve Extruded

Use Curve Extruded for flat profiles with depth: panels, lettering outlines, slots, and shaped cutouts.

**Work Plane** offers Auto, XY, XZ, and YZ. The curve is aligned and flattened into a local plane before extrusion, then placed back using the primitive transform. Use planar source curves for predictable results. This is a profile extrusion, so a nonplanar curve's depth variation isn't preserved as it is with Curve Wire.

| Control | What it does |
| --- | --- |
| Filled Profile | Fills a closed outline before extrusion |
| Stroked Profile | Gives an open or closed curve a planar width before extrusion |
| Profile Half Width | Sets the stroked profile's half width, multiplied by source `pscale` |
| Profile Offset | Expands or shrinks the 2D profile without moving the extrusion limits |
| Finite / Infinite | Adds finite depth, or extends the profile along its extrusion axis |
| Unidirectional | Extrudes from the profile plane by Extrude Amount; negative values go backward |
| Bidirectional | Centers the extrusion on the plane; the absolute amount is the total depth |

Stroked profiles share Curve Wire's radius interpolation choices and vertex-before-point `pscale` priority.

**Cap Edge Style** controls the rim between the profile walls and the end caps: Sharp, Chamfer, or Round / Bevel. Top and Bottom Edge Style can inherit the shared setting or override it with their own style and radius. Cap radii are limited to half the extrusion depth.

**Profile Corner Style** changes the outline's polygon corners before extrusion. This is separate from the cap edges. Original, Chamfer, and Round Fillet are available; Bezier outlines use their handles instead.

Infinite extrusion is still limited by the assembler's finite working region. It doesn't create an infinitely large output volume.

### Multiple outlines and holes

Separate input curves become separate prims. An inner outline isn't automatically treated as a hole in an outer outline. To make a hole, create a separate inner cutter, set its Mode to Difference, and place it after the outer shape. Group the result if you want to reuse the complete profile.

### Curve detail

Curved spans, nonlinear radius changes, and round polygon corners are sampled during preparation. The assembler then evaluates that prepared shape on its voxel grid. Small details can therefore be limited by both the prepared curve approximation and voxel resolution. Increasing assembly resolution helps the voxel side, but doesn't make the prepared curve mathematically exact.

Older mesh and volume primitive nodes are covered under [Legacy tools](legacy.md), rather than the current GPU workflow.
