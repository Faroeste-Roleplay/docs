# Events

## Client Events

### FRP:preCharacterInitialization

Triggered when the character starts to be selected

```lua
AddEventHandler('FRP:preCharacterInitialization', function() end)
```

### FRP:preCharacterInitialization

Triggered when the character has finished loading and spawning on the map

```lua
AddEventHandler('FRP:postCharacterInitialization', function() end)
```

## Network Events

### FRP:onCharacterLoaded

Triggered once the player has selected a character and spawned.

```lua
RegisterNetEvent('FRP:onCharacterLoaded', function(charId) end)
```

### FRP:onCharacterLogout

Triggered when the player enters character selection if they were previously playing a character.

```lua
RegisterNetEvent('FRP:onCharacterLogout', function() end)
```

### FRP:JoinedGroup

Triggers when the player is added to a group

```lua
RegisterNetEvent('FRP:JoinedGroup', function(groupName) end)
```

### FRP:LeftGroup

Triggers when the player is removed from a group

```lua
RegisterNetEvent('FRP:JoinedGroup', function(groupName) end)
```

### FRP:onGroupFlagsChanged

Triggers when player group has changed flag status

```lua
RegisterNetEvent('FRP:JoinedGroup', function(groupName, flag, flagStatus) end)
```
