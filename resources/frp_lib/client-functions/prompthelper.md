# PromptHelper

A utility module for handling prompt interactions, specifically designed to manage hold-mode prompt completions with automatic disabling and re-enabling.

## Overview

The PromptHelper provides utilities for safely handling prompt interactions, particularly for hold-mode prompts. It includes built-in safeguards to prevent prompt spam and ensures proper state management.

## API Reference

### promptHelper:hasPromptHoldModeCompleted(promptId, disableMsOnComplete?)

Checks if a hold-mode prompt has been completed and handles the prompt state safely.

**Parameters:**

* `promptId` (`number`) - The ID of the prompt to check
* `disableMsOnComplete` (`number`, optional) - Time in milliseconds to disable the prompt after completion (default: 1000ms)

**Returns:** `boolean` - `true` if the prompt was completed and is ready for action, `false` otherwise

## How It Works

The method performs several safety checks and state management operations:

1. **Completion Check**: Verifies if the hold mode has been completed
2. **Enabled State Check**: Ensures the prompt is currently enabled
3. **Temporary Disable**: Disables the prompt immediately after completion
4. **Control Disable**: Disables all controls for one frame to prevent input conflicts
5. **Auto Re-enable**: Automatically re-enables the prompt after the specified delay

## Usage Examples

### Basic Usage

```lua
local promptId = PromptBuilder:new()
    :setText("Hold to interact")
    :setControl(`INPUT_CONTEXT`)
    :setMode('Hold', 2000)
    :build()

-- In your main loop
if promptHelper:hasPromptHoldModeCompleted(promptId) then
    -- Action was completed
    print("Player completed the hold interaction!")
    -- Perform your action here
end
```

### Custom Disable Duration

```lua
-- Disable for 5 seconds after completion
if promptHelper:hasPromptHoldModeCompleted(promptId, 5000) then
    -- Long action that takes time
    TriggerServerEvent('perform:long:action')
end

-- Disable for half a second for quick actions
if promptHelper:hasPromptHoldModeCompleted(quickPromptId, 500) then
    -- Quick action
    DoSomethingQuick()
end
```

### In a Game Loop with Multiple Prompts

```lua
local tick = scheduler:setTick(function()
    -- Check multiple prompts
    if promptHelper:hasPromptHoldModeCompleted(doorPrompt, 1500) then
        OpenDoor()
    end
    
    if promptHelper:hasPromptHoldModeCompleted(shopPrompt, 2000) then
        TriggerEvent('shop:open')
    end
    
    if promptHelper:hasPromptHoldModeCompleted(vehiclePrompt) then
        -- Uses default 1000ms disable time
        EnterVehicle()
    end
end)
```

## Advanced Example with Animation

```lua
local barbershopPrompt = PromptBuilder:new()
    :setText("Hold to use barbershop")
    :setControl(`INPUT_CONTEXT`)
    :setMode('Hold', 2000)
    :setPoint(barbershopCoords)
    :setRadius(2.0)
    :build()

local tick = scheduler:setTick(function()
    if promptHelper:hasPromptHoldModeCompleted(barbershopPrompt, 3000) then
        -- Animate to chair
        animToSeatOnChair(GetEntityCoords(PlayerPedId()))
        
        -- Wait for animation to complete
        CreateThread(function()
            while not Citizen.InvokeNative(0xAA135F9482C82CC3, PlayerPedId(), `PROP_PLAYER_BARBER_SEAT`) do
                Wait(100)
            end
            
            -- Open barbershop menu
            TriggerEvent("appearance:shop:menu", "barbershop")
        end)
    end
    
    -- Display the prompt group
    PromptSetActiveGroupThisFrame(promptGroupId, CreateVarString(10, 'LITERAL_STRING', "Barbershop"))
end)
```

## Safety Features

### Automatic Prompt Disabling

After a successful completion, the prompt is automatically disabled for the specified duration to prevent:

* Accidental repeated activations
* Input spam
* Unintended behavior during animations or transitions

### Frame-Perfect Control Handling

The system disables all control actions for one frame after prompt completion, ensuring:

* Clean state transitions
* Prevention of input conflicts
* Proper prompt deactivation

### State Validation

Multiple checks ensure the prompt is in the correct state:

* Verifies hold mode completion
* Checks if prompt is enabled
* Waits one tick for proper state synchronization

## Best Practices

### 1. Use Appropriate Disable Durations

```lua
-- For quick actions (0.5-1 second)
promptHelper:hasPromptHoldModeCompleted(quickActionPrompt, 500)

-- For normal actions (1-2 seconds)
promptHelper:hasPromptHoldModeCompleted(normalPrompt, 1000)

-- For slow actions like animations (2+ seconds)
promptHelper:hasPromptHoldModeCompleted(animationPrompt, 3000)
```

### 2. Combine with Scheduler

```lua
local tick = scheduler:setTick(function()
    if promptHelper:hasPromptHoldModeCompleted(promptId) then
        -- Perform action
        PerformAction()
        
        -- Optionally clear the tick if one-time use
        scheduler:clearTick(tick)
    end
end)
```

### 3. Handle Multiple States

```lua
local isProcessing = false

if not isProcessing and promptHelper:hasPromptHoldModeCompleted(promptId, 2000) then
    isProcessing = true
    
    -- Perform long-running action
    CreateThread(function()
        DoLongAction()
        Wait(5000)
        isProcessing = false
    end)
end
```

## Technical Notes

* The method includes a `Wait(0)` to ensure proper tick synchronization
* All control actions are disabled for exactly one frame after completion
* The prompt is temporarily disabled and automatically re-enabled
* Compatible with all PromptBuilder-created prompts in hold mode
* Thread-safe for use in multiple concurrent game loops
