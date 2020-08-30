.. include:: <isonum.txt>

Other pipeline items
==========================================
Besides shader passes, there are also compute and audio passes.

Compute pass
******************************************
Compute passes are pipeline items that are used to handle compute shaders. They can output 2D & 3D images and buffers.
You can specify the group size in the compute pass' properties.
This pipeline item functions similarly to shader passes except it doesn't render anything to the screen and can't have child items.

Audio pass
******************************************
Audio passes are pipeline items that are used to generate audio on the GPU.

The so called audio shaders should define a mainSound function which returns a `float2`/`vec2`.
The first (x) component is for the left channel while the second (y) component is
for the right channel. The values should be in [-1, 1] range.