# Orbital Camera System

The Orbital Camera System provides a smooth, controllable camera that orbits around a specific point in 3D space. This system is ideal for property previews, showcasing locations, or any scenario where you need a cinematic orbital view.

## Basic Usage

To enable the Orbital Camera inside of your resource file just add `useOrbitalCam() useControllableOrbitalCam()` inside your code like example below.

```lua
useOrbitalCam()
useControllableOrbitalCam()
```

## Features

* **Smooth orbital movement** around a target position
* **Distance control** with minimum and maximum limits
* **Collision detection** to prevent camera clipping through objects
* **Interpolated positioning** for smooth transitions
* **Controllable input** support for user interaction

## Core Functions

### Initialization

#### `EnableOrbitalCam`

Enables the orbital camera system and starts the main camera loop.

**Returns:** `number` - Handle for the camera instance

**Example:**

```lua
local cameraHandle = EnableOrbitalCam()
```

#### `DisableOrbitalCam`

Disables the orbital camera system and returns to gameplay camera.

**Parameters:**

* `immediately` (boolean, optional) - If true, disables immediately without interpolation

**Example:**

```lua
DisableOrbitalCam(false) -- Smooth transition
DisableOrbitalCam(true)  -- Immediate disable
```

### Position Control

#### `SetOrbitalCamLookAtPosition`

Sets the target position that the camera will orbit around.

**Parameters:**

* `position` (vector3) - World coordinates to focus on

**Example:**

```lua
local targetPos = vector3(100.0, 200.0, 30.0)
SetOrbitalCamLookAtPosition(targetPos)
```

#### `GetOrbitalCamLookAtPosition`

Gets the current look-at position.

**Returns:** `vector3` - Current target position

### Distance Control

#### `SetOrbitalCamDistanceToLookAtPos`

Sets the distance from the camera to the target position.

**Parameters:**

* `distance` (number) - Distance in game units

**Example:**

```lua
SetOrbitalCamDistanceToLookAtPos(15.0) -- 15 units away
```

#### `GetOrbitalCamDistanceToLookAtPos`

Gets the current distance to the target position.

**Returns:** `number` - Current distance

#### `SetOrbitalCamMinDistanceToLookAtPos`

Sets the minimum allowed distance for the orbital camera.

**Parameters:**

* `minDistance` (number) - Minimum distance in game units

#### `SetOrbitalCamMaxDistanceToLookAtPos`

Sets the maximum allowed distance for the orbital camera.

**Parameters:**

* `maxDistance` (number) - Maximum distance in game units

### Angle Control

#### `SetOrbitalCamAngleX`

Sets the horizontal rotation angle around the target.

**Parameters:**

* `angle` (number) - Angle in degrees (0-360)
* `disableInterpolation` (boolean, optional) - Skip smooth interpolation

**Example:**

```lua
SetOrbitalCamAngleX(45.0) -- Face northeast
```

#### `AddOrbitalCamAngleX`

Adds to the current horizontal angle.

**Parameters:**

* `addAngle` (number) - Degrees to add to current angle
* `disableInterpolation` (boolean, optional) - Skip smooth interpolation

#### `SetOrbitalCamAngleY`

Sets the vertical rotation angle (pitch).

**Parameters:**

* `angle` (number) - Angle in degrees (0-360)
* `disableInterpolation` (boolean, optional) - Skip smooth interpolation

#### `AddOrbitalCamAngleY`

Adds to the current vertical angle.

**Parameters:**

* `addAngle` (number) - Degrees to add to current angle
* `disableInterpolation` (boolean, optional) - Skip smooth interpolation

### Status Functions

#### `IsOrbitalCamCreated`

Checks if the orbital camera is currently active.

**Returns:** `boolean` - True if camera exists

#### `GetOrbitalCamId`

Gets the internal camera ID.

**Returns:** `number` - Camera ID or nil if not created

## Complete Usage Example

Here's a comprehensive example showing how to set up an orbital camera for a property preview:

```lua
local gOrbitalCamPreviewIsEnabled = false

function startPropertyPreviewUsingOrbitalCam(propertyId)
    if propertyId then
        -- Get property position
        local propertyPosition = GetPropertyFromId(tonumber(propertyId)).camPosition
        
        -- Fade out screen for smooth transition
        DoScreenFadeOut(500)
        
        -- Set focus and camera parameters
        SetFocusPosAndVel(propertyPosition, 0.0, 0.0, 0.0)
        SetOrbitalCamLookAtPosition(propertyPosition)
        SetOrbitalCamDistanceToLookAtPos(30.0)
        SetOrbitalCamMaxDistanceToLookAtPos(80.0)
        
        -- Enable the controllable orbital camera
        EnableControllableOrbitalCam()
        
        -- Wait for collision to load
        while not Citizen.InvokeNative(0xDA8B2EAF29E872E2, propertyPosition) do
            Wait(100)
        end
        
        -- Fade back in
        DoScreenFadeIn(500)
        gOrbitalCamPreviewIsEnabled = true
        
        -- Enable controls for user interaction
        triggerControlsPreview()
    end
end

function stopPropertyPreviewUsingOrbitalCam()
    DoScreenFadeIn(500)
    DisableControllableOrbitalCam(true)
    ClearFocus()
    gOrbitalCamPreviewIsEnabled = false
end
```

## Advanced Features

### Collision Detection

The orbital camera automatically handles collision detection to prevent clipping through walls and objects. It uses two collision checks:

1. **Depth collision** - Prevents camera from going through objects between the camera and target
2. **Wall collision** - Prevents camera from positioning behind solid objects

### Smooth Interpolation

The camera uses frame-rate independent interpolation with a smoothing factor of 5.0 for natural movement transitions.

### Performance Considerations

* The camera runs at 60 FPS (Wait(0) in main loop)
* Collision detection is optimized to ignore pedestrians when appropriate
* Memory usage is minimal with efficient state management

## Best Practices

1. **Always set a look-at position** before enabling the camera
2. **Use appropriate distance limits** for your use case
3. **Fade screen transitions** for professional presentation
4. **Clear focus** when disabling to return to normal gameplay
5. **Handle promises properly** when disabling to prevent conflicts

## Troubleshooting

**Camera not appearing:**

* Ensure you've set a look-at position before enabling
* Check that the target position has loaded collision

**Jerky movement:**

* Verify frame rate is stable
* Check for conflicting camera systems

**Camera stuck:**

* Try disabling immediately: `DisableOrbitalCam(true)`
* Ensure proper cleanup in your scripts
