# Utility Functions

## Utility Functions

A collection of utility functions providing enum creation, random string generation, scheduling, and progress bar functionality for RedM development.

## Enum System

### enum(name)

Creates a bidirectional enumeration that maps both keys to values and values to keys.

**Parameters:**

* `name` (`string`) - The global name for the enum

**Returns:** `function` - A function that accepts the enum definition

**Usage:**

```lua
enum 'eTriggerType' {
    Invalid = 1,
    Incapacitate = 2,
    Wound = 3,
    Die = 4,
    Revive = 5,
    ReviveByCommand = 6,
    Respawn = 7,
    Heal = 8,
}

-- Usage examples
print(eTriggerType.Wound)        -- Output: 3
print(eTriggerType[3])           -- Output: "Wound"
print(eTriggerType.Invalid)      -- Output: 1
print(eTriggerType[1])           -- Output: "Invalid"
```

**Benefits:**

* Bidirectional mapping (key→value and value→key)
* Type safety for state management
* Clear, readable code
* Easy debugging with reverse lookups

***

## Random String Generation

### RandomStr(length)

Generates a random string containing uppercase letters, lowercase letters.

**Parameters:**

* `length` (`number`) - Length of the string to generate

**Returns:** `string` - Random alphanumeric string

**Character Set:** A-Z, a-z (62 characters total)

```lua
local randomId = RandomStr(8)
print(randomId) -- Example: "aBc3XyZ1"

local sessionToken = RandomStr(32)
-- Generates a 32-character random string
```

### RandomInt(length)

Generates a random numeric string.

**Parameters:**

* `length` (`number`) - Length of the numeric string to generate

**Returns:** `string` - Random numeric string

**Character Set:** 0-9 (10 characters total)

```lua
local randomCode = RandomInt(6)
print(randomCode) -- Example: "482759"

local playerId = RandomInt(10)
-- Generates a 10-digit random number as string
```

**Use Cases:**

* Unique identifiers
* Session tokens
* Random passwords
* Database keys
* Temporary file names

***

## Scheduler System

The scheduler provides utilities for managing repeating tasks and game loops with clean cleanup capabilities.

### scheduler:setTick(fn)

Creates a repeating task that runs every game frame.

**Parameters:**

* `fn` (`function`) - Function to execute each frame

**Returns:** `SetTickCookie` - A function to stop the tick

**Type Definition:**

```lua
---@alias SetTickCookie fun()
```

**Usage:**

```lua
local stopTick = scheduler:setTick(function()
    -- This runs every frame
    local playerPos = GetEntityCoords(PlayerPedId())
    DrawMarker(1, playerPos.x, playerPos.y, playerPos.z - 1.0, 0, 0, 0, 0, 0, 0, 2.0, 2.0, 1.0, 255, 0, 0, 100, false, true, 2, false, nil, nil, false)
end)

-- Stop the tick when no longer needed
stopTick()
```

### scheduler:clearTick(endTick)

Stops a previously created tick.

**Parameters:**

* `endTick` (`SetTickCookie`) - The function returned by `setTick`

**Usage:**

```lua
local tick = scheduler:setTick(function()
    -- Game loop logic
end)

-- Later, stop the tick
scheduler:clearTick(tick)
```

### Complete Scheduler Example

```lua
local gTick
local isNearShop = false

-- Start monitoring player proximity to shop
gTick = scheduler:setTick(function()
    local playerPos = GetEntityCoords(PlayerPedId())
    local shopPos = vector3(-308.3, 805.7, 118.9)
    local distance = #(playerPos - shopPos)
    
    if distance < 5.0 then
        if not isNearShop then
            isNearShop = true
            print("Player entered shop area")
        end
        
        -- Show shop prompt
        DrawText3D(shopPos.x, shopPos.y, shopPos.z + 1.0, "Press E to enter shop")
        
    else
        if isNearShop then
            isNearShop = false
            print("Player left shop area")
        end
    end
end)

-- Stop monitoring when resource stops
AddEventHandler('onResourceStop', function(resourceName)
    if resourceName == GetCurrentResourceName() then
        if gTick then
            scheduler:clearTick(gTick)
        end
    end
end)
```

***

## Progress Bar System

### Progressbar(name, label, duration, useWhileDead, canCancel, disableControls, animation, prop, propTwo, onFinish, onCancel)

