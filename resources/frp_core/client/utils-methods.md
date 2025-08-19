# Utils Methods

## Utils Methods

## Overview

This documentation covers the Client API (cAPI) functions available in the FRP Framework. The API provides essential functionality for player interactions, world manipulation, UI elements, and game mechanics.

## Table of Contents

* Position & Movement
* Camera & Targeting
* Player State
* Visual Effects

***

## Position & Movement

### `cAPI.Prompt`

Shows an input prompt dialog and waits for user input.

**Parameters:**

* `title` (string): Prompt dialog title
* `default_text` (string): Default input text

**Returns:**

* `string`: User input text (or nil if cancelled)

**Features:**

* Sets NUI focus for text input
* Waits for user completion
* Returns actual user input
* Handles cancellation gracefully

**Example:**

```lua
local playerName = cAPI.Prompt("Enter your name:", "John Doe")
if playerName then
    print("Player entered name:", playerName)
else
    print("Player cancelled the prompt")
end
```

***

## Resource Management

### `cAPI.RequestModel`

Safely requests and loads a model with validation.

**Parameters:**

* `hash` (number/string): Model hash or name

**Features:**

* Validates model before requesting
* Waits for complete loading
* Handles invalid models gracefully
* Automatic hash conversion

**Example:**

```lua
cAPI.RequestModel(GetHashKey("mp_male"))
-- Model is now loaded and ready to use

local vehicle = CreateVehicle(GetHashKey("wagon02x"), x, y, z, heading, true, false)
```

### `cAPI.RequestAnimDict`

Safely requests and loads an animation dictionary.

**Parameters:**

* `dictionary` (string): Animation dictionary name

**Features:**

* Checks if already loaded
* Waits for complete loading
* Prevents duplicate requests
* Thread-safe loading

**Example:**

```lua
cAPI.RequestAnimDict("amb_rest_drunk")
-- Animation dictionary is now loaded
TaskPlayAnim(PlayerPedId(), "amb_rest_drunk", "enter_left", 8.0, -8.0, -1, 0, 0, false, 0, false)
```

***

## Screen Effects

### `cAPI.StartFade`

Initiates a screen fade-out with optional storm effect.

**Parameters:**

* `timer` (number): Fade duration in milliseconds
* `effect` (boolean, optional): Enable storm visual effect

**Features:**

* Smooth screen fade transition
* Optional atmospheric storm effect
* Waits for fade completion
* Thread-safe execution

**Example:**

```lua
cAPI.StartFade(1000, true) -- Fade out over 1 second with storm effect
-- Screen is now faded out
```

### `cAPI.EndFade`

Initiates a screen fade-in and cleans up effects.

**Parameters:**

* `timer` (number): Fade duration in milliseconds
* `effect` (boolean, optional): Clean up storm effect

**Features:**

* Shuts down loading screen
* Smooth screen fade-in
* Automatic effect cleanup
* Waits for fade completion

**Example:**

```lua
cAPI.EndFade(1000, true) -- Fade in over 1 second and stop storm effect
-- Screen is now fully visible
```

### Complete Fade Sequence Example

```lua
-- Perform action with fade transition
cAPI.StartFade(500, true)   -- Fade out with storm effect
-- ... perform teleport, model change, etc ...
cAPI.EndFade(500, true)     -- Fade back in and clean up
```

***

## Animation Helpers

### `cAPI.PlayAnim`

Plays an animation with automatic dictionary loading.

**Parameters:**

* `dict` (string): Animation dictionary name
* `anim` (string): Animation name within dictionary
* `speed` (number): Animation playback speed

**Features:**

* Checks if animation is already playing
* Automatically loads animation dictionary
* Waits for dictionary loading
* Starts animation with proper parameters

**Example:**

```lua
-- Play drinking animation
cAPI.PlayAnim("amb_rest_drunk", "enter_left", 8.0)

-- Play eating animation
cAPI.PlayAnim("mech_inventory@eating@multi_bite@sphere_d8-4_fruit", "quick_right_hand_throw", 4.0)
```

***

## Advanced Usage Patterns

### Request/Prompt Flow Control

