.. include:: <isonum.txt>

Plugin API
==========================================
You can upload plugins `here </upload_plugin>`_ so that they can be installed through the "Browse online" window.
The .zip file containing binaries that you upload *must* contain the following file: ``/plugins/PluginName/plugin.dll`` where PluginName can be anything (though it really should be equal to the ID of your plugin). The .zip file's content will be extracted directly into the SHADERed's installation directory which means that you can provide additional .dll libraries, example projects, data files, etc...

To update your plugin, go to your profile page and click on "My plugins", find the plugin that you want to update and click on the "UPDATE" button. You can change the description, title & upload new .zip files.

Once uploaded/updated, the plugin will be waiting for a review. We do this to ensure quality content.

Every plugin in the database must have unique ID.

Building a plugin
******************************************
Copy files found in ``/src/SHADERed/Objects/Plugins`` to your plugin's project.
`PluginData.h` file contains some object definitions and enums that SHADERed will use to communicate with the plugin.
`Plugin.h` file contains definitions for IPlugin1, IPlugin2, etc..

Start by creating a class that inherits one of the IPlugin interfaces. This class will be the main part of your plugin.
In that class you'll have to implement all of the functions. Even if you don't use some Plugin API's feature you will have
to leave the function's body empty.

You will also have to implement these five functions:

.. function:: CreatePlugin()

	:return: a pointer to a instance of your class which inherits IPlugin
	:rtype: IPlugin*

.. function:: DestroyPlugin(ptr)

	:param IPlugin* ptr: a pointer to a object that CreatePlugin() returned

.. function:: GetPluginAPIVersion()

	:return: version of PluginAPI that this plugin uses; this function must return 3 
	:rtype: int

.. function:: GetPluginVersion()

	:return: version of your plugin; the return value will be used to compare the version of this plugin with the version available in the databse (the one you provide while uploading/updating the plugin) to check if there has been any updates
	:rtype: int
	
.. function:: GetPluginName()

	:return: name of your plugin that will be used everywhere
	:rtype: const char*




IPlugin1
******************************************

Features
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
In this section we will describe all the methods that you can override.


General
++++++++++++++++++++++++++++++++++++++++++
.. function:: Init(isWeb, sedVersion)

	This function is called immediately after the plugin has been loaded from the .dll

	:param bool isWeb: whether or not this plugin is running on the web
	:param int sedVersion: encoded version of SHADERed (1003006 for v1.3.6, 1004000 for v1.4, etc...)
	:return: true if initialization completed successfully, false otherwise
	:rtype: bool

.. function:: InitUI(ctx)

	Implement this function only when your plugin uses UI. You must use imgui library and also it must match the version that SHADERed uses.

	:param void* ctx: a pointer to ImGuiContext* object

.. function:: Update(delta)

	This function is called every frame between event handling code and rendering code.
	This is where you should call ImGui functions if you use it.

	:param float delta: time elapsed between previous and this frame

.. function:: Destroy()

	Called when plugin is unloaded.

.. function:: IsRequired()

	This function will be called when saving a project file & if this plagin output some data in that project file (.sprj).
	
	:return: true if this project can't be opened without this plugin, false otherwise
	:rtype: bool

.. function:: IsVersionCompatible(version)

	This function is called when loading a project that requires this plugin. It is used to check if the older/newer version of this plugin's project data is compatible with user's version of plugin.
	
	:param int version: version of the plugin used in the project file
	:return: true if plugins are compatible and project can be loaded, otherwise false
	:rtype: bool

.. function:: BeginRender()

	This method is called right before the rendering starts.

.. function:: EndRender()

	This method is called right after the rendering ends.

Project
++++++++++++++++++++++++++++++++++++++++++

Functions related to project loading/saving.

.. function:: Project_BeginLoad()

	This method is called before project loading starts. It's only called for plugins that were used in this project.

.. function:: Project_EndLoad()

	This method is called right after project loading ends. It's only called for plugins that were used in this project.

.. function:: Project_BeginSave()

	This method is called before project saving starts. It's only called for plugins that were used in this project.

.. function:: Project_EndSave()

	This method is called right after project saving ends. It's only called for plugins that were used in this project.

.. function:: Project_HasAdditionalData()

	Called when saving to a project file. Used to check if plugin wants to export some addional data to the project file.

	:return: true if this plugin would like to export some extra data to .sprj file, otherwise false
	:rtype: bool

.. function:: Project_ExportAdditionalData()

	Called if Project_HasAdditionalData() returned true
	
	:return: a string containing XML data
	:rtype: const char*

.. function:: Project_ImportAdditionalData(xml)

	Called when loading a project if there is any extra data tied to this plugin in the .sprj file.

	:param `const_char*` xml: version of the plugin used in the project file

.. function:: Project_CopyFilesOnSave(dir)

	Called when saving a project but also copying all the files related to it (when "Save As" is used).

	:param const_char* dir: path to a directory in which the project is being saved

Menu
++++++++++++++++++++++++++++++++++++++++++

UI methods for adding items to the menu bar & its menus.

.. function:: bool HasCustomMenuItem()

	Called each frame to check if the plugin wants a special menu in the menu bar. The menu will user GetPluginName() for display string. 

	:return: a string containing XML data
	:rtype: bool

.. function:: HasMenuItems(name)

	This method will be called to check if plugin wants to add custom options to one of the menus in the menu bar.

	:param const_char* name: name of the parent menu

		* "file" |rarr| parent is the `File` menu in the menu bar
		* "newproject" |rarr| parent is the `File` |rarr| `New`
		* "project" |rarr| parent is the `Project` menu in the menu bar
		* "createitem" |rarr| parent is the `Project` |rarr| `Create`
		* "window" |rarr| parent is the `Window` menu in the menu bar

	:return: true if this plugin wants to add its own items to the menu, false otherwise
	:rtype: bool

.. function:: ShowMenuItems(name)

	Call ImGui::MenuItem() to add your own items depending on the given parameter

	:param const_char* name: name of the parent menu

		* "file" |rarr| parent is the `File` menu in the menu bar
		* "newproject" |rarr| parent is the `File` |rarr| `New`
		* "project" |rarr| parent is the `Project` menu in the menu bar
		* "createitem" |rarr| parent is the `Project` |rarr| `Create`
		* "window" |rarr| parent is the `Window` menu in the menu bar
		* "custom" |rarr| parent is the `"GetPluginName()"` menu in the menu bar

		
Context
++++++++++++++++++++++++++++++++++++++++++

UI methods for adding items to the context menus.

.. function:: HasContextItems(name)

	This method will be called to check if plugin wants to add custom options to one of the context menus.

	:param const_char* name: name of the context menu, can be one of these values:

		* "pipeline" |rarr| right click in the "Pipeline" window
		* "shaderpass_add" |rarr| right click on a shader pass item |rarr| `Add`
		* "pluginitem_add" |rarr| right click on a plugin item in the pipeline window |rarr| `Add`
		* "objects" |rarr| right click in the "Objects" window
		* "editcode" |rarr| right click on a plugin item |rarr| `Edit code`
		
	:return: true if this plugin wants to add its own items to the context menu, false otherwise
	:rtype: bool

.. function:: ShowContextItems(name, [owner = nullptr], [extraData = nullptr])

	Call ImGui::MenuItem() to add items depending on the given parameter

	:param const_char* name: name of the context menu, can be one of these values:

		* "pipeline" |rarr| right click in the "Pipeline" window
		* "shaderpass_add" |rarr| right click on a shader pass item |rarr| `Add`
		* "pluginitem_add" |rarr| right click on a plugin item in the pipeline window |rarr| `Add`
		* "objects" |rarr| right click in the "Objects" window
		* "editcode" |rarr| right click on a plugin item |rarr| `Edit code`
		
	:param void* owner: the item that we right clicked on

		* "shaderpass_add" |rarr| pointer to a PipelineItem (PipelineItem*) that we right clicked on
		* "pluginitem_add" |rarr| the item type (const char*) of the plugin PipelineItem that we right clicked on 
		* "editcode" |rarr| name of the PipelineItem
		
	:param void* extraData: additional data supplied

		* "pluginitem_add" |rarr| pointer to PluginItemData structure

