---
description: >-
  A fluent API for creating and managing UI prompts in RedM with method chaining
  support.
---

# PromptBuilder

## Overview

The PromptBuilder provides a simple, chainable interface for creating interactive UI prompts. It automatically handles prompt registration, cleanup, and provides various configuration options through a fluent API pattern.

## Basic Usage

```lua
local promptId = PromptBuilder:new()
    :setText("Press to interact")
    :setControl(`INPUT_CONTEXT`)
    :setVisible(true)
    :setEnabled(true)
    :build()
```

## API Reference

### PromptBuilder:new()

Creates a new prompt instance with default settings.

**Returns:** `PromptBuilderChain` - A chainable prompt builder instance

**Default Settings:**

* Text: "Sem nome {promptId}"
* Enabled: `true`
* Visible: `true`
* Mode: Standard

### Methods

#### setControl

Sets the control input(s) for the prompt.

**Parameters:**

* `controlHashLike` - Primary control hash or input
* `secondControlHashLike` (optional) - Secondary control hash

**Returns:** `PromptBuilderChain` - For method chaining

```lua
PromptBuilder:new()
    :setControl(`INPUT_CONTEXT`)
    :setControl(`INPUT_ATTACK`, `INPUT_AIM`) -- Multiple controls
```

#### setText

Sets the display text for the prompt.

**Parameters:**

* `text` (`string|number`) - Text to display or text hash

**Returns:** `PromptBuilderChain` - For method chaining

```lua
PromptBuilder:new()
    :setText("Press E to open door")
    :setText(GetHashKey("PROMPT_OPEN_DOOR")) -- Using hash
```

#### setVisible

Controls prompt visibility.

**Parameters:**

* `visible` (`boolean`) - Whether the prompt should be visible

**Returns:** `PromptBuilderChain` - For method chaining

#### setMode

Sets the prompt interaction mode.

**Parameters:**

* `mode` (`'Standard'|'Hold'|'AutoFill'`) - Interaction mode
* `...params` - Additional parameters for specific modes

**Modes:**

* `'Standard'` - Single press interaction
* `'Hold'` - Hold button interaction (requires hold duration)
* `'AutoFill'` - Auto-filling hold interaction

**Returns:** `PromptBuilderChain` - For method chaining

```lua
PromptBuilder:new()
    :setMode('Hold', 2000) -- Hold for 2 seconds
    :setMode('AutoFill', 1500) -- Auto-fill after 1.5 seconds
```

#### setGroup

Assigns the prompt to a specific group.

**Parameters:**

* `groupHash` - Group identifier hash

**Returns:** `PromptBuilderChain` - For method chaining

#### setPoint

Sets a 3D world position for the prompt context.

**Parameters:**

* `point` (`{x: number, y: number, z: number}`) - World coordinates

**Returns:** `PromptBuilderChain` - For method chaining

```lua
PromptBuilder:new()
    :setPoint({x = 100.0, y = 200.0, z = 30.0})
```

#### setRadius

Sets the interaction radius for 3D prompts.

**Parameters:**

* `radius` (`number`) - Interaction radius in game units

**Returns:** `PromptBuilderChain` - For method chaining

#### setVolume

Sets the interaction volume for 3D prompts.

**Parameters:**

* `volume` (`number`) - Volume identifier

**Returns:** `PromptBuilderChain` - For method chaining

#### setEnabled

Controls whether the prompt can be interacted with.

**Parameters:**

* `enabled` (`boolean`) - Whether the prompt is enabled

**Returns:** `PromptBuilderChain` - For method chaining

#### build

Finalizes the prompt creation and registers it with the game.

**Returns:** `number` - The prompt ID for further manipulation

## Complete Example

```lua
local shopPrompt = PromptBuilder:new()
    :setText("Hold to browse shop")
    :setControl(`INPUT_CONTEXT`)
    :setMode('Hold', 1500)
    :setPoint({x = -308.3, y = 805.7, z = 118.9})
    :setRadius(2.0)
    :setVisible(true)
    :setEnabled(true)
    :build()

-- Later in your game loop
if PromptHasHoldModeCompleted(shopPrompt) then
    -- Open shop menu
    TriggerEvent('shop:open')
end
```

## Memory Management

The PromptBuilder automatically handles cleanup when the resource stops. All created prompts are tracked and properly deleted to prevent memory leaks.

**Automatic Cleanup:**

* Prompts are automatically deleted when the resource stops
* Manual deletion is supported via `PromptDelete(promptId)`
* Deleted prompts are removed from the tracking pool

## Advanced Usage

### Custom Prompt Groups

```lua
local groupHash = GetHashKey("SHOP_PROMPTS")

local buyPrompt = PromptBuilder:new()
    :setText("Buy Item")
    :setControl(`INPUT_CONTEXT`)
    :setGroup(groupHash)
    :build()

local sellPrompt = PromptBuilder:new()
    :setText("Sell Item")
    :setControl(`INPUT_ATTACK`)
    :setGroup(groupHash)
    :build()

-- Display group
PromptSetActiveGroupThisFrame(groupHash, CreateVarString(10, 'LITERAL_STRING', "Shop Actions"))
```

### 3D World Prompts

```lua
local doorPrompt = PromptBuilder:new()
    :setText("Open Door")
    :setControl(`INPUT_CONTEXT`)
    :setPoint(doorCoords)
    :setRadius(1.5)
    :setMode('Hold', 1000)
    :build()
```

## Notes

* All methods return the chain instance, allowing for fluent method chaining
* Prompts are automatically cleaned up on resource stop
* The system handles both hash-based and string-based text inputs
* 3D prompts require both point and radius to function properly
