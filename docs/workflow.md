# Overall workflow

The general idea is pretty simple: create some shapes, decide how they should combine, and plug them into an assembler. Most of the nodes before the assembler describe the model. The assembler is where those descriptions become an evaluated surface.

An SDF (signed distance field) describes a shape using distances: negative inside, positive outside, and zero on the surface. This lets us combine and modify shapes without having to manage their polygon topology at every step.

## The main pieces

| Category | What it does |
| --- | --- |
| Prims | Describe individual shapes. SDFM Prim includes Box, Sphere, Tube, Capsule, Torus, Pyramid, and Octahedron. Curve Wire and Curve Extruded cover curve-based shapes. |
| Prim modifiers | Copy or transform the actual source prims. Prim Array and Prim Mirror belong here. |
| SDF modifiers | Change the coordinates used to evaluate upstream shapes. Mirror, arrays, Bend, and Twist are available in SDFM Modifier. |
| SDFM Group | Evaluates the input shapes as one standalone expression, then combines that result with what came before. |
| SDFM GPU Assembler | Evaluates the setup in COPs using OpenCL. Outputs a mesh by default, with VDB and primitive bypass options too. |

## Your first model

1. Inside a Geometry object, create an **SDFM Prim**. Leave it as a Box.
2. Create another SDFM Prim and choose Sphere. Move it so it overlaps the box.
3. Set the sphere's **Combine Settings → Mode** to **Difference**.
4. Connect the box to the first input of an **SDFM GPU Assembler** and the sphere to the next input. You can also merge them first, with the box above the sphere in the Merge SOP's input list.
5. Press **Fit Working Region** on the assembler. Add some space around the model if needed, and display the assembler's first output.

You should now have a box with a spherical cutout. Move or resize the sphere to change the cut. For a softer transition, try the Difference **Round** type and increase the combination **Radius**.

The primitive viewport shapes are guides. They help you place and edit the objects, but the combined result comes from the assembler. Select a prim and press Enter in the viewport to use its handle. SDFM Prim keeps the different built-in shapes in the same node and handle workflow, so switching shapes doesn't require rebuilding the setup.

## Order matters

Shapes are evaluated in stream order. The first shape establishes the starting object; each following shape uses its own combination settings to modify the result so far. Start a new expression with a Union shape.

For example, with three prims in this order:

```text
Box (Union) → Sphere (Difference) → Tube (Union)
```

The sphere cuts the box, then the tube is added to that result. These arrows describe evaluation order; connect the separate prims to ordered Merge or assembler inputs.

If you swap the sphere and tube, the sphere cuts the combined box and tube. Same shapes, different result! This also applies to the order inside groups.

## Groups: build the cutter first

Sometimes you want several shapes to behave as one object. A complex cutout is a good example. You might want to combine a box and two tubes into one cutter, then subtract the whole thing from your model.

Connect the cutter shapes to an **SDFM Group**. Build their internal shape with their own Combine Settings, then set the group's Mode to **Difference**. Connect your main body first and the group second to the assembler.

```text
Box cutter ──┐
Tube cutter ─┼── SDFM Group (Difference) ──┐
Tube cutter ─┘                            │
Main body ────────────────────────────────┴── GPU Assembler
                                             body input first
```

The group is evaluated on its own before it cuts the body. Without the group, each cutter would combine directly with the accumulated model. A Union inside your cutter branch could end up adding material back to the body.

SDFM Group is an evaluation boundary. A normal Houdini primitive group or Merge SOP doesn't create this behavior. Groups can be nested, and a group's Shell setting acts on its combined shape.

## Applying modifiers

Connect the shapes you want to affect into an **SDFM Modifier**, then pass that node into the assembler. It affects the shapes connected upstream of that modifier, including groups within that branch. Shapes on a separate assembler branch stay outside its scope.

Prim Array and Prim Mirror create or change actual source prims. SDFM Modifier's arrays and mirror work by folding the SDF evaluation space. This makes a different kind of repetition, particularly where shapes cross cell boundaries. See [Modifiers](modifiers.md) for examples and settings.

## Preview a part, then reuse it

An assembler has two outputs:

1. **SDF Output**: the evaluated result, normally a mesh.
2. **Assembly Output**: the source shape setup enclosed as a group.

Use the first output to preview a subcomponent, then connect the **second output** to a larger assembler. Set the subcomponent assembler's Combine Settings to control how that entire part combines in the larger model.

This keeps the part editable. The larger assembler evaluates its source shapes at the larger assembler's resolution and working region; it doesn't reuse the preview mesh or a cached subcomponent volume. For example, you can preview a handle separately, then use its Assembly Output to add it to a larger body.

Next: [Primitives](primitives.md) · [GPU Assembler](assembler.md)
