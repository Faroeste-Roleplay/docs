# Client - Functions

## Client

All exports with the `options` argument expect a table with the targeting properties [here](https://overextended.dev/ox_target/TargetOptions).

For some examples you can refer to [defaults.lua](https://github.com/overextended/ox_target/blob/main/client/defaults.lua) or [debug.lua](https://github.com/overextended/ox_target/blob/main/client/debug.lua).

### disableTargeting <a href="#disabletargeting" id="disabletargeting"></a>

Toggle the availability of the targeting menu.

```lua
exports.ox_target:disableTargeting(state)
```

* state: `boolean`
  * Setting `state` to `true` will turn off the targeting eye if it is active and prevent it from reopening until `state` is set to `false` again.

### addGlobalOption <a href="#addglobaloption" id="addglobaloption"></a>

Creates new targetable options which are displayed at all times.

```lua
exports.ox_target:addGlobalOption(options)
```

* options: `TargetOptions`

### removeGlobalOption <a href="#removeglobaloption" id="removeglobaloption"></a>

Removes all options from the global options list with the option names.

```lua
exports.ox_target:removeGlobalOption(optionNames)
```

* optionNames: `string` or `string[]`

### addGlobalObject <a href="#addglobalobject" id="addglobalobject"></a>

Creates new targetable options for all Object entity types.

```lua
exports.ox_target:addGlobalObject(options)
```

* options: `TargetOptions`

### removeGlobalObject <a href="#removeglobalobject" id="removeglobalobject"></a>

Removes all options from the global Object list with the option names.

```lua
exports.ox_target:removeGlobalObject(optionNames)
```

* optionNames: `string` or `string[]`

### addGlobalPed <a href="#addglobalped" id="addglobalped"></a>

Creates new targetable options for all Ped entity types (excluding players).

```lua
exports.ox_target:addGlobalPed(options)
```

* options: `TargetOptions`

### removeGlobalPed <a href="#removeglobalped" id="removeglobalped"></a>

Removes all options from the global Ped list with the option names.

```lua
exports.ox_target:removeGlobalPed(optionNames)
```

* optionNames: `string` or `string[]`

### addGlobalPlayer <a href="#addglobalplayer" id="addglobalplayer"></a>

Creates new targetable options for all Player entities.

```lua
exports.ox_target:addGlobalPlayer(options)
```

* options: `TargetOptions`

### removeGlobalPlayer <a href="#removeglobalplayer" id="removeglobalplayer"></a>

Removes all options from the global Player list with the option names.

```lua
exports.ox_target:removeGlobalPlayer(optionNames)
```

* optionNames: `string` or `string[]`

### addGlobalVehicle <a href="#addglobalvehicle" id="addglobalvehicle"></a>

Creates new targetable options for all Vehicle entity types.

```lua
exports.ox_target:addGlobalVehicle(options)
```

* options: `TargetOptions`

### removeGlobalVehicle <a href="#removeglobalvehicle" id="removeglobalvehicle"></a>

Removes all options from the global Vehicle list with the option names.

```lua
exports.ox_target:removeGlobalVehicle(optionNames)
```

* optionNames: `string` or `string[]`

### addModel <a href="#addmodel" id="addmodel"></a>

Creates new targetable options for a specific model or list of models.

```lua
exports.ox_target:addModel(models, options)
```

* models: `number` or `string` or `Array<number | string>`
* options: `TargetOptions`

### removeModel <a href="#removemodel" id="removemodel"></a>

Removes all options from the models list with the option names.

```lua
exports.ox_target:removeModel(models, optionNames)
```

* models: `number` or `string` or `Array<number | string>`
* optionNames: `string` or `string[]`

### addEntity <a href="#addentity" id="addentity"></a>

Creates new targetable options for a specific network id or list of network ids (see [NetworkGetNetworkIdFromEntity](https://docs.fivem.net/natives/?_0x9E35DAB6)).

```lua
exports.ox_target:addEntity(netIds, options)
```

* netIds: `number` or `number[]`
* options: `TargetOptions`

### removeEntity <a href="#removeentity" id="removeentity"></a>

Removes all options from the networked entities list with the option names.

```lua
exports.ox_target:removeEntity(netIds, optionNames)
```

* netIds: `number` or `number[]`
* optionNames: `string` or `string[]`

### addLocalEntity <a href="#addlocalentity" id="addlocalentity"></a>

Creates new targetable options for a specific entity handle or list of entity handles.

```lua
exports.ox_target:addLocalEntity(entities, options)
```

* entities: `number` or `number[]`
* options: `TargetOptions`

### removeLocalEntity <a href="#removelocalentity" id="removelocalentity"></a>

Removes all options from the entities list with the option names.

```lua
exports.ox_target:removeLocalEntity(entities, optionNames)
```

* entities: `number` or `number[]`
* optionNames: `string` or `string[]`

### addSphereZone <a href="#addspherezone" id="addspherezone"></a>

Creates a new targetable [sphere zone](https://overextended.dev/ox_lib/Modules/Zones/Shared#libzonessphere).

```lua
exports.ox_target:addSphereZone(parameters)
```

* parameters: `table`
  * coords: `vector3`
  * name?: `string`
    * An optional name to refer to the zone instead of using the `id`.
  * radius?: `number`
  * debug?: `boolean`
  * drawSprite?: `boolean`
    * Draw a sprite at the centroid of the zone. Defaults to `true`.
  * options: `TargetOptions`

Return:

* id: `number`

### addBoxZone <a href="#addboxzone" id="addboxzone"></a>

Creates a new targetable [box zone](https://overextended.dev/ox_lib/Modules/Zones/Shared#libzonesbox).

```lua
exports.ox_target:addBoxZone(parameters)
```

* parameters: `table`
  * coords: `vector3`
  * name?: `string`
    * An optional name to refer to the zone instead of using the `id`.
  * size?: `vector3`
  * rotation?: `number`
  * debug?: `boolean`
  * drawSprite?: `boolean`
    * Draw a sprite at the centroid of the zone. Defaults to `true`.
  * options: `TargetOptions`

Return:

* id: `number`

### addPolyZone <a href="#addpolyzone" id="addpolyzone"></a>

Creates a new targetable [poly zone](https://overextended.dev/ox_lib/Modules/Zones/Shared#libzonespoly).

```lua
exports.ox_target:addPolyZone(parameters)
```

* parameters: `table`
  * points: `vector3[]`
    * An array of 3d points defining the polygon's shape.
  * name?: `string`
    * An optional name to refer to the zone instead of using the `id`.
  * thickness?: `number`
    * The height of the polygon, defaulting to `4`.
  * debug?: `boolean`
  * drawSprite?: `boolean`
    * Draw a sprite at the centroid of the zone. Defaults to `true`.
  * options: `TargetOptions`

Return:

* id: `number`

### removeZone <a href="#removezone" id="removezone"></a>

Removes a targetable zone with the given id (returned by addBoxZone/addSphereZone).

```lua
exports.ox_target:removeZone(id)
```

* id: `number` or `string`
  * The `number` id that is returned by [addSphereZone](https://overextended.dev/ox_target/Functions/Client#addspherezone), [addBoxZone](https://overextended.dev/ox_target/Functions/Client#addboxzone), or [addPolyZone](https://overextended.dev/ox_target/Functions/Client#addpolyzone)\
    **OR**
  * The `string` name given to the zone.

\
