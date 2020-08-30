.. include:: <isonum.txt>

Embed API
==========================================
You can embed shaders (that are hosted on shadered.org) into your own website.

Add an iframe with src set to ``shadered.org/embed?id=ENTER_ID_HERE`` to your website.

There are some additional parameters that you can set in the url:


+---------------------+-----------------------------------------------------------------------------+
| Name                | Description                                                                 |
+---------------------+-----------------------------------------------------------------------------+
| rotate              | set to 1 if you want your camera to rotate around the origin                |
+---------------------+-----------------------------------------------------------------------------+
| ui_controls         | set to 0 if you want to hide bottom bar (which contains various controls)   |
+---------------------+-----------------------------------------------------------------------------+
| ui_title            | set to 0 if you want to hide top bar (which contains project's title)       |
+---------------------+-----------------------------------------------------------------------------+
| ui_variables        | set to 0 if you want to hide the menu that contains variables               |
+---------------------+-----------------------------------------------------------------------------+
| pin_ui_controls     | set to 1 if you want the bottom bar to be always visible                    |
+---------------------+-----------------------------------------------------------------------------+
| paused              | set to 1 if you want the preview to be paused on startup                    |
+---------------------+-----------------------------------------------------------------------------+
| mouse               | set to 0 if you want to disable mouse controls                              |
+---------------------+-----------------------------------------------------------------------------+


Javascript functions
******************************************

You can also call these Javascript functions:

.. function:: pause()

	Pauses the preview.

.. function:: resume()

	Resumes the preview.

.. function:: isPaused()

	:return: true if the preview is paused
	:rtype: bool

.. function:: setVariable(name, array)

	Change the value of a pinned variable.

	:param string name: Name of the variable whose value we want to change
	:param array array: either a int or float array - a new value

.. function:: getVariables()

	Get list of all pinned variables.
	
	:return: list containing variable names
	:rtype: StringVector

.. function:: getVariableType(name)

	Get the type of the variable

.. function:: pushShader(pass, stage, glslesSource)

	Update the GLSL ES source code of a shader pass

.. function:: getShader(pass, stage)

	Get shader code.

.. function:: getShaderPassList()

	Get list of all shader passes
	
	:return: list containing shader pass names
	:rtype: StringVector

.. function:: advanceTime(t)

	Increase the time by `t`.
	
.. function:: restartTime()

	Set elapsed time to 0.0

.. function:: getTime()

	Get elapsed time

.. function:: getFPS()

	Get current FPS

.. function:: getRendererWidth()

	Get preview width

.. function:: getRendererHeight()

	Get preview height

.. function:: getShaderPassVertexEntry(pass)

	Get vertex shader's entry function name
	
.. function:: getShaderPassPixelEntry(pass)

	Get pixel shader's entry function name
	
	
The iframe will also send a 'onprojectinit' message once the .sprj has been fully loaded.

.. image:: images/WebPinned.png
   :alt: Website pinned variables
