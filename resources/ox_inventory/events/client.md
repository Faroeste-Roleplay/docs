---
description: >-
  This is not a comprehensive list of events and is missing events intended for
  internal use only.
---

# Client

## Event Triggers

{% hint style="info" %}
These events **are safe** to trigger and handle in other scripts.
{% endhint %}

### ox\_inventory:disarm

Can be triggered to force the player to disarm.

```lua
TriggerClientEvent('ox_inventory:disarm', playerId, noAnim)
```

* playerId: `number`
* noAnim: `boolean`
  * If `true`, disarm animation will be skipped

## Event Handlers

{% hint style="warning" %}
These events **should not** be triggered by any other scripts.
{% endhint %}

### ox\_inventory:updateInventory

Triggered after inventory slots have been updated, included on load.\
Changes is a table containing all updated slot data indexed by slotId. Empty slots are `false`.

```lua
AddEventHandler('ox_inventory:updateInventory', function(changes) end)
```

* changes: `table<number, table | false>`

### ox\_inventory:currentWeapon

Triggered when a weapon is equipped or its metadata is altered.

```lua
AddEventHandler('ox_inventory:currentWeapon', function(weapon) end)
```

* weapon?: `table`

### ox\_inventory:itemCount

Triggered when the amount of an item in the player's inventory is changed.\
&#xNAN;_&#x4E;ote: Not available for ESX, use esx:addInventoryItem or esx:removeInventoryItem._

```lua
AddEventHandler('ox_inventory:itemCount', function(itemName, totalCount) end)
```

* itemName: `string`
* totalCount: `number`

### ox\_inventory:updateWeaponComponent

```lua
AddEventHandler('ox_inventory:updateWeaponComponent', function(action, componentHash, componentItem) end)
```

* action: `'added' | 'removed'`
* componentHash: `number`
* componentItem: `string`

### ox\_inventory:usedItem

```lua
AddEventHandler('ox_inventory:usedItem', function(name, slotId, metadata) end)
```

* name: `string`
* slotId: `number`
* metadata?: `table`
