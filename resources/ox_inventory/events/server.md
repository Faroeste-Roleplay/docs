---
description: >-
  This is not a comprehensive list of events and is missing events intended for
  internal use only.
---

# Server

## Handlers

{% hint style="warning" %}
These events **should not** be triggered by any other scripts.
{% endhint %}

### ox\_inventory:openedInventory

Triggered after an inventory is opened by a player.

```lua
AddEventHandler('ox_inventory:openedInventory', function(playerId, inventoryId) end)
```

* playerId: `number`
* inventoryId: `string`

### ox\_inventory:closedInventory

Triggered after an inventory is closed by a player.

```lua
AddEventHandler('ox_inventory:closedInventory', function(playerId, inventoryId) end)
```

### ox\_inventory:usedItem

```lua
AddEventHandler('ox_inventory:usedItem', function(playerId, name, slotId, metadata) end)
```

* playerId: `number`
* name: `string`
* slotId: `number`
* metadata?: `table`