```lua
-- Complex user interaction flow
local wantsToEnter = cAPI.Request("Enter the dangerous area?", 15000)
if wantsToEnter then
    local password = cAPI.Prompt("Enter password:", "")
    if password == "secret123" then
        cAPI.NotifySimple("Access granted!", 3000)
        -- ... proceed with entry logic
    else
        cAPI.NotifySimple("Incorrect password!", 3000)
    end
else
    cAPI.NotifySimple("Entry cancelled.", 3000)
end
```

### Resource Loading with Error Handling

```lua
-- Safe model spawning
local function spawnModelSafely(modelName, coords)
    cAPI.RequestModel(GetHashKey(modelName))
    
    if HasModelLoaded(GetHashKey(modelName)) then
        local entity = CreatePed(4, GetHashKey(modelName), coords.x, coords.y, coords.z, 0.0, true, true)
        SetModelAsNoLongerNeeded(GetHashKey(modelName))
        return entity
    else
        print("Failed to load model:", modelName)
        return nil
    end
end
```

### Smooth Scene Transitions

```lua
-- Cinematic scene transition
local function cinematicTransition(newLocation, callback)
    cAPI.StartFade(1000, true)          -- Fade out with storm
    
    cAPI.TeleportPlayer(newLocation)     -- Move player
    
    if callback then
        callback()                       -- Perform additional setup
    end
    
    Wait(500)                           -- Brief pause
    cAPI.EndFade(1000, true)            -- Fade back in
end
```

### Animation Sequences

```lua
-- Chained animation sequence
local function performActionSequence()
    -- Play preparation animation
    cAPI.PlayAnim("script_common@other@iteminteraction@drinking@player@idle_c", "idle_a", 2.0)
    Wait(2000)
    
    -- Play main action
    cAPI.PlayAnim("mech_inventory@eating@multi_bite@sphere_d8-4_fruit", "quick_right_hand_throw", 4.0)
    Wait(3000)
    
    -- Return to normal state
    ClearPedTasks(PlayerPedId())
end
```

***

## Integration with Other Systems

### UI + Teleportation

```lua
-- Interactive teleportation system
local function interactiveTeleport(locations)
    local locationNames = {}
    for name, _ in pairs(locations) do
        table.insert(locationNames, name)
    end
    
    local choice = cAPI.Prompt("Choose destination:", table.concat(locationNames, ", "))
    
    if choice and locations[choice] then
        local confirm = cAPI.Request("Teleport to " .. choice .. "?", 10000)
        if confirm then
            cAPI.StartFade(500)
            cAPI.TeleportPlayer(locations[choice])
            cAPI.EndFade(500)
            cAPI.NotifySimple("Teleported to " .. choice, 3000)
        end
    end
end
```

### Resource Management Best Practices

```lua
-- Efficient resource management
local function loadRequiredResources()
    local modelsToLoad = {"mp_male", "mp_female", "wagon02x"}
    local animsToLoad = {"amb_rest_drunk", "mech_inventory@eating@multi_bite@sphere_d8-4_fruit"}
    
    -- Load all models
    for _, model in ipairs(modelsToLoad) do
        cAPI.RequestModel(GetHashKey(model))
    end
    
    -- Load all animations
    for _, anim in ipairs(animsToLoad) do
        cAPI.RequestAnimDict(anim)
    end
    
    -- Resources are now available for immediate use
end
```

***

## Error Handling and Performance

### UI Interaction Safety

* Request and Prompt functions include automatic cleanup
* Use reasonable timeouts to prevent infinite waiting
* Handle nil returns from cancelled prompts

### Resource Loading Optimization

* Load frequently used models/animations at startup
* Use `SetModelAsNoLongerNeeded()` after use
* Batch load resources when possible

### Screen Effect Performance

* Minimize fade frequency to avoid visual fatigue
* Use effects sparingly for better performance
* Always pair `StartFade()` with `EndFade()`

### Animation Management

* Check if animations are already playing to avoid conflicts
* Use appropriate speeds for realistic movement
* Clear ped tasks when switching between different animations

```lua
-- Safe animation switching
ClearPedTasks(PlayerPedId())  -- Clear current animations
Wait(100)                     -- Brief pause
cAPI.PlayAnim("new_dict", "new_anim", 8.0)  -- Play new animationlua
local x, y, z = cAPI.GetPosition()
print("Player is at:", x, y, z)
```
