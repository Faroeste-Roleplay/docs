# Events

## Server Events

### FRP:onCharacterLoaded

Triggered once the player has selected a character and spawned.

```lua
AddEventHandler('FRP:onCharacterLoaded', function(User, CharacterId) end)
```

### FRP:onCharacterLogout

Triggered when the player enters character selection if they were previously playing a character.

```lua
AddEventHandler('FRP:onCharacterLogout', function(User) end)
```

### FRP:onUserStarted

Triggered when player entrer on server and his account is loaded (before select character)

```lua
AddEventHandler('FRP:onUserLoaded', function(User) end)
```

### FRP:playerDropped

Triggered when player drop from game

```lua
AddEventHandler('FRP:playerDropped', function(source, User) end)
```

### FRP:JoinedGroup

Triggers when the player is added to a group

```lua
AddEventHandler('FRP:JoinedGroup', function(source, groupName) end)
```

### FRP:LeftGroup

Triggers when the player is removed from a group

```lua
AddEventHandler('FRP:JoinedGroup', function(source, groupName) end)
```

### FRP:onGroupFlagsChanged

Triggers when player group has changed flag status

```lua
AddEventHandler('FRP:onGroupFlagsChanged', function(source, groupName, flag, flagStatus) end)
```

## Network Events

### FRP:onPlayerDeath

Triggered when player dies

```lua
RegisterNetEvent('FRP:onPlayerDeath', function() end)
```