System variables
++++++++++++++++++++++++++++++++++++++++++

Methods for implementing your own system variables.

.. function:: SystemVariables_GetNameCount(varType)

	Get number of system variables for given variable type.

	:param VariableType varType: the type of the variable
	:return: number of system variables for given variable type
	:rtype: int

.. function:: SystemVariables_GetName(varType, index)

	Get the name of the system variable.
	
	:param VariableType varType: the type of the variable
	:param int index: number in range [0, SystemVariables_GetNameCount(varType)-1]
	:return: name of the system variable at the given index
	:rtype: const char*

.. function:: SystemVariables_HasLastFrame(name, varType)

	Does this system variable support last frame value?

	:param char* name: name of the system variable
	:param VariableType varType: type of the variable
	:return: true if the "Use values from last frame" flag can be used on this system variable
	:rtype: bool

.. function:: SystemVariables_UpdateValue(data, name, varType, isLastFrame)

	Modify variable's value (stored in the `data` parameter)

	:param char* data: variable's value (input & output)
	:param char* name: system variable name
	:param VariableType varType: variable type
	:param bool isLastFrame: true if the "Last frame" flag is checked, otherwise false

Function variables
++++++++++++++++++++++++++++++++++++++++++

Methods for implementing your own variable functions.

.. function:: VariableFunctions_GetNameCount(vtype)

	Get number of functions for given variable type.

	:param VariableType vtype: the type of the variable
	:return: number of function variables for given variable type
	:rtype: int

.. function:: VariableFunctions_GetName(ed::plugin::VariableType varType, int index)

	Get the name of the function.

	:param VariableType varType: the type of the variable
	:param int index: number in range [0, VariableFunctions_GetNameCount(varType)-1]
	:return: name of the function variable at the given index
	:rtype: const char*

.. function:: VariableFunctions_ShowArgumentEdit(fname, args, vtype)

	Show the UI for editing function's arguments

	:param char* fname: name of the function
	:param char* args: argument values
	:param VariableType vtype: the type of the variable
	:return: -- not used --
	:rtype: bool

.. function:: VariableFunctions_UpdateValue(data, args, fname, varType)

	Update the value of a variable based on the function and arguments.

	:param char* data: variable's value (input & output)
	:param char* fname: name of the function
	:param char* args: argument values
	:param VariableType vtype: the type of the variable

.. function:: VariableFunctions_GetArgsSize(fname, varType)

	Get the size required to store the arguments for this function.

	:param char* fname: function name
	:param VariableType varType: the type of the variable
	:return: number of bytes required to store arguments for this function
	:rtype: int

.. function:: VariableFunctions_InitArguments(args, fname, vtype)

	Initialize arguments to default values.

	:param char* args: output
	:param char* fname: function name
	:param VariableType vtype: the type of the variable

.. function::  VariableFunctions_ExportArguments(fname, vtype, args)

	Export arguments in the project file.

	:param char* fname: function name
	:param VariableType vtype: the type of the variable
	:param char* args: argument values
	:return: XML containing information about argument values
	:rtype: const char*

.. function:: VariableFunctions_ImportArguments(fname, vtype, args, argsString)

	Import arguments from the project file.

	:param char* fname: function name
	:param VariableType vtype: the type of the variable
	:param char* args: argument values
	:param const_char* argsString: XML containing information about argument values

Objects
++++++++++++++++++++++++++++++++++++++++++

Methods for handling and implementing custom objects.

.. function:: Object_HasPreview(type)

	Check if the custom object has a preview in the context menu (when user right clicks on the object).

	:param const_char* type: the type name of the object
	:return: true if the object has preview, false otherwise
	:rtype: bool

.. function:: Object_ShowPreview(type, data, id)

	Show preview in the context menu.

	:param const_char* type: the type name of the object
	:param void* data: the data allocated by this plugin ("user data") and passed as an argument in AddObject() function
	:param uint id: object ID, the one passed as an argument when calling the AddObject()

