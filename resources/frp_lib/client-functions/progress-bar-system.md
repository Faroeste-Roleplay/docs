# Progress Bar System

### Progressbar

Creates a progress bar with customizable options and callbacks.

**Parameters:**

* `name` (`string`) - Unique identifier for the progress bar
* `label` (`string`) - Display text shown during progress
* `duration` (`number`) - Duration in milliseconds
* `useWhileDead` (`boolean`) - Whether the progress bar works while dead
* `canCancel` (`boolean`) - Whether the player can cancel the action
* `disableControls` (`table`) - Controls to disable during progress
* `animation` (`table`) - Animation configuration
* `prop` (`table`) - Primary prop configuration
* `propTwo` (`table`) - Secondary prop configuration
* `onFinish` (`function`) - Callback when progress completes successfully
* `onCancel` (`function`) - Callback when progress is cancelled

### Basic Usage

```lua
Progressbar(
    "drinking_coffee",           -- Unique name
    "Drinking coffee...",        -- Display label
    5000,                       -- 5 seconds
    false,                      -- Don't use while dead
    true,                       -- Can be cancelled
    {                          -- Disable controls
        disableMovement = true,
        disableCarMovement = true,
        disableMouse = false,
        disableCombat = true,
    },
    {},                        -- No animation
    {},                        -- No props
    {},                        -- No secondary prop
    function()                 -- On completion
        print("Coffee finished!")
        -- Add health or whatever
    end,
    function()                 -- On cancel
        print("Coffee drinking cancelled!")
    end
)
```

### Advanced Usage with Animation and Props

```lua
Progressbar(
    "repairing_vehicle",
    "Repairing vehicle engine...",
    15000,  -- 15 seconds
    false,
    true,
    {
        disableMovement = true,
        disableCarMovement = true,
        disableMouse = false,
        disableCombat = true,
    },
    {
        type = "anim",
        dict = "mini@repair",
        lib = "fixing_a_ped",
        flag = 16,
    },
    {
        model = "prop_tool_wrench",
        bone = 57005,
        coords = vector3(0.09, 0.0, -0.02),
        rotation = vector3(-78.0, 13.0, 28.0),
    },
    {},  -- No second prop
    function()
        -- Repair completed
        TriggerServerEvent('vehicle:repair:complete')
        exports['mythic_notify']:DoNotification({
            text = 'Vehicle repaired successfully!',
            type = 'success'
        })
    end,
    function()
        -- Repair cancelled
        exports['mythic_notify']:DoNotification({
            text = 'Repair cancelled',
            type = 'error'
        })
    end
)
```

### Real-world Examples

#### Medical Treatment

```lua
Progressbar(
    "medical_treatment",
    "Applying bandages...",
    8000,
    false,
    true,
    { disableMovement = true, disableCombat = true },
    {
        type = "anim",
        dict = "amb@world_human_clipboard@male@idle_a",
        lib = "idle_c",
        flag = 49,
    },
    {
        model = "prop_ld_health_pack",
        bone = 18905,
        coords = vector3(0.13, 0.05, 0.05),
        rotation = vector3(-50.0, 290.0, 0.0),
    },
    {},
    function()
        -- Heal player
        TriggerEvent('medical:heal', 50)
    end,
    function()
        -- Treatment cancelled
        TriggerEvent('medical:cancel')
    end
)
```

#### Crafting Item

```lua
Progressbar(
    "crafting_weapon",
    "Crafting weapon...",
    20000,
    false,
    true,
    { disableMovement = true },
    {
        type = "anim",
        dict = "anim@amb@clubhouse@tutorial@bkr_tut_ig3@",
        lib = "machinic_loop_mechandplayer",
        flag = 1,
    },
    {},
    {},
    function()
        TriggerServerEvent('crafting:complete', 'weapon_pistol')
    end,
    nil  -- No cancel callback needed
)
```

## Best Practices

### Progress Bars

* Provide clear, descriptive labels
* Use appropriate durations for realism
* Always handle both completion and cancellation
* Disable relevant controls during progress
* Use animations and props for immersion
