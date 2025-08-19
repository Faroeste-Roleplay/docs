# Virtual World System

### Overview

The Virtual World system provides a comprehensive solution for managing isolated player environments within your server. Players in different virtual worlds cannot interact with each other, making it perfect for creating private sessions, instances, or separate game areas.

### Key Concepts

* **Global World (ID: 0)**: The default world where all players start
* **Virtual World**: An isolated environment with its own set of players
* **Routing Bucket**: The underlying mechanism that separates players between worlds

### API Reference

#### VirtualWorld:Create(players, virtualWorldId)

Creates a new virtual world and moves the specified players into it.

**Parameters**

* `players` (table): Array of player IDs to add to the virtual world
* `virtualWorldId` (number, optional): Specific ID for the virtual world. If not provided, auto-generates the next available ID

**Returns**

* `virtualWorld` (table): The created virtual world object containing `id` and `players`

**Example**

```lua
-- Create a virtual world with specific players
local world = VirtualWorld:Create({1, 2, 3})
print("Created world with ID:", world.id)

-- Create a virtual world with a specific ID
local customWorld = VirtualWorld:Create({4, 5}, 100)
print("Created world with custom ID:", customWorld.id)
```

**Notes**

* If a player is already in another virtual world, they will be automatically removed from their current world
* Players are moved using `SetPlayerRoutingBucket()` function

***

#### VirtualWorld:IsValidWorld(virtualWorldId)

Checks if a virtual world exists.

**Parameters**

* `virtualWorldId` (number): The virtual world ID to validate

**Returns**

* `boolean`: True if the world exists, false otherwise

**Example**

```lua
if VirtualWorld:IsValidWorld(100) then
    print("World 100 exists!")
else
    print("World 100 does not exist")
end
```

***

#### VirtualWorld:DeleteWorld(virtualWorldId)

Deletes a virtual world and moves all its players back to the global world.

**Parameters**

* `virtualWorldId` (number): The virtual world ID to delete

**Returns**

* None

**Example**

```lua
-- Delete world 100
VirtualWorld:DeleteWorld(100)
print("World 100 has been deleted")
```

**Notes**

* All players in the deleted world are automatically moved to the global world (ID: 0)
* If the world doesn't exist, the function returns silently

***

#### VirtualWorld:AddPlayerToGlobalWorld(playerId)

Moves a player to the global world (ID: 0).

**Parameters**

* `playerId` (number): The player ID to move to the global world

**Returns**

* None

**Example**

```lua
-- Move player 5 to the global world
VirtualWorld:AddPlayerToGlobalWorld(5)
```

***

#### VirtualWorld:AddPlayerOnVirtualWorld(playerId, virtualWorldId)

Adds a player to a specific virtual world. If the world doesn't exist, it creates a new one.

**Parameters**

* `playerId` (number): The player ID to add
* `virtualWorldId` (number): The target virtual world ID

**Returns**

* `virtualWorld` (table): The virtual world object (if created new)
* None (if added to existing world)

**Example**

```lua
-- Add player 1 to world 50
VirtualWorld:AddPlayerOnVirtualWorld(1, 50)

-- Add player 2 to world 75 (creates world if it doesn't exist)
VirtualWorld:AddPlayerOnVirtualWorld(2, 75)
```

**Events Triggered**

* `VirtualWorld:PlayerAddedToWorld`: Fired when a player is successfully added to a world

**Notes**

* If the player is already in another world, they are automatically removed first
* If the target world doesn't exist, it creates a new world with the player

***

#### VirtualWorld:RemovePlayerFromVirtualWorld(playerId)

Removes a player from their current virtual world.

**Parameters**

* `playerId` (number): The player ID to remove

**Returns**

* None

**Example**

```lua
-- Remove player 3 from their current world
VirtualWorld:RemovePlayerFromVirtualWorld(3)
```

**Events Triggered**

* `VirtualWorld:PlayerLeftFromWorld`: Fired when a player is successfully removed from a world

**Notes**

* If the player is not in any virtual world, the function returns silently
* The player's routing bucket is reset and they're removed from tracking

***

#### VirtualWorld:GetPlayerVirtualWorld(playerId)

Gets the virtual world object that a player is currently in.

**Parameters**

* `playerId` (number): The player ID to query

**Returns**

* `virtualWorld` (table): The virtual world object containing `id` and `players`
* `nil`: If the player is not in any virtual world

**Example**

```lua
local playerWorld = VirtualWorld:GetPlayerVirtualWorld(1)
if playerWorld then
    print("Player 1 is in world:", playerWorld.id)
    print("Players in this world:", #playerWorld.players)
else
    print("Player 1 is not in any virtual world")
end
```

