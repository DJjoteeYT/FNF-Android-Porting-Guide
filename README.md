# FNF-Android-Porting

The Things im using when i port a mod to android

### This should be used for the FNF 0.2.8 update and engines that have this version of FNF

## Instructions:

1. You Need to install extension-androidtools

To Install it You Need To Open Command prompt/PowerShell And To Tipe
```cmd
haxelib git extension-androidtools https://github.com/jigsaw-4277821/extension-androidtools.git
```

2. Download the repository code and paste it in your source code folder

3. You Need to add these things in project.xml

On This Line
```xml
	<!--Mobile-specific-->
	<window if="mobile" orientation="landscape" fullscreen="true" width="0" height="0" resizable="false"/>
```

Replace It With
```xml
	<!--Mobile-specific-->
	<window if="mobile" orientation="landscape" fullscreen="true" width="1280" height="720" resizable="false" allow-shaders="true" require-shaders="true"/>
```

Add

```xml
	<assets path="assets/android" if="android"/> <!-- to not have the android assets in another builds -saw -->
```

Then, After the Libraries, or where the packeges are located add

```xml
	<haxelib name="extension-androidtools" if="android"/>
```

Add
```xml
	<!-- make's the game use less ram -->
	<haxedef name="HXCPP_GC_BIG_BLOCKS"/>

	<!-- Always enable Null Object Reference check -->
	<haxedef name="HXCPP_CHECK_POINTER" if="release" />
	<haxedef name="HXCPP_STACK_LINE" if="release" />

	<!-- Android stuff: thanks Yoshi Engine -->
	<android permission="android.permission.ACCESS_NETWORK_STATE"/>
	<android permission="android.permission.INTERNET"/>
	<android permission="android.permission.VIBRATE"/>
	<android permission="android.permission.WRITE_EXTERNAL_STORAGE"/>
	<android permission="android.permission.READ_EXTERNAL_STORAGE"/>
```

4. Setup Controls.hx

after these lines
```haxe
import flixel.input.actions.FlxActionSet;
import flixel.input.keyboard.FlxKey;
```
add

```haxe
#if android
import android.flixel.FlxButton;
import android.flixel.FlxHitbox;
import android.flixel.FlxVirtualPad;
#end
```

before these lines
```haxe
	override function update()
	{
		super.update();
	}
```

