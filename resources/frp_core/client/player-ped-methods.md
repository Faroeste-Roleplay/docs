# Player/Ped Methods

## Overview

This documentation covers the Client API (cAPI) functions available in the FRP Framework. The API provides essential functionality for player interactions, world manipulation, UI elements, and game mechanics.

## Table of Contents

* Character Management
* Player Model & Appearance
* Animations & Interactions
* Health & Stats Management
* Player State Management
* Wanted System
* Sickness System

***

## Character Management

## Player Model & Appearance

### `cAPI.SetPlayerPedModel`

Changes the player's ped model with proper loading and cleanup.

**Parameters:**

* `model` (string): Model name to set

**Features:**

* Validates model before loading
* Handles model loading wait
* Sets random outfit variation
* Fixes stuck ammo clothing pieces
* Cleans up model from memory

**Example:**

```lua
cAPI.SetPlayerPedModel("mp_female")
```

### `cAPI.SetPedScale`

Sets the scale of a ped entity.

**Parameters:**

* `ped` (number): Ped entity handle
* `num` (number): Scale multiplier (default: 1.0)

**Example:**

```lua
cAPI.SetPedScale(PlayerPedId(), 1.2) -- 20% larger
```

### `cAPI.SetPlayerScale`

Sets player scale based on character height from appearance data.

**Example:**

```lua
cAPI.SetPlayerScale() -- Uses height from gCharAppearence
```

### `cAPI.SetPlayerAppearence`

Applies the complete character appearance including scale and outfits.

**Features:**

* Applies appearance for MP models
* Sets outfit presets for custom models
* Updates player scale

**Example:**

```lua
cAPI.SetPlayerAppearence()
```

### `cAPI.SetPlayerDefaultModel`

Sets the player model to the stored default model.

**Example:**

```lua
cAPI.SetPlayerDefaultModel()
```

### `cAPI.ReloadSkin`

Reloads the player's skin with timeout protection and health preservation.

**Features:**

* 5-second timeout between reloads
* Saves and restores health/stamina
* Updates appearance and scale
* Shows timeout error if called too frequently

**Example:**

```lua
cAPI.ReloadSkin()
```

### `cAPI.SetPlayerWhistle`

Configures player whistle characteristics from appearance data.

**Example:**

```lua
cAPI.SetPlayerWhistle()
```

### `cAPI.FixStuckAmmoClothingPiece`

Fixes stuck ammunition and pants clothing pieces on MP models.

**Example:**

```lua
cAPI.FixStuckAmmoClothingPiece()
```

### `cAPI.IsModelMp`

Checks if a ped is using a multiplayer model.

**Parameters:**

* `ped` (number): Ped entity handle

**Returns:**

* `boolean`: True if using MP model

**Example:**

```lua
if cAPI.IsModelMp(PlayerPedId()) then
    print("Player is using MP model")
end
```

***

## Animations & Interactions

### `cAPI.TaskScriptedAnim`

Plays scripted animations with proper setup.

**Parameters:**

* `scriptedAnimName` (string): Animation name ("eat")

**Example:**

```lua
cAPI.TaskScriptedAnim("eat") -- Play eating animation
```

***

## Health & Stats Management

### `cAPI.VaryPedHealth`

Varies ped health instantly or over time.

**Parameters:**

* `ped` (number): Ped entity handle
* `variation` (number): Health change amount
* `variationTime` (number, optional): Time in seconds for gradual change

**Example:**

```lua
cAPI.VaryPedHealth(PlayerPedId(), 50) -- Instant heal
cAPI.VaryPedHealth(PlayerPedId(), 100, 10) -- Heal 100 over 10 seconds
```

### `cAPI.VaryPedStamina`

Varies ped stamina instantly or over time.

**Parameters:**

* `ped` (number): Ped entity handle
* `variation` (number): Stamina change amount (-1000.0 to 1000.0)
* `variationTime` (number, optional): Time in seconds for gradual change

**Example:**

```lua
cAPI.VaryPedStamina(PlayerPedId(), 200) -- Instant stamina boost
cAPI.VaryPedStamina(PlayerPedId(), -50, 5) -- Drain 50 stamina over 5 seconds
```

### `cAPI.VaryPedCore`

Varies ped core values (health/stamina cores) instantly or over time.

**Parameters:**

