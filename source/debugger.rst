.. include:: <isonum.txt>

Debugger
==========================================
SHADERed comes with a shader debugger. The debugger currently only supports vertex and pixel shaders.

Pixel Inspect
******************************************
Pixel inspect contains the information about the selected pixel.
To select a pixel, first pause the preview then left click on a pixel.
SHADERed will also automatically select all related pixels in every render texture.

Click the "FETCH" button (if visible) to fetch additional information about the pixel.

The first color shown is the one that has been read directly from the texture.
The second color is calculated by the debugger. If the second color
strongly deviates (small differences can be ignored) from the first color, it means that an error has occured
in the debugger while executing the shader. I highly suggest to avoid debugging such shaders.

The pixel inspect will also display information about the vertices that this pixel belongs to.

Overlay
******************************************
Once the debugger fetches information about the pixel, you will see
lines that show you excatly what vertices and what pixel you've selected.

.. image:: images/OverlayUI.png
   :alt: Debugger's overlay UI

Debugging
******************************************
To start the pixel shader debugger, press the play button next to the
color in the pixel inspect window. To start the vertex shader debugger,
press the play button next to the vertex that you want to debug.

You have few options when the debugger starts:

 * Stop |rarr| stops the debugger
 * Step in |rarr| steps into the function 
 * Step out |rarr| steps out of the current function
 * Step |rarr| steps over a single line - does not step in the function if there is one on that line
 * Continue |rarr| executes the shader until it hits a breakpoint or completely finishes executing 

Arrow on the sidebar will show you what line is next to be executed.

You can hover over variables to see their values while debugging.
You can also hover over parenthesis to execute the expression located under the cursor.

.. image:: images/TextEditorDebugging.png
   :alt: The look of the debugger's interface

Breakpoints
******************************************
You can place breakpoints by clicking on the bar that contains the line numbers.
Right click on the breakpoint will open a context menu with several options.
You can temprorarily disable breakpoint or place a condition.
The so called conditional breakpoint will stop the debugger only if the entered condition is met.

You can see list of breakpoints in the "Breakpoints" window.

Variables
******************************************
In the variables window you can see all the variables that are accessible (globals, locals & arguments).

Immediate mode
******************************************
Immediate window lets you run custom expressions while debugging the shader.

.. image:: images/ImmediateMode.png
   :alt: Immediate mode

Watches
******************************************
Watches window is similar to immediate mode except it allows storing multiple expressions
which will then be executed every time you step through your code.

.. image:: images/Watches.png
   :alt: Watches