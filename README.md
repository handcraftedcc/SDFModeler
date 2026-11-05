# SDFModeler
 A free, open source SDF Modeling Toolset for Houdini!

 UPDATE 2.0 - Now running on the GPU using OpenCL in Cops!

Start with the [user documentation](docs/README.md) and [overall workflow](docs/workflow.md). There are guides for primitives, modifiers, groups, the GPU Assembler, and older tools, along with a bit of project history.

The [original documentation and tutorials](https://www.notion.so/dominiklange/SDF-Modeler-d965edcc82b64262ab4e5aed102daa12?pvs=4) are still available, though older examples may use the VEX workflow. Developer notes live in [docs/agent](docs/agent/README.md).

## Installation
1. Download and unpack zip file into folder of your choice.
2. Copy SDFM.json to your Houdini version's `packages` folder (create it if needed). For example:
	```
	Windows: c:/Users/<yourusername>/Documents/houdini22.0/packages
	OSX: ~/Library/Preferences/houdini/22.0/packages
	Linux: ~/houdini22.0/packages
	```
3. Replace ```"PATH/TO/PACKAGE/"``` with path to the folder. In my case that will read: ```"C:/Users/Dominik/Documents/Houdini Assets/Project Packs/SDFModeler"```
	Make sure to use ```"/"``` as seperators, not ```"\"``` otherwise it won't work.

The GPU assembler and curve assets have been validated with Houdini 22.0.368. Compatibility with older Houdini versions has not been verified.

## Credit
A good bit of the base code for the SDF operations were taken from https://mercury.sexy/hg_sdf/ (The link sounds sketchy - but it's not - the team there just picked a quirky URL - it's just some SDF code)
Thanks to the mercury team for releasing the code under the MIT license!

## AI Disclaimer
AI was used to translate my original VEX code into opencl along with a few other additions to the solver written in opencl (modifiers, and new groups structure)