add
```haxe
	#if android
	public var trackedinputsUI:Array<FlxActionInput> = [];
	public var trackedinputsNOTES:Array<FlxActionInput> = [];

	public function addbuttonNOTES(action:FlxActionDigital, button:FlxButton, state:FlxInputState)
	{
		var input:FlxActionInputDigitalIFlxInput = new FlxActionInputDigitalIFlxInput(button, state);
		trackedinputsNOTES.push(input);
		action.add(input);
	}

	public function addbuttonUI(action:FlxActionDigital, button:FlxButton, state:FlxInputState)
	{
		var input:FlxActionInputDigitalIFlxInput = new FlxActionInputDigitalIFlxInput(button, state);
		trackedinputsUI.push(input);
		action.add(input);
	}

	public function setHitBox(Hitbox:FlxHitbox) 
	{
		inline forEachBound(Control.NOTE_UP, (action, state) -> addbuttonNOTES(action, Hitbox.buttonUp, state));
		inline forEachBound(Control.NOTE_DOWN, (action, state) -> addbuttonNOTES(action, Hitbox.buttonDown, state));
		inline forEachBound(Control.NOTE_LEFT, (action, state) -> addbuttonNOTES(action, Hitbox.buttonLeft, state));
		inline forEachBound(Control.NOTE_RIGHT, (action, state) -> addbuttonNOTES(action, Hitbox.buttonRight, state));
	}

	public function setVirtualPadUI(VirtualPad:FlxVirtualPad, DPad:FlxDPadMode, Action:FlxActionMode)
	{
		switch (DPad)
		{
			case UP_DOWN:
				inline forEachBound(Control.UI_UP, (action, state) -> addbuttonUI(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.UI_DOWN, (action, state) -> addbuttonUI(action, VirtualPad.buttonDown, state));
			case LEFT_RIGHT:
				inline forEachBound(Control.UI_LEFT, (action, state) -> addbuttonUI(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.UI_RIGHT, (action, state) -> addbuttonUI(action, VirtualPad.buttonRight, state));
			case UP_LEFT_RIGHT:
				inline forEachBound(Control.UI_UP, (action, state) -> addbuttonUI(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.UI_LEFT, (action, state) -> addbuttonUI(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.UI_RIGHT, (action, state) -> addbuttonUI(action, VirtualPad.buttonRight, state));
			case LEFT_FULL | RIGHT_FULL:
				inline forEachBound(Control.UI_UP, (action, state) -> addbuttonUI(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.UI_DOWN, (action, state) -> addbuttonUI(action, VirtualPad.buttonDown, state));
				inline forEachBound(Control.UI_LEFT, (action, state) -> addbuttonUI(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.UI_RIGHT, (action, state) -> addbuttonUI(action, VirtualPad.buttonRight, state));
			case BOTH_FULL:
				inline forEachBound(Control.UI_UP, (action, state) -> addbuttonUI(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.UI_DOWN, (action, state) -> addbuttonUI(action, VirtualPad.buttonDown, state));
				inline forEachBound(Control.UI_LEFT, (action, state) -> addbuttonUI(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.UI_RIGHT, (action, state) -> addbuttonUI(action, VirtualPad.buttonRight, state));
				inline forEachBound(Control.UI_UP, (action, state) -> addbuttonUI(action, VirtualPad.buttonUp2, state));
				inline forEachBound(Control.UI_DOWN, (action, state) -> addbuttonUI(action, VirtualPad.buttonDown2, state));
				inline forEachBound(Control.UI_LEFT, (action, state) -> addbuttonUI(action, VirtualPad.buttonLeft2, state));
				inline forEachBound(Control.UI_RIGHT, (action, state) -> addbuttonUI(action, VirtualPad.buttonRight2, state));
			case NONE: // do nothing
		}

		switch (Action)
		{
			case A:
				inline forEachBound(Control.ACCEPT, (action, state) -> addbuttonUI(action, VirtualPad.buttonA, state));
			case B:
				inline forEachBound(Control.BACK, (action, state) -> addbuttonUI(action, VirtualPad.buttonB, state));
			case A_B | A_B_C | A_B_E | A_B_X_Y | A_B_C_X_Y | A_B_C_X_Y_Z | A_B_C_D_V_X_Y_Z | A_B_C_D_V_X_Y_Z_UP_DOWN:
				inline forEachBound(Control.ACCEPT, (action, state) -> addbuttonUI(action, VirtualPad.buttonA, state));
				inline forEachBound(Control.BACK, (action, state) -> addbuttonUI(action, VirtualPad.buttonB, state));
			case NONE: // do nothing
		}
	}

	public function setVirtualPadNOTES(VirtualPad:FlxVirtualPad, DPad:FlxDPadMode, Action:FlxActionMode) 
	{
		switch (DPad)
		{
			case UP_DOWN:
				inline forEachBound(Control.NOTE_UP, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.NOTE_DOWN, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonDown, state));
			case LEFT_RIGHT:
				inline forEachBound(Control.NOTE_LEFT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.NOTE_RIGHT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonRight, state));
			case UP_LEFT_RIGHT:
				inline forEachBound(Control.NOTE_UP, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.NOTE_LEFT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.NOTE_RIGHT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonRight, state));
			case LEFT_FULL | RIGHT_FULL:
				inline forEachBound(Control.NOTE_UP, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.NOTE_DOWN, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonDown, state));
				inline forEachBound(Control.NOTE_LEFT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.NOTE_RIGHT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonRight, state));
			case BOTH_FULL:
				inline forEachBound(Control.NOTE_UP, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonUp, state));
				inline forEachBound(Control.NOTE_DOWN, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonDown, state));
				inline forEachBound(Control.NOTE_LEFT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonLeft, state));
				inline forEachBound(Control.NOTE_RIGHT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonRight, state));
				inline forEachBound(Control.NOTE_UP, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonUp2, state));
				inline forEachBound(Control.NOTE_DOWN, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonDown2, state));
				inline forEachBound(Control.NOTE_LEFT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonLeft2, state));
				inline forEachBound(Control.NOTE_RIGHT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonRight2, state));
			case NONE: // do nothing
		}

		switch (Action)
		{
			case A:
				inline forEachBound(Control.ACCEPT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonA, state));
			case B:
				inline forEachBound(Control.BACK, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonB, state));
			case A_B | A_B_C | A_B_E | A_B_X_Y | A_B_C_X_Y | A_B_C_X_Y_Z | A_B_C_D_V_X_Y_Z | A_B_C_D_V_X_Y_Z_UP_DOWN:
				inline forEachBound(Control.ACCEPT, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonA, state));
				inline forEachBound(Control.BACK, (action, state) -> addbuttonNOTES(action, VirtualPad.buttonB, state));
			case NONE: // do nothing
		}
	}

	public function removeFlxInput(Tinputs:Array<FlxActionInput>)
	{
		for (action in this.digitalActions)
		{
			var i = action.inputs.length;
			while (i-- > 0)
			{
				var x = Tinputs.length;
				while (x-- > 0)
				{
					if (Tinputs[x] == action.inputs[i])
						action.remove(action.inputs[i]);
				}
			}
		}
	}
	#end
```

