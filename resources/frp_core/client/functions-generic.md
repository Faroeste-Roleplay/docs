# Functions (Generic)

## Client API Documentation

## Overview

This documentation covers the Client API (cAPI) functions available in the FRP Framework. The API provides essential functionality for player interactions, world manipulation, UI elements, and game mechanics.

### Table of Contents

* Position & Movement
* Camera & Targeting
* Player State
* Visual Effects
* UI & Text Display
* Vehicle Management
* Notifications
* Inventory
* Progress Bars
* Location Services
* Audio
* Dimensions
* Teleportation

***

## Position & Movement

### `cAPI.GetPosition()`

Gets the current player position coordinates.

**Returns:**

* `x` (number): X coordinate
* `y` (number): Y coordinate
* `z` (number): Z coordinate

**Example:**

```lua
local x, y, z = cAPI.GetPosition()
print("Player is at:", x, y, z)
```

### `cAPI.GetSpeed`

Gets the current player movement speed (magnitude of velocity vector).

**Returns:**

* `speed` (number): Current speed in game units

**Example:**

```lua
local currentSpeed = cAPI.GetSpeed()
print("Player speed:", currentSpeed)
```

### `cAPI.GetCoords`

Gets coordinates and heading of any entity.

**Parameters:**

* `entity` (number): Entity handle

**Returns:**

* `table`: Contains x, y, z coordinates and heading (a)

**Example:**

```lua
local coords = cAPI.GetCoords(PlayerPedId())
print("Coords:", coords.x, coords.y, coords.z, "Heading:", coords.a)
```

***

## Camera & Targeting

### `cAPI.GetCoordsFromCam`

Calculates world coordinates from camera position at specified distance.

**Parameters:**

* `distance` (number): Distance from camera

**Returns:**

* `x` (number): X coordinate
* `y` (number): Y coordinate
* `z` (number): Z coordinate

**Example:**

```lua
local x, y, z = cAPI.GetCoordsFromCam(10.0)
print("Point 10 units from camera:", x, y, z)
```

### `cAPI.Target`

Performs raycast targeting from camera to detect entities.

**Parameters:**

* `Distance` (number): Maximum raycast distance
* `Ped` (number): Ped entity to ignore in raycast

**Returns:**

* `Entity` (number): Hit entity (0 if none)
* `x` (number): Hit point X coordinate
* `y` (number): Hit point Y coordinate
* `z` (number): Hit point Z coordinate

**Example:**

```lua
local entity, x, y, z = cAPI.Target(50.0, PlayerPedId())
if entity ~= 0 then
    print("Hit entity:", entity, "at", x, y, z)
end
```

***

## Player State

### `cAPI.ToggleInvinsible`

Toggles player invincibility state.

**Example:**

```lua
cAPI.ToggleInvinsible() -- Player becomes invincible
cAPI.ToggleInvinsible() -- Player becomes vulnerable again
```

### `cAPI.IsPlayingAnimation`

Checks if player is currently playing a specific animation.

**Parameters:**

* `dict` (string): Animation dictionary
* `anim` (string): Animation name

**Returns:**

* `boolean`: True if animation is playing

**Example:**

```lua
if cAPI.IsPlayingAnimation("amb_rest_drunk", "enter_left") then
    print("Player is playing drunk animation")
end
```

### `cAPI.ClientConnected`

Handles client connection state and loading screen.

**Parameters:**

* `bool` (boolean): Connection status

**Example:**

```lua
cAPI.ClientConnected(true) -- Shuts down loading screen
```

***

## Visual Effects

### `cAPI.ForceLightningFlashAtCoords`

Creates a lightning flash effect at specified coordinates.

**Parameters:**

* `x` (number): X coordinate
* `y` (number): Y coordinate
* `z` (number): Z coordinate

**Returns:**

* `boolean`: Success status

**Example:**

```lua
cAPI.ForceLightningFlashAtCoords(100.0, 200.0, 50.0)
```

