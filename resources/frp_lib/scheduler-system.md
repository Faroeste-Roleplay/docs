# Scheduler System

The scheduler provides utilities for managing repeating tasks and game loops with clean cleanup capabilities.

### scheduler:setTick

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

### scheduler:clearTick

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

## Best Practices

### Scheduler Management

* Always store tick references for cleanup
* Clear ticks when no longer needed
* Use resource stop handlers for automatic cleanup

###
