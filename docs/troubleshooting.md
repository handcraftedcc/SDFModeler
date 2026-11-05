# Troubleshooting

## Nothing appears, or part of the model is missing

Check the assembler's working region first. Press **Fit Working Region**, then expand Size or Bound if a shell, blend, modifier, or infinite extrusion extends beyond the guides. The region stays fixed after fitting.

Make sure the first shape in each standalone expression uses Union. Difference needs something to cut, and Intersect needs an existing shape to overlap.

Check **Output Mode** too. Primitives (bypass) shows the source setup; it doesn't evaluate a final surface. For a quick check, use one SDFM Prim Box and the GPU Assembler with Mesh output.

## The cut or union affects the wrong objects

Check input order. Each shape combines with the result built before it. On a Merge SOP, the input list determines that order.

If several shapes should form one cutter, put them through SDFM Group and set the group's Mode to Difference. Set the members' modes to build the cutter itself. See [the group example](workflow.md#groups-build-the-cutter-first).

## An array clips my shape

SDFM Modifier arrays fold the evaluation space into cells or sectors. A shape extending across a boundary can be clipped. Try a smaller source shape or larger spacing, or use Prim Array for complete copies that can overlap freely.

## The bevel, groove, or wall disappears

Increase resolution or reduce the working region while keeping the model inside it. A feature smaller than the voxel spacing may disappear. Check whether you're editing the primitive's own edge radius or the Radius under Combine Settings; they affect different things.

Shell Thickness is a half thickness in the GPU evaluator. Also check whether Shell is enabled on each prim or on their enclosing group.

## Curves don't give the expected result

Filled extruded profiles need closed input. Use Stroked Profile for open outlines. Set the Work Plane explicitly if Auto doesn't match your intended plane, and use planar input for an extrusion.

For variable thickness, inspect the source `pscale`, especially vertex values that override point values. Bezier Control Values uses handle values too. Curve Wire preserves 3D paths; Curve Extruded flattens a profile before extrusion.

An inner outline doesn't automatically make a hole. Build it as a Difference shape after the outer profile. On Curve Wire, **Treat All Curves as One Primitive** unions the wires before applying Shell and Combine; turn it off if you want the individual wires to blend through their combination settings.

## A mesh or volume prim becomes a box

The current GPU type table supports the seven analytic shapes and the two current curve shapes. It has no general mesh or volume primitive evaluator, and unknown type names fall back to Box. Use the [legacy workflow](legacy.md) for those older assets, with compatibility checked in your scene.

## It cooks slowly

Lower resolution first. Keep the workspace close to the model, and avoid fitting it again on every edit. Then check the number of copied prims and the complexity of the input curves. Mesh output also adds extraction time after the SDF evaluation.

## The result looks stale after changing asset files

Reload the affected HDA definitions or restart Houdini. Reload both the curve asset and the GPU Assembler if both changed. The Reset Cached Pass button in the current GPU definition has no callback, so don't use it as proof that the result was rebuilt.

## An older scene behaves differently

Check the exact node versions saved in the scene. This repository contains several generations of assembler and primitive definitions. Older assets may still work with an older assembler, but that isn't guaranteed. See [Legacy tools](legacy.md).