### `cAPI.PlaySkyCameraAnimationAtCoords`

Plays a cinematic sky camera animation sequence ending with teleportation.

**Parameters:**

* `coords` (table): Array with \[x, y, z] coordinates

**Example:**

```lua
cAPI.PlaySkyCameraAnimationAtCoords({100.0, 200.0, 50.0})
```

***

## UI & Text Display

### `cAPI.DrawText`

Draws 2D text on screen with customizable properties.

**Parameters:**

* `x` (number): Screen X position (0.0 to 1.0)
* `y` (number): Screen Y position (0.0 to 1.0)
* `width` (number): Text width
* `height` (number): Text height
* `scale` (number): Text scale multiplier
* `r` (number): Red color component (0-255)
* `g` (number): Green color component (0-255)
* `b` (number): Blue color component (0-255)
* `a` (number): Alpha transparency (0-255)
* `text` (string): Text to display

**Example:**

```lua
cAPI.DrawText(0.5, 0.5, 0.0, 0.0, 0.5, 255, 255, 255, 255, "Hello World")
```

### `cAPI.DrawText3D`

Draws 3D text in world space with background.

**Parameters:**

* `x` (number): World X coordinate
* `y` (number): World Y coordinate
* `z` (number): World Z coordinate
* `text` (string): Text to display

**Example:**

```lua
local x, y, z = cAPI.GetPosition()
cAPI.DrawText3D(x, y, z + 2.0, "Player Location")
```

### `cAPI.DisplayHelpText`

Displays help text in the game's native help system.

**Parameters:**

* `helpText` (string): Help message to display

**Example:**

```lua
cAPI.DisplayHelpText("Press E to interact")
```

***

## Vehicle Management

### `cAPI.SpawnVehicle`

Spawns a vehicle with specified parameters.

**Parameters:**

* `model` (string/number): Vehicle model name or hash
* `cb` (function, optional): Callback function receiving vehicle handle
* `coords` (table, optional): Spawn coordinates {x, y, z, a}
* `isnetworked` (boolean, optional): Whether vehicle should be networked (default: true)

**Example:**

```lua
cAPI.SpawnVehicle("wagon02x", function(vehicle)
    print("Vehicle spawned:", vehicle)
    -- Set player into vehicle
    SetPedIntoVehicle(PlayerPedId(), vehicle, -1)
end, {x = 100.0, y = 200.0, z = 50.0, a = 90.0})
```

***

## Notifications

### `cAPI.NotifySimple`

Shows a simple notification message.

**Parameters:**

* `text` (string): Notification text
* `timeout` (number): Display duration in milliseconds

**Example:**

```lua
cAPI.NotifySimple("Welcome to the server!", 5000)
```

### `cAPI.Notify`

Shows a notification with type and quantity.

**Parameters:**

* `type` (string): Notification type
* `text` (string): Notification text
* `quantity` (number): Quantity to display

**Example:**

```lua
cAPI.Notify("success", "Item received", 1)
```

### `cAPI.NotifyToast`

Shows a toast-style notification.

**Parameters:**

* `type` (string): Toast type (defaults to "dev" if only text provided)
* `text` (string): Toast text
* `quantity` (number, optional): Quantity to display

**Example:**

```lua
cAPI.NotifyToast("info", "New quest available", 1)
-- Or simplified:
cAPI.NotifyToast("Simple toast message")
```

***

## Inventory

### `cAPI.HasItem`

Checks if player has specified item and amount.

**Parameters:**

* `item` (string): Item identifier
* `amount` (number, optional): Required amount (default: 1)

**Returns:**

* `boolean`: True if player has required amount

**Example:**

```lua
if cAPI.HasItem("apple", 5) then
    print("Player has at least 5 apples")
end
```

***

## Progress Bars

### `cAPI.Progressbar`

Creates a progress bar with customizable options.

**Parameters:**