* `ped` (number): Ped entity handle
* `core` (number): Core type (0 = health, 1 = stamina)
* `variation` (number): Core change amount
* `variationTime` (number, optional): Time in seconds for gradual change
* `goldenEffect` (boolean, optional): Show golden effect

**Example:**

```lua
cAPI.VaryPedCore(PlayerPedId(), 0, 50) -- Heal health core
cAPI.VaryPedCore(PlayerPedId(), 1, -25, 3) -- Drain stamina core over 3 seconds
```

### Player-Specific Health Functions

#### `cAPI.VaryPlayerHealth(variation, variationTime)`

#### `cAPI.VaryPlayerStamina(variation, variationTime)`

#### `cAPI.VaryPlayerCore(core, variation, variationTime, goldenEffect)`

Convenience functions that apply health/stamina/core changes to the player.

**Example:**

```lua
cAPI.VaryPlayerHealth(100, 5) -- Heal 100 HP over 5 seconds
cAPI.VaryPlayerStamina(50) -- Instant stamina boost
cAPI.VaryPlayerCore(0, 25) -- Heal health core
```

### `cAPI.SetHealth`

Sets player health to specific amount.

**Parameters:**

* `amount` (number): Health value to set

**Example:**

```lua
cAPI.SetHealth(200)
```

### `cAPI.GetHealth`

Gets current player health.

**Returns:**

* `number`: Current health value

**Example:**

```lua
local health = cAPI.GetHealth()
print("Current health:", health)
```

### Health State Management

#### `cAPI.SaveHealth`

Saves current health and stamina values for later restoration. Its a cached data only

#### `cAPI.ReturnLastStatus`

Restores previously saved health and stamina values.

**Example:**

```lua
cAPI.SaveHealth() -- Save current state
-- ... do something that changes health ...
cAPI.ReturnLastStatus() -- Restore saved state
```

***

## Player State Management

### `cAPI.IsPlayerLassoed`

Checks if player is currently lassoed.

**Returns:**

* `boolean`: True if player is lassoed

**Example:**

```lua
if cAPI.IsPlayerLassoed() then
    print("Player is lassoed!")
end
```

### `cAPI.TeleportPlayerToWaypoint`

Teleports player to the active waypoint on the map.

**Features:**

* Handles vehicle passengers
* Uses ground detection
* Shows error if no waypoint set
* Uses advanced teleportation system

**Example:**

```lua
cAPI.TeleportPlayerToWaypoint()
```

## Enhanced Teleportation Functions

### `cAPI.TeleportPlayer`

Advanced teleportation with collision detection and ground finding.

**Parameters:**

* `position` (table): Coordinates {x, y, z, w}
* `variation` (number, optional): Random offset range

**Features:**

* Ground Z detection
* Collision loading
* Native teleport system
* Random position variation

### `cAPI.TeleportPlayerWithGroundZ`

Teleports player using the advanced ground detection system.

**Parameters:**

* `position` (table): Destination coordinates

***

## Wanted System

### `cAPI.AddWantedTime`

Sets or adds wanted status with time duration.

**Parameters:**

* `wanted` (boolean): Wanted status
* `time` (number): Duration in minutes

**Features:**

* Extends existing wanted time
* Syncs with server
* Updates player state

**Example:**

```lua
cAPI.AddWantedTime(true, 10) -- Wanted for 10 minutes
cAPI.AddWantedTime(false) -- Clear wanted status
```

### `cAPI.IsWanted`

Checks if player is currently wanted.

**Returns:**

* `boolean`: True if player is wanted

**Example:**

```lua
if cAPI.IsWanted() then
    print("Player is wanted by law!")
end
```

## Error Handling and Best Practices

### Performance Considerations

* Use `cAPI.ReloadSkin()` sparingly due to timeout protection
* Cache health/stamina values when possible
* Avoid frequent appearance updates

### Common Patterns

```lua
-- Health management with restoration
cAPI.SaveHealth()
-- ... perform actions that modify health ...
cAPI.ReturnLastStatus()

-- Safe skin reload with error handling
cAPI.ReloadSkin() -- Handles timeout automatically

-- Wanted system usage
if cAPI.IsWanted() then
    cAPI.AddWantedTime(true, 5) -- Add 5 more minutes
else
    cAPI.AddWantedTime(true, 10) -- Set 10 minute wanted level
end
```
