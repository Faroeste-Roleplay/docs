# Ped Camera System

The Ped Camera System provides a specialized camera designed for close-up character viewing, perfect for character customization, showcasing outfits, or detailed character inspection. This system offers more restrictive movement than the orbital camera, optimized for pedestrian/character focus.

## Basic Usage

To enable the Ped Camera inside of your resource file just add `usePedCam() useControllablePedCam()` inside your code like example below.

```lua
usePedCam()
useControllablePedCam()
```

## Features

* **Character-focused camera** with restricted movement
* **Close-range operation** with tight distance controls
* **Limited vertical movement** to maintain character focus
* **Z-position adjustment** for fine-tuned camera height
* **Smooth interpolation** for natural camera movement
* **Collision detection** optimized for character viewing

## Core Functions

## Initialization

### `EnablePedCam`

Enables the ped camera system and starts the main camera loop.

**Returns:** `number` - Handle for the camera instance

**Example:**

```lua
local cameraHandle = EnablePedCam()
```

### `DisablePedCam`

Disables the ped camera system and returns to gameplay camera.

**Parameters:**

* `immediately` (boolean, optional) - If true, disables immediately without interpolation

**Example:**

```lua
DisablePedCam(false) -- Smooth transition
DisablePedCam(true)  -- Immediate disable
```

## Position Control

### `SetPedCamLookAtPosition`

Sets the target position that the camera will focus on.

**Parameters:**

* `position` (vector3) - World coordinates to focus on

**Example:**

```lua
local pedPosition = GetEntityCoords(playerPed)
SetPedCamLookAtPosition(pedPosition)
```

### `GetPedCamLookAtPosition`

Gets the current look-at position.

**Returns:** `vector3` - Current target position

## Distance Control

### `SetPedCamDistanceToLookAtPos`

Sets the distance from the camera to the target position.

**Parameters:**

* `distance` (number) - Distance in game units

**Example:**

```lua
SetPedCamDistanceToLookAtPos(2.5) -- Close-up view
```

### `GetPedCamDistanceToLookAtPos`

Gets the current distance to the target position.

**Returns:** `number` - Current distance

### `SetPedCamMinDistanceToLookAtPos`

Sets the minimum allowed distance for the ped camera.

**Parameters:**

* `minDistance` (number) - Minimum distance in game units

### `SetPedCamMaxDistanceToLookAtPos`

Sets the maximum allowed distance for the ped camera.

**Parameters:**

* `maxDistance` (number) - Maximum distance in game units

## Angle Control

### `SetPedCamAngleX`

Sets the horizontal rotation angle around the target.

**Parameters:**

* `angle` (number) - Angle in degrees (0-360)
* `disableInterpolation` (boolean, optional) - Skip smooth interpolation

**Example:**

```lua
SetPedCamAngleX(90.0) -- Side view of character
```

### `AddPedCamAngleX`

Adds to the current horizontal angle.

**Parameters:**

* `addAngle` (number) - Degrees to add to current angle
* `disableInterpolation` (boolean, optional) - Skip smooth interpolation

### `SetPedCamAngleY`

Sets the vertical rotation angle (pitch). **Note: This is restricted to prevent extreme angles.**

**Parameters:**

* `angle` (number) - Angle in degrees (limited to -5° to 5°)
* `disableInterpolation` (boolean, optional) - Skip smooth interpolation

**Restrictions:**

* Minimum angle: -5° (looking slightly down)
* Maximum angle: 5° (looking slightly up)
* **Important:** Currently always sets to 0° regardless of input

### `AddPedCamAngleY`

Adds to the current vertical angle.

**Parameters:**

* `addAngle` (number) - Degrees to add to current angle
* `disableInterpolation` (boolean, optional) - Skip smooth interpolation

## Z-Position Control

### `SetPedCamPositionZ`

Sets the Z-offset for the camera's look-at position, allowing fine height adjustment.

**Parameters:**

* `posZ` (number) - Z-offset in game units (limited to -0.80 to 0.70)
* `disableInterpolation` (boolean, optional) - Skip smooth interpolation

**Restrictions:**

* Minimum Z-offset: -0.80 (lower view)
* Maximum Z-offset: 0.70 (higher view)

**Example:**

```lua
SetPedCamPositionZ(0.5) -- Raise camera focus point
```

### `AddPedCamPositionZ`

Adds to the current Z-position offset.

**Parameters:**