Creates a progress bar with customizable options and callbacks.

**Parameters:**

* `name` (`string`) - Unique identifier for the progress bar
* `label` (`string`) - Display text shown during progress
* `duration` (`number`) - Duration in milliseconds
* `useWhileDead` (`boolean`) - Whether the progress bar works while dead
* `canCancel` (`boolean`) - Whether the player can cancel the action
* `disableControls` (`table`) - Controls to disable during progress
* `animation` (`table`) - Animation configuration
* `prop` (`table`) - Primary prop configuration
* `propTwo` (`table`) - Secondary prop configuration
* `onFinish` (`function`) - Callback when progress completes successfully
* `onCancel` (`function`) - Callback when progress is cancelled

### Basic Usage

```lua
Progressbar(
    "drinking_coffee",           -- Unique name
    "Drinking coffee...",        -- Display label
    5000,                       -- 5 seconds
    false,                      -- Don't use while dead
    true,                       -- Can be cancelled
    {                          -- Disable controls
        disableMovement = true,
        disableCarMovement = true,
        disableMouse = false,
        disableCombat = true,
    },
    {},                        -- No animation
    {},                        -- No props
    {},                        -- No secondary prop
    function()                 -- On completion
        print("Coffee finished!")
        -- Add health or whatever
    end,
    function()                 -- On cancel
        print("Coffee drinking cancelled!")
    end
)
```

### Advanced Usage with Animation and Props

```lua
Progressbar(
    "repairing_vehicle",
    "Repairing vehicle engine...",
    15000,  -- 15 seconds
    false,
    true,
    {
        disableMovement = true,
        disableCarMovement = true,
        disableMouse = false,
        disableCombat = true,
    },
    {
        type = "anim",
        dict = "mini@repair",
        lib = "fixing_a_ped",
        flag = 16,
    },
    {
        model = "prop_tool_wrench",
        bone = 57005,
        coords = vector3(0.09, 0.0, -0.02),
        rotation = vector3(-78.0, 13.0, 28.0),
    },
    {},  -- No second prop
    function()
        -- Repair completed
        TriggerServerEvent('vehicle:repair:complete')
        exports['mythic_notify']:DoNotification({
            text = 'Vehicle repaired successfully!',
            type = 'success'
        })
    end,
    function()
        -- Repair cancelled
        exports['mythic_notify']:DoNotification({
            text = 'Repair cancelled',
            type = 'error'
        })
    end
)
```

### Real-world Examples

#### Medical Treatment

```lua
Progressbar(
    "medical_treatment",
    "Applying bandages...",
    8000,
    false,
    true,
    { disableMovement = true, disableCombat = true },
    {
        type = "anim",
        dict = "amb@world_human_clipboard@male@idle_a",
        lib = "idle_c",
        flag = 49,
    },
    {
        model = "prop_ld_health_pack",
        bone = 18905,
        coords = vector3(0.13, 0.05, 0.05),
        rotation = vector3(-50.0, 290.0, 0.0),
    },
    {},
    function()
        -- Heal player
        TriggerEvent('medical:heal', 50)
    end,
    function()
        -- Treatment cancelled
        TriggerEvent('medical:cancel')
    end
)
```

#### Crafting Item

```lua
Progressbar(
    "crafting_weapon",
    "Crafting weapon...",
    20000,
    false,
    true,
    { disableMovement = true },
    {
        type = "anim",
        dict = "anim@amb@clubhouse@tutorial@bkr_tut_ig3@",
        lib = "machinic_loop_mechandplayer",
        flag = 1,
    },
    {},
    {},
    function()
        TriggerServerEvent('crafting:complete', 'weapon_pistol')
    end,
    nil  -- No cancel callback needed
)
```

## Best Practices

### Enum Usage

* Use meaningful names for enum values
* Keep enum values sequential for debugging
* Use enums for state machines and game modes

### Random Generation

* Use `RandomStr` for user-facing identifiers
* Use `RandomInt` for numeric codes
* Consider the security implications for sensitive data

### Scheduler Management

* Always store tick references for cleanup
* Clear ticks when no longer needed
* Use resource stop handlers for automatic cleanup

### Progress Bars

* Provide clear, descriptive labels
* Use appropriate durations for realism
* Always handle both completion and cancellation
* Disable relevant controls during progress
* Use animations and props for immersion