and replace these lines (you can skip this, it's for psych engine)
```haxe
	public function bindKeys(control:Control, keys:Array<FlxKey>)
	{
		var copyKeys:Array<FlxKey> = keys.copy();
		for (i in 0...copyKeys.length) {
			if(i == NONE) copyKeys.remove(i);
		}

		#if (haxe >= "4.0.0")
		inline forEachBound(control, (action, state) -> addKeys(action, copyKeys, state));
		#else
		forEachBound(control, function(action, state) addKeys(action, copyKeys, state));
		#end
	}

	public function unbindKeys(control:Control, keys:Array<FlxKey>)
	{
		var copyKeys:Array<FlxKey> = keys.copy();
		for (i in 0...copyKeys.length) {
			if(i == NONE) copyKeys.remove(i);
		}

		#if (haxe >= "4.0.0")
		inline forEachBound(control, (action, _) -> removeKeys(action, copyKeys));
		#else
		forEachBound(control, function(action, _) removeKeys(action, copyKeys));
		#end
	}

```

with
```haxe
	#if !android
	public function bindKeys(control:Control, keys:Array<FlxKey>)
	{
		var copyKeys:Array<FlxKey> = keys.copy();
		for (i in 0...copyKeys.length) {
			if(i == NONE) copyKeys.remove(i);
		}

		#if (haxe >= "4.0.0")
		inline forEachBound(control, (action, state) -> addKeys(action, copyKeys, state));
		#else
		forEachBound(control, function(action, state) addKeys(action, copyKeys, state));
		#end
	}

	public function unbindKeys(control:Control, keys:Array<FlxKey>)
	{
		var copyKeys:Array<FlxKey> = keys.copy();
		for (i in 0...copyKeys.length) {
			if(i == NONE) copyKeys.remove(i);
		}

		#if (haxe >= "4.0.0")
		inline forEachBound(control, (action, _) -> removeKeys(action, copyKeys));
		#else
		forEachBound(control, function(action, _) removeKeys(action, copyKeys));
		#end
	}
	
	#else

	public function bindKeys(control:Control, keys:Array<FlxKey>)
	{
		#if (haxe >= "4.0.0")
		inline forEachBound(control, (action, state) -> addKeys(action, keys, state));
		#else
		forEachBound(control, function(action, state) addKeys(action, keys, state));
		#end
	}

	public function unbindKeys(control:Control, keys:Array<FlxKey>)
	{
		#if (haxe >= "4.0.0")
		inline forEachBound(control, (action, _) -> removeKeys(action, keys));
		#else
		forEachBound(control, function(action, _) removeKeys(action, keys));
		#end
	}	
	#end
```

5. Setup MusicBeatState.hx

in the lines you import things add
```haxe
#if android
import android.AndroidControls;
import android.flixel.FlxVirtualPad;
import flixel.input.actions.FlxActionInput;
import flixel.util.FlxDestroyUtil;
#end
```

after these lines
```haxe
	inline function get_controls():Controls
		return PlayerSettings.player1.controls;
```

add
```haxe
	#if android
	var virtualPad:FlxVirtualPad;
	var androidControls:AndroidControls;
	var trackedinputsUI:Array<FlxActionInput> = [];
	var trackedinputsNOTES:Array<FlxActionInput> = [];

	public function addVirtualPad(DPad:FlxDPadMode, Action:FlxActionMode)
	{
		virtualPad = new FlxVirtualPad(DPad, Action);
		add(virtualPad);

		controls.setVirtualPadUI(virtualPad, DPad, Action);
		trackedinputsUI = controls.trackedinputsUI;
		controls.trackedinputsUI = [];
	}

	public function removeVirtualPad()
	{
		if (trackedinputsUI != [])
			controls.removeFlxInput(trackedinputsUI);

		if (virtualPad != null)
			virtualPad.destroy();
	}

	public function addAndroidControls()
	{
		androidControls = new AndroidControls();

		switch (AndroidControls.getMode())
		{
			case 0 | 1 | 2: // RIGHT_FULL | LEFT_FULL | CUSTOM
				controls.setVirtualPadNOTES(androidControls.virtualPad, RIGHT_FULL, NONE);
			case 3: // BOTH_FULL
				controls.setVirtualPadNOTES(androidControls.virtualPad, BOTH_FULL, NONE);
			case 4: // HITBOX
				controls.setHitBox(androidControls.hitbox);
			case 5: // KEYBOARD
		}

		trackedinputsNOTES = controls.trackedinputsNOTES;
		controls.trackedinputsNOTES = [];

		var camControls = new flixel.FlxCamera();
		FlxG.cameras.add(camControls);
		camControls.bgColor.alpha = 0;

		androidControls.cameras = [camControls];
		androidControls.visible = false;
		add(androidControls);
	}

	public function removeAndroidControls()
	{
		if (trackedinputsNOTES != [])
			controls.removeFlxInput(trackedinputsNOTES);

		if (androidControls != null)
			androidControls.destroy();
	}

	public function addPadCamera()
	{
		if (virtualPad != null)
		{
			var camControls = new flixel.FlxCamera();
			FlxG.cameras.add(camControls);
			camControls.bgColor.alpha = 0;
			virtualPad.cameras = [camControls];
		}
	}
	#end

	override function destroy()
	{
		#if android
		if (trackedinputsNOTES != [])
			controls.removeFlxInput(trackedinputsNOTES);

		if (trackedinputsUI != [])
			controls.removeFlxInput(trackedinputsUI);
		#end

		super.destroy();

		#if android
		if (virtualPad != null)
		{
			virtualPad = FlxDestroyUtil.destroy(virtualPad);
			virtualPad = null;
		}

		if (androidControls != null)
		{
			androidControls = FlxDestroyUtil.destroy(androidControls);
			androidControls = null;
		}
		#end
	}
```

6. Setup MusicBeatSubstate.hx

in the lines you import things add
```haxe
#if android
import android.flixel.FlxVirtualPad;
import flixel.input.actions.FlxActionInput;
import flixel.util.FlxDestroyUtil;
#end
```

after these lines
```haxe
	inline function get_controls():Controls
		return PlayerSettings.player1.controls;
```

add
```haxe
	#if android
	var virtualPad:FlxVirtualPad;
	var trackedinputsUI:Array<FlxActionInput> = [];

	public function addVirtualPad(DPad:FlxDPadMode, Action:FlxActionMode)
	{
		virtualPad = new FlxVirtualPad(DPad, Action);
		add(virtualPad);

		controls.setVirtualPadUI(virtualPad, DPad, Action);
		trackedinputsUI = controls.trackedinputsUI;
		controls.trackedinputsUI = [];
	}

	public function removeVirtualPad()
	{
		if (trackedinputsUI != [])
			controls.removeFlxInput(trackedinputsUI);

		if (virtualPad != null)
			virtualPad.destroy();
	}

	public function addPadCamera()
	{
		if (virtualPad != null)
		{
			var camControls = new flixel.FlxCamera();
			FlxG.cameras.add(camControls);
			camControls.bgColor.alpha = 0;
			virtualPad.cameras = [camControls];
		}
	}
	#end

	override function destroy()
	{
		#if android
		if (trackedinputsUI != [])
			controls.removeFlxInput(trackedinputsUI);
		#end

		super.destroy();

		#if android
		if (virtualPad != null)
		{
			virtualPad = FlxDestroyUtil.destroy(virtualPad);
			virtualPad = null;
		}
		#end
	}
```

And somehow you finished adding the android controls to your psych engine copy

now on every state/substate add
```haxe
	#if android
	addVirtualPad(LEFT_FULL, A_B);
	#end

	//if you want it to have a camera
	#if android
	addPadCamera();
	#end

	//in states, these need to be added before super.create();
	//in substates, in fuction new at the last line add these

	//on Playstate.hx after all of the
	//obj.cameras = [...];
	//things, add
	#if android
	addAndroidControls();
	#end

	//to make the controls visible the code is
	#if android
	androidControls.visible = true;
	#end

	//to make the controls invisible the code is
	#if android
	androidControls.visible = false;
	#end
```

7. Prevent the Android BACK Button

in TitleState.hx

after
```haxe
override public function create():Void
```

add
```haxe
#if android
FlxG.android.preventDefaultKeys = [BACK];
#end
```

8. Set An action to the BACK Button

you can set one with
```haxe
#if android || FlxG.android.justReleased.BACK #end
```

9. On sys.FileSystem and sys.io.File

this is not working with app storage but on phone storage it will work with this

```haxe
SUtil.getPath() + 
```
this will make the game use the phone storage
but you will have to add one thing in Your source

in Main.hx before 
```haxe
addChild(new FlxGame(gameWidth, gameHeight, initialState, zoom, framerate, framerate, skipSplash, startFullscreen));
```

add 
```haxe
SUtil.check();
```
this will check for android storage permisions and the assets/mods directories

10. On Crash Application Alert

on Main.hx after
```haxe
public function new()
```	
add
```haxe
SUtil.uncaughtErrorHandler();
```

11. File Saver

This is a feature to save files with sys.io.File
This is the code
```haxe
SUtil.saveContent("your file name", ".txt", "lololol");

//The file will be where the assets and mods are located in phone storage in system-saves folder
```

13. Do an action when you press on the screen

```haxe
		#if android
		var justTouched:Bool = false;

		for (touch in FlxG.touches.list)
			if (touch.justPressed)
				justTouched = true;
		#end

		if (controls.ACCEPT #if android || justTouched #end)
		{
			//Do something
		}
```

## Credits:
* Saw (M.A. JIGSAW) me - Doing the rest of the code, utils, pad buttons and other things
* luckydog7 - Original code for android controls
* Goldie - Pad designer
