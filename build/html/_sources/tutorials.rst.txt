.. include:: <isonum.txt>

Tutorials
==========================================

Besides these two text tutorials, you can also watch these video tutorials - here's a `playlist <https://www.youtube.com/watch?v=vuJLpnL73As&list=PLK0EO-cKorzRAEfwHoJFiIldiyiyDR3-2>`_.

#1 - Simple
******************************************
We will start from a completely empty project. Open SHADERed and click on `File `&rightarrow;` New `&rightarrow;` Empty` option.
SHADERed will ask you to choose a location for your newly created project. Once you select the location
you will have a completely empty project. Now we can start working on our shader.

Shader pass
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Each shader has to be assigned to a shader pass. Shader pass is just a `'container'` for vertex and pixel shader. Every item that is child will be rendered using only those shaders. 


To create a shader pass, click on the `Project` |rarr| `Create` |rarr| `Shader Pass`.
A new dialog window will open and ask you to enter this shader pass' name, paths to your
shaders (or let SHADERed automatically create shaders for you) and a shader `entry`. Shader `entry` is just
a name of the function in which your shader program starts. It is used only in HLSL shaders.
If you let SHADERed create shader files for you, it will create .glsl files - GLSL shaders. Though, if you
want to use HLSL (and we do want to use it in this tutorial), your files must end with a .hlsl extension.
So create two files that end with .hlsl .

You can change those values later by right clicking on your shader pass and clicking on
the `Properties` button. The shader pass will open in the property viewer window.


Notice that we also have another option in the property viewer that we didn't have while
creating the shader pass. That option is called "RT" (Render Texture). This option allows binding multiple render
textures to one shader pass. We will touch this subject more in tutorial #3.

3D objects
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
After we have created a shader pass, we can add 3D objects to the scene.
We do that by right clicking on the shader pass and selecting `Add` |rarr| `Geometry` (you can also create a 3D model and a render state). A popup window
will ask you to enter item name, geometry type and the size of your geometry item. You can
enter anything as the item name (there mustn't exist an item with the same name though).
There are plenty of geometry types that you can pick from. In this tutorial we will go with
the cube. Leave the size at (1,1,1).

You can save your progress using `File` |rarr| `Save` option
or by using the shortcut `CTRL+S` (you can change it in the options).

Notice that nothing is being rendered even though we have added a 3D object to the scene.
That is because we haven't added any code to the shaders and we haven't set up shader variables.

Shader code
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
After setting up the scene, double click on the shader pass to open shaders in the code
editor (or right click |rarr| `Edit Code`).
You can now start writing your shader code. For this tutorial's purposes, just copy and paste this vertex shader
code:

.. code-block:: c++

	cbuffer cbPerFrame : register(b0)
	{
		float4x4 matVP;
		float4x4 matGeo;
	};

	struct VSInput
	{
		float3 Position : POSITION;
		float3 Normal : NORMAL;
		float2 UV : TEXCOORD;
	};

	struct VSOutput
	{
		float4 Position : SV_POSITION;
		float4 Color : COLOR;
		float2 UV : TEXCOORD;
	};

	VSOutput main(VSInput vin)
	{
		VSOutput vout = (VSOutput)0;

		vout.Position = mul(mul(float4(vin.Position, 1.0f), matGeo), matVP);
		vout.Color = 1;
		vout.UV = vin.UV;

		return vout;
	}

and this pixel shader:

.. code-block:: c++

	struct PSInput
	{
		float4 Position : SV_POSITION;
		float4 Color : COLOR;
		float2 UV : TEXCOORD;
	};

	float4 main(PSInput pin) : SV_TARGET
	{
		return pin.Color;
	}

Hit `CTRL+F5` (recompile the whole project) or `F5` (recompile opened shader). Notice that
there is still nothing being displayed on the preview window. This time it is because the
vertex shader is using variables in the constant buffer (cbuffer) that we haven't defined yet.

Variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
You can send custom variables to the shader, edit them and see the results in real time
without needing to recompile your shaders or restart the program.

Right click on your shader pass |rarr| `Variables`. A window for creating variables will open - the so called "Variable Manager".
Choose the type of your variable and enter its name. Variable names are important - variable names in the Variable Manager and shader code must match.
The system column allows you to choose some type of the value that SHADERed will
automatically assign and update (time elapsed, cursor position, view matrix, etc...). If it is 
set to `--NONE--` you can enter your own value.

In this tutorial we will add two matrices: ViewProjection and GeometryTransform.


Result
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
After hitting CTRL+F5 you will see your result displayed in the preview window.

You can drag your cursor around preview window while holding right
mouse button pressed to rotate the camera around the origin. Or use your scroll wheel
to move forward/backwards. Left click on an object to select it. Left click on an empty 
part of a preview window (or use shortcut) to deselect the item.


#2 - Textured cube
******************************************
We will expand the project from the previous tutorial by adding a texture and rendering
a textured cube. You can load a texture by either drag and dropping it into the window or
by clicking `Project` in the menu bar |rarr| `Create` |rarr| `Texture` and then select
your texture file.

To bind a texture to our shader pass, right click on it |rarr| select `Bind` |rarr| then select the shader pass to which you want to bind the texture. The number in parenthesis will tell you to which slot the texture is bound.

Change the pixel shader code to:

.. code-block:: c++

	struct PSInput
	{
		float4 Position : SV_POSITION;
		float4 Color : COLOR;
		float2 UV : TEXCOORD;
	};

	Texture2D tex : register(t0);
	SamplerState smp : register(s0);

	float4 main(PSInput pin) : SV_TARGET
	{
		return tex.Sample(smp, pin.UV);
	}

You should now have a textured cube rendered in the preview window.
