# A bit of history

I started working on SDFModeler a few years ago after watching a video by Mark Fancher about some SDF work they did at ABC Studios. I also came across the [Mercury hg_sdf resource](https://mercury.sexy/hg_sdf/), which provided a good bit of the base code for the SDF operations.

Initially I was just messing around with it, implementing the functions in VEX. But I really enjoyed the process and ended up building it into a toolset. I submitted it to the SideFX Tech Art Challenge and won first place!

## The original VEX version

The main issue was performance. Groups used nested packed primitives, and evaluating them in VEX meant working through the shapes with for loops. It worked, but more complex setups could get pretty slow.

I tried a few times to move the evaluation to OpenCL. This was before the easier OpenCL bindings and before the newer COPs workflow, so there was quite a bit more to set up. Another tricky part was keeping upstream changes from rebuilding the volume and uploading it to the GPU again. That could easily eat up the benefit of doing the evaluation there in the first place.

## Moving to the GPU

When the newer COPs tools came out, I realized they could handle GPU volumes and keep them on the GPU. I was also inspired by how SideFX handles groups in its implicit surface workflow.

With help from an LLM, I translated my original VEX code to OpenCL, and it ran without issues. Since then I've added a few smaller tools and modifiers and adjusted the workflow around GPU evaluation, including a fixed workspace size.

The goal is still the same: make it quick to build and adjust shapes in Houdini, with each primitive carrying its own combination settings and a consistent handle workflow.

Some older nodes remain in the repository. They might still work to some degree with an older assembler, but this isn't guaranteed. The [legacy tool reference](legacy.md) separates those from the current GPU workflow.

## SDFM vs. Houdini's implicit surface tools

They share a lot of core functionality: describe shapes as functions, combine them, then evaluate a surface when you need it.

SDFM's focus is a fast modeling workflow. The main differences for this toolset are its selection of primitives and combination treatments, each primitive carrying its own combination settings, and the unified SDFM Prim handle workflow.

Compared with the SDFM options documented here, SideFX's documented implicit primitive and operation menus offer a smaller selection. Their workflow uses separate operation nodes to combine or modify shapes, while SDFM puts the combination controls on the incoming prim or group. That makes the order of the source shapes a central part of working in SDFM. See SideFX's [implicit surface workflow](https://www.sidefx.com/docs/houdini/model/implicitsurfacesworkflow.html) for its current capabilities; the two toolsets can continue to evolve independently.

## Credits

Thanks to the Mercury team for releasing hg_sdf under the MIT license. The OpenCL implementation started from the original VEX implementation, with AI assistance for the translation and later additions including modifiers and the newer group structure.
