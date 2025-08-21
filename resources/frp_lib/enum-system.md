# Enum System

Creates a bidirectional enumeration that maps both keys to values and values to keys.

**Parameters:**

* `name` (`string`) - The global name for the enum

**Returns:** `function` - A function that accepts the enum definition

**Usage:**

```lua
enum 'eTriggerType' {
    Invalid = 1,
    Incapacitate = 2,
    Wound = 3,
    Die = 4,
    Revive = 5,
    ReviveByCommand = 6,
    Respawn = 7,
    Heal = 8,
}

-- Usage examples
print(eTriggerType.Wound)        -- Output: 3
print(eTriggerType[3])           -- Output: "Wound"
print(eTriggerType.Invalid)      -- Output: 1
print(eTriggerType[1])           -- Output: "Invalid"
```

**Benefits:**

* Bidirectional mapping (key→value and value→key)
* Type safety for state management
* Clear, readable code
* Easy debugging with reverse lookups

***

## Best Practices

### Enum Usage

* Use meaningful names for enum values
* Keep enum values sequential for debugging
* Use enums for state machines and game modes