* `addOffset` (number) - Offset to add to current Z-position
* `disableInterpolation` (boolean, optional) - Skip smooth interpolation

### `GetPedCamPositionZ`

Gets the current Z-position offset.

**Returns:** `number` - Current Z-offset (defaults to 0 if not set)

## Status Functions

### `IsPedCamCreated`

Checks if the ped camera is currently active.

**Returns:** `boolean` - True if camera exists

### `GetPedCamId`

Gets the internal camera ID.

**Returns:** `number` - Camera ID or nil if not created

## Complete Usage Example

Here's a comprehensive example showing how to set up a ped camera for character viewing:

```lua
local gPedCamPreviewIsEnabled = false

function Game.StartPedCam(entityId)
    if entityId then
        -- Get camera position offset from the entity
        local cameraPosition = GetOffsetFromEntityInWorldCoords(
            entityId, 
            0.13,  -- X offset (slightly to the side)
            0.0,   -- Y offset (no front/back offset)
            0.05   -- Z offset (slightly up)
        )
        
        -- Configure camera settings
        SetPedCamLookAtPosition(cameraPosition)
        SetPedCamDistanceToLookAtPos(3.0)        -- Medium distance
        SetPedCamMinDistanceToLookAtPos(0.4)     -- Very close minimum
        SetPedCamMaxDistanceToLookAtPos(3.0)     -- Moderate maximum
        SetPedCamAngleY(0)                       -- Neutral vertical angle
        
        -- Enable the controllable ped camera
        EnableControllablePedCam()
        gPedCamPreviewIsEnabled = true
    end
end

function Game.StopPedCam()
    DisableControllablePedCam(true)
    ClearFocus()
    gPedCamPreviewIsEnabled = false
end
```

## Key Differences from Orbital Camera

## Movement Restrictions

The Ped Camera has several built-in restrictions designed for character viewing:

1. **Vertical Angle Limits:** Y-angle is restricted to -5° to 5° (and currently forced to 0°)
2. **Z-Position Limits:** Z-offset is restricted to -0.80 to 0.70 game units
3. **Closer Operation:** Designed for much closer distances than orbital camera

## Optimized for Characters

* Default distance ranges are much smaller (0.4 to 3.0 units vs 3.0 to 7.0)
* Z-position adjustment allows fine-tuning of character focus height
* More restrictive movement prevents awkward camera angles

## Advanced Configuration

## Character Customization Setup

```lua
function setupCharacterCustomizationCamera(playerPed)
    local pedCoords = GetEntityCoords(playerPed)
    
    -- Focus on character's torso area
    SetPedCamLookAtPosition(pedCoords)
    SetPedCamDistanceToLookAtPos(1.5)  -- Close for detail
    SetPedCamMinDistanceToLookAtPos(0.8)
    SetPedCamMaxDistanceToLookAtPos(2.5)
    
    -- Adjust height to focus on torso/face
    SetPedCamPositionZ(0.3)  -- Raise focus point
    
    EnablePedCam()
end
```

## Dynamic Height Adjustment

```lua
function adjustCameraForClothing(clothingType)
    if clothingType == "hat" or clothingType == "hair" then
        SetPedCamPositionZ(0.6)  -- Focus on head
    elseif clothingType == "shirt" or clothingType == "jacket" then
        SetPedCamPositionZ(0.2)  -- Focus on chest
    elseif clothingType == "pants" or clothingType == "shoes" then
        SetPedCamPositionZ(-0.3) -- Focus on lower body
    end
end
```

## Best Practices

1. **Use appropriate distance ranges** - Keep distances small for character detail
2. **Utilize Z-position adjustment** - Fine-tune the height focus for different body parts
3. **Consider entity offsets** - Use `GetOffsetFromEntityInWorldCoords()` for precise positioning
4. **Handle character movement** - Update look-at position if character moves
5. **Test angle restrictions** - Remember that Y-angles are heavily restricted

## Performance Notes

* Optimized collision detection for close-range operation
* Same interpolation smoothing as orbital camera (factor 5.0)
* Efficient state management with minimal memory footprint
* Runs at 60 FPS for smooth character viewing

## Troubleshooting

**Camera too close/far:**

* Adjust min/max distance settings
* Check if entity position is correct

**Cannot look up/down:**

* This is by design - Y-angle is restricted to maintain character focus
* Use Z-position adjustment instead for height changes

**Jerky movement:**

* Ensure stable frame rate
* Check for conflicting camera systems or input handlers