***

#### VirtualWorld:GetPlayersFromVirtualWorld(virtualWorldId)

Gets all players currently in a specific virtual world.

**Parameters**

* `virtualWorldId` (number): The virtual world ID to query

**Returns**

* `players` (table): Array of player IDs in the virtual world
* `{}` (empty table): If the world doesn't exist

**Example**

```lua
local players = VirtualWorld:GetPlayersFromVirtualWorld(50)
print("Players in world 50:", #players)

for i, playerId in ipairs(players) do
    print("Player", i, ":", playerId)
end
```

### Events

The system triggers the following events that you can listen to:

#### VirtualWorld:PlayerAddedToWorld

Fired when a player is added to a virtual world.

**Parameters:**

* `playerId` (number): The player who was added
* `virtualWorldId` (number): The world they were added to

#### VirtualWorld:PlayerLeftFromWorld

Fired when a player leaves a virtual world.

**Parameters:**

* `playerId` (number): The player who left
* `virtualWorldId` (number): The world they left from

### Usage Examples

#### Creating a Private Session

```lua
-- Create a private world for a group of friends
local friendsWorld = VirtualWorld:Create({10, 15, 22, 8})
print("Friends are now in private world:", friendsWorld.id)
```

#### Managing Player Movements

```lua
-- Check if player is in a world before moving them
local currentWorld = VirtualWorld:GetPlayerVirtualWorld(5)
if currentWorld then
    print("Player 5 is currently in world:", currentWorld.id)
    -- Move them to a different world
    VirtualWorld:AddPlayerOnVirtualWorld(5, 100)
else
    print("Player 5 is in the global world")
    -- Add them to a virtual world
    VirtualWorld:AddPlayerOnVirtualWorld(5, 50)
end
```

#### Event Handling

```lua
-- Listen for players joining worlds
RegisterNetEvent("VirtualWorld:PlayerAddedToWorld")
AddEventHandler("VirtualWorld:PlayerAddedToWorld", function(playerId, worldId)
    print("Player", playerId, "joined world", worldId)
    -- Send welcome message or setup world-specific features
end)

-- Listen for players leaving worlds
RegisterNetEvent("VirtualWorld:PlayerLeftFromWorld")
AddEventHandler("VirtualWorld:PlayerLeftFromWorld", function(playerId, worldId)
    print("Player", playerId, "left world", worldId)
    -- Cleanup player-specific data
end)
```

#### World Management

```lua
-- Create multiple worlds for different activities
local racingWorld = VirtualWorld:Create({1, 2, 3, 4}, 200)
local deathMatchWorld = VirtualWorld:Create({5, 6, 7, 8}, 201)

-- Later, clean up the worlds
VirtualWorld:DeleteWorld(200) -- Racing participants return to global world
VirtualWorld:DeleteWorld(201) -- DM participants return to global world
```

### Best Practices

1.  **Always validate worlds** before performing operations:

    ```lua
    if VirtualWorld:IsValidWorld(worldId) then
        -- Safe to proceed
    end
    ```
2.  **Handle player disconnections** by removing them from virtual worlds:

    ```lua
    AddEventHandler("playerDropped", function()
        local playerId = source
        VirtualWorld:RemovePlayerFromVirtualWorld(playerId)
    end)
    ```
3.  **Use meaningful world IDs** for better organization:

    ```lua
    local RACING_WORLD = 1000
    local DEATHMATCH_WORLD = 2000
    local MISSION_WORLD_BASE = 3000
    ```
4.  **Monitor world populations** to manage server resources:

    ```lua
    local players = VirtualWorld:GetPlayersFromVirtualWorld(worldId)
    if #players == 0 then
        VirtualWorld:DeleteWorld(worldId) -- Clean up empty worlds
    end
    ```

### Troubleshooting

#### Common Issues

**Players can still see each other after being separated**

* Ensure `SetPlayerRoutingBucket()` is properly implemented in your server
* Check that the virtual world IDs are different

**Memory leaks from abandoned worlds**

* Implement regular cleanup of empty virtual worlds
* Always delete worlds when they're no longer needed

**Players stuck in virtual worlds**

* Use `VirtualWorld:AddPlayerToGlobalWorld()` as a fallback
* Implement admin commands to force player movement

#### Debug Information

```lua
-- Get all virtual worlds (for debugging)
for worldId, world in pairs(VirtualWorlds) do
    print("World", worldId, "has", #world.players, "players")
end

-- Check player's current world
local playerId = 1
local world = VirtualWorld:GetPlayerVirtualWorld(playerId)
if world then
    print("Player", playerId, "is in world", world.id)
else
    print("Player", playerId, "is in global world or not tracked")
end
```