.. function:: Object_IsBindable(type)

	Can this object be bound as a readonly object to shader and compute passes?

	:param const_char* type: the type name of the object
	:return: true if the object can be bound as a SRV, false otherwise
	:rtype: bool

.. function:: Object_IsBindableUAV(type)

	Can this object be bound as a UAV in compute passes?

	:param const_char* type: the type name of the object
	:return: true if the object can be bound as a UAV, false otherwise
	:rtype: bool

.. function:: Object_Remove(name, type, data, id)

	Method that handles when user deletes an object.

	:param const_char* name: name of the object
	:param const_char* type: the type name of the object
	:param void* data: the data allocated by the plugin
	:param uint id: id of the object

.. function:: Object_HasExtendedPreview(type)

	Check if the custom object has a preview that can be opened in a special window.

	:param const_char* type: the type name of the object
	:return: true if the object has "extended" preview, false otherwise
	:rtype: bool

.. function:: Object_ShowExtendedPreview(type, data, id)

	Show preview in a special window.

	:param const_char* type: the type name of the object
	:param void* data: the data allocated by this plugin ("user data") and passed as an argument in AddObject() function
	:param uint id: object ID, the one passed as an argument when calling the AddObject()

.. function:: Object_HasProperties(type)

	Can our object be opened in the properties window?

	:param const_char* type: the type name of the object
	:return: true if the object can be opened in properties window, false otherwise
	:rtype: bool

.. function:: Object_ShowProperties(type, data, id)

	Show properties UI for this object.

	:param const_char* type: the type name of the object
	:param void* data: the data allocated by this plugin ("user data") and passed as an argument in AddObject() function
	:param uint id: object ID, the one passed as an argument when calling the AddObject()

.. function:: Object_Bind(type, data, id)

	Bind your object to a shader (eg: call glActiveTexture, glBindTexture, etc...)

	:param const_char* type: the type name of the object
	:param void* data: the data allocated by this plugin ("user data") and passed as an argument in AddObject() function
	:param uint id: object ID, the one passed as an argument when calling the AddObject()

.. function:: Object_Export(type, data, id)

	Export information about your object to a project file

	:param const_char* type: the type name of the object
	:param void* data: the data allocated by this plugin ("user data") and passed as an argument in AddObject() function
	:param uint id: object ID, the one passed as an argument when calling the AddObject()
	:return: XML containing information about the object
	:rtype: const char*

.. function:: Object_Import(name, type, objString)

	Parse information about an object from a project file. This function should call AddObject so that SHADERed can actually add the object to object manager.

	:param const_char* name: the name of the object
	:param const_char* type: the type name of the object
	:param const_char* objString: XML containing information about the object

.. function:: Object_HasContext(type)

	Check if this plugin object should have more items in its context menu than the "Preview", "Properties" & "Delete" buttons.

	:param const_char* type: the type name of the object
	:return: true if there are additional context menu items, false otherwise
	:rtype: bool

.. function:: Object_ShowContext(type, data)

	Render the UI for the context menu items.

	:param const_char* type: the type name of the object
	:param void* data: the data allocated by this plugin ("user data") and passed as an argument in AddObject() function
	
Pipeline items
++++++++++++++++++++++++++++++++++++++++++

Methods for handling and implementing custom pipeline items.

.. function:: PipelineItem_HasProperties(type, data)

	Can our pipeline item be opened in the properties window?

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:return: true if the pipeline item can be opened in the properties window, false otherwise
	:rtype: bool

.. function:: PipelineItem_ShowProperties(type, data)

	Show properties UI for this pipeline item.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function

.. function:: PipelineItem_IsPickable(type, data)

	Check if the pipeline item can be selected through the preview window using left click.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:return: true if the pipeline item can be picked, false otherwise
	:rtype: bool

.. function:: PipelineItem_HasShaders(type, data)

	Check if the pipeline item has its own shaders.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:return: true if the pipeline item has its own shaders, false otherwise
	:rtype: bool

