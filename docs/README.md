# SDFModeler documentation

SDFModeler is a free, open source SDF modeling toolset for Houdini. Build shapes from primitives, combine them, add modifiers, and evaluate the result on the GPU using OpenCL in COPs.

If you're just getting started, read [the overall workflow](workflow.md) first. It covers how the nodes fit together, why order matters, and how to build reusable parts.

| Guide | What's in it |
| --- | --- |
| [Workflow](workflow.md) | Your first setup, groups, modifiers, and subassemblies |
| [Primitives](primitives.md) | Built-in shapes, Curve Wire, and Curve Extruded |
| [Combining shapes](combining-shapes.md) | Boolean modes, edge treatments, shell, and properties |
| [Modifiers](modifiers.md) | Copying actual prims vs. modifying the SDF |
| [GPU Assembler](assembler.md) | Working region, resolution, outputs, and COP processing |
| [Troubleshooting](troubleshooting.md) | Missing shapes, clipped arrays, curve issues, and slow cooks |
| [Legacy tools](legacy.md) | Older assemblers, mesh/volume prims, and mesh utilities |
| [History and implicit surfaces](history.md) | Where the toolset came from and how it compares |

Installation is covered in the [repository README](../README.md). These guides describe the definitions in this repository, with SDFM Prim 1.6 and GPU Assembler 1.00 as the main workflow. Existing curve notes record validation with Houdini 22.0.368; this is not a compatibility guarantee for every node or Houdini version.