* `name` (string): Progress bar identifier
* `label` (string): Display label
* `duration` (number): Duration in milliseconds
* `useWhileDead` (boolean): Allow while dead
* `canCancel` (boolean): Can be cancelled
* `disableControls` (table): Controls to disable
* `animation` (table): Animation settings
* `prop` (table): Primary prop settings
* `propTwo` (table): Secondary prop settings
* `onFinish` (function): Completion callback
* `onCancel` (function): Cancellation callback

**Example:**

```lua
cAPI.Progressbar("eating", "Eating food...", 5000, false, true, {}, {}, {}, {}, 
    function()
        print("Finished eating!")
    end,
    function()
        print("Eating cancelled")
    end
)
```

***

## Location Services

### `cAPI.GetCurrentStateName`

Gets the current state/region name.

**Returns:**

* `string`: State name ("Ambarino", "Lemoyne", "West Elizabeth", "New Austin", "New Hanover", "Guarma")

**Example:**

```lua
local state = cAPI.GetCurrentStateName()
print("Current state:", state)
```

### `cAPI.GetCurrentTownName`

Gets the current town/settlement name.

**Returns:**

* `string`: Town name or "Cidade Fantasma" if unknown

**Example:**

```lua
local town = cAPI.GetCurrentTownName()
print("Current town:", town)
```

***

## Audio

### `cAPI.PlayAmbientSpeech`

Plays ambient speech for a ped.

**Parameters:**

* `ped` (number): Ped entity handle
* `speech` (string): Speech identifier

**Example:**

```lua
cAPI.PlayAmbientSpeech(PlayerPedId(), "GREET_GENERAL")
```

***

## Dimensions

### `cAPI.enterDimension`

Enters a specific dimension, including any transport vehicle.

**Parameters:**

* `dimensionId` (number): Target dimension ID

**Example:**

```lua
cAPI.enterDimension(1) -- Enter dimension 1
```

### `cAPI.leaveDimension`

Leaves a specific dimension, including any transport vehicle.

**Parameters:**

* `dimensionId` (number): Dimension ID to leave

**Example:**

```lua
cAPI.leaveDimension(1) -- Leave dimension 1
```

***

## Utility Functions

### `cAPI.GetUserIdFromServerId`

Converts server ID to user ID.

**Parameters:**

* `serverid` (number): Server ID

**Returns:**

* `number`: User ID (0 if not found)

### `cAPI.GetServerIdFromUserId`

Converts user ID to server ID.

**Parameters:**

* `userid` (number): User ID

**Returns:**

* `number`: Server ID (0 if not found)

### `cAPI.GetFromCoordsFromPlayer`

Calculates coordinates at a specific radius from a player/ped position.

**Parameters:**

* `position` (vector3): Base position
* `ped` (number, optional): Ped entity (defaults to PlayerPedId())
* `radius` (number, optional): Distance radius (default: 0.8)

**Returns:**

* `vector3`: Calculated coordinates with ground Z adjustment

***

### Error Handling and Best Practices

#### Thread Safety

* Most functions are safe to call from any thread
* Camera and teleportation functions may require main thread execution

#### Performance Considerations

* Use `DrawText3D` sparingly in loops
* Cache entity handles when possible
* Avoid calling expensive functions like `GetCurrentTownName()` every frame

#### Common Patterns

```lua
-- Check if player has item before using
if cAPI.HasItem("bandage") then
    cAPI.Progressbar("healing", "Using bandage...", 3000, false, true, {}, {}, {}, {},
        function()
            -- Heal player
            cAPI.NotifySimple("Bandage used successfully!", 3000)
        end
    )
else
    cAPI.NotifySimple("You don't have any bandages!", 3000)
end

-- Safe teleportation with notification
local targetCoords = {x = 100.0, y = 200.0, z = 50.0}
cAPI.NotifySimple("Teleporting...", 2000)
handleTpNormally(targetCoords)
```

This framework provides a comprehensive set of tools for RedM server development, focusing on player interaction, world manipulation, and user interface elements.
