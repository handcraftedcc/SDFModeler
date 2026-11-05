# Modifiers

There are two approaches here. Prim modifiers work on the actual source prims. SDF modifiers change the function used to evaluate them. Both are useful, but their arrays and mirrors behave differently.

## Prim modifiers

### SDFM Prim Array

Prim Array creates actual copies of the input prims. The copies retain their shape and combination attributes and enter the assembly as source elements.

| Control | What it does |
| --- | --- |
| Source Group | Restricts which source prims are copied |
| Mode | Directional Total, Directional Individual, Radial Total, or Radial Individual |
| Total Number | Sets the number of elements |
| Total / Individual Degrees | Controls the radial layout across the whole array or per step |
| Translate, Rotate, Scale | Controls the array transform according to the selected mode |
| Pivot Translate / Rotate | Places and orients the transform pivot |
| Set Pivot To Centroid | Starts the pivot at the source's center |
| Transform / Rotate Order | Changes the order in which transformations are applied |

The Total modes distribute the transformation across the array. The Individual modes apply a per-step transformation. Start with a small Total Number while setting up the spacing and pivot.

### SDFM Prim Mirror

Prim Mirror mirrors actual prim geometry across a plane. **Origin**, **Direction**, and **Distance** define the plane. **Group** restricts the source selection, **Keep Original** retains the original alongside the reflection, and **Create Output Group** names the mirrored result for later selection.

This is useful when you want whole mirrored copies, including parts that cross the mirror plane.

## SDFM Modifier

Connect the shapes to the modifier's inputs, then feed its output into your assembly. The modifier wraps that branch so its effect stays with those upstream shapes. It doesn't deform the already meshed output of the assembler.

**Center** and **Rotate** define the modifier's local coordinate system. Rotate the modifier to change its axis or mirror plane. Multiple modifiers can be chained, and their order changes the result.

### Mirror

The plane is local X=0. With **Clip** enabled, **Keep Side** chooses +X or -X, and that side is reflected across the plane. Without Clip, the domain is flipped, giving a reflection of the whole input rather than keeping both halves.

This differs from Prim Mirror's **Keep Original** option. Use Prim Mirror when you need independent whole copies.

### Radial Array

Repeats around local Y. **Count** sets the number of sectors and **Mirror** flips alternating sectors.

This folds the evaluation space into a sector. Shapes extending beyond that sector can be clipped at its boundaries. If you want overlapping complete copies, use Prim Array instead.

### Array

Enable X, Y, or Z to repeat along those local axes. **Offset** controls spacing, and a negative offset reverses the direction. A positive **Count** gives a finite sequence starting at cell 0. A count of 0 or below leaves the repetition unbounded on that axis, within the assembler's working region. Zero spacing leaves that axis unchanged.

**Mirror** flips alternating cells. Like the radial version, this is a folded domain, so geometry crossing cell boundaries may be clipped. Large shapes relative to the spacing are the most obvious case.

### Bend

Bends in the modifier's local XY plane. **Bendamount** controls curvature: one unit corresponds to 90 degrees per local X unit. It is a curvature control rather than a total angle across the input's bounds, so the same value bends a longer object further.

Move and rotate the modifier to place the bend. Start with a small amount; tight bends and overlapping regions can distort the field.

### Twist

Twists the local XZ cross section along local Y. **Twistamount** is turns per local Y unit: 1 corresponds to 360 degrees over one unit of height. Rotate the modifier to twist along another direction.

### Combining the result

The modifier's Combine Settings control how its modified branch joins the surrounding assembly. For example, you can build a repeated pattern of cutters in a modifier branch, then set that branch to Difference to cut the pattern from a body.

The current evaluator supports up to 16 nested group levels and 16 active modifiers at a time. Modifiers themselves introduce a group boundary, so keep this in mind for deeply nested setups.