.. function:: PipelineItem_OpenInEditor(type, data)

	Handle a request to open this plugin's pipeline item's shaders in the text editor.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function

.. function:: PipelineItem_CanHaveChild(type, data, itemType)

	Check if an item of a certain type can be added as a child to this custom pipeline item.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:param PipelineItemType itemType: type of the child item

.. function:: PipelineItem_GetInputLayoutSize(type, data)

	Get number of input layout entries for this pipeline item. This must be supported if this item can have geometry as children.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:return: number of input layout entries
	:rtype: int

.. function:: PipelineItem_GetInputLayoutItem(type, data, index, out)

	Gets information about input layout item at a certain index.
	
	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:param int index: index of a input layout item
	:param InputLayoutItem& out: parameter in which you should store information about this input layout item

.. function:: PipelineItem_Remove(itemName, type, data)

	Method that handles when user deletes a pipeline item owned by this plugin.

	:param const_char* itemName: name of the pipeline item
	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function

.. function:: PipelineItem_Rename(oldName, newName)

	Method that handles when user renames a pipeline item owned by this plugin.

	:param const_char* oldName: old name of the pipeline item
	:param const_char* newName: new name of the pipeline item

.. function:: PipelineItem_AddChild(owner, name, type, data)

	Method that handles when user adss a child item to a pipeline item owned by this plugin.

	:param const_char* owner: name of the pipeline item to which this child item has been added
	:param const_char* name: name of the pipeline item
	:param PipelineItemType type: type of the pipeline item
	:param void* data: pointer to pipeline item's data

.. function:: PipelineItem_CanHaveChildren(const char* type, void* data)

	Check if an item can have child items.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:return: true if it can have children, false otherwise
	:rtype: bool

.. function:: PipelineItem_CopyData(type, data)

	Handle copying data allocated by this plugin to another item. This is used when user duplicates an item for example.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:return: pointer to a new object
	:rtype: void*

.. function:: void PipelineItem_Execute(Owner, OwnerType, type, data)

	Handle executing/rendering the child item. 

	:param void* Owner: pointer to a PipelineItem of the owner of this plugin's pipeline item that we are executing
	:param PipelineItemType OwnerType: the type of the owner
	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function

.. function:: PipelineItem_Execute(type, data, children, count)

	Execute/Render the pipeline item.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:param void* children: list of child items
	:param int count: number of child items

.. function:: PipelineItem_GetWorldMatrix(type, data, pMat)
	
	Get pipeline item's world matrix. This must be handled if item is pickable.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:param float* children: output - a 4x4 matrix

.. function:: PipelineItem_Intersect(type, data, rayOrigin, rayDir, hitDist)

	Check if a ray (generated when user is picking items) intersects the pipeline item

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:param float[3] rayOrigin: the position where the ray starts
	:param float[3] rayDir: the direction in which ray is going
	:param float& hitDist: output - store a distance between ray origin and intersection point if there is any intersection
	:return: true if ray intersects the pipeline item
	:rtype: bool

.. function:: PipelineItem_GetBoundingBox(type, data, minPos, maxPos)

	Get the pipeline item's bounding box.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:param float[3] minPos: output - the minimum coordinates
	:param float[3] maxPos: output - the maximum coordinates

.. function:: PipelineItem_HasContext(const char* type, void* data) = 0;

	Check if this plugin pipeline item should have additional items in its context menu.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:return: true if there are additional context menu items
	:rtype: bool

.. function:: PipelineItem_ShowContext(type, data)

	Render the UI for the context menu items.

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	
.. function:: PipelineItem_Export(type, data)

	Export information about the pipeline item to a project file

	:param const_char* type: the type name of the pipeline item
	:param void* data: the data allocated by this plugin and passed to the AddCustomPipelineItem() function
	:return: XML containing information about the pipeline item
	:rtype: const char*





Rest of the documentation is missing. Feel free to help. Here's source code for this documentation: `GitHub link <https://github.com/dfranx/shadered-docs>`_.