# Table Extensions

The Table Extensions library provides JavaScript-style array and object manipulation methods for Lua tables, making it easier to work with collections of data in a functional programming style.

## Overview

This library extends Lua's built-in table functionality with methods inspired by JavaScript's Array methods. It automatically detects whether a table is array-like or object-like and handles operations accordingly.

## Functions

### `table.filter`

Creates a new table containing only elements that pass the predicate test.

**Parameters:**

* `t` (table): The table to filter
* `predicate` (function): Function that tests each element `predicate(value, key)`

**Returns:** New filtered table

**Behavior:**

* For arrays: Returns array with filtered values
* For objects: Returns object with filtered key-value pairs

**Examples:**

```lua
-- Filter array
local numbers = {1, 2, 3, 4, 5, 6}
local evenNumbers = table.filter(numbers, function(value)
    return value % 2 == 0
end)
-- Result: {2, 4, 6}

-- Filter object
local users = {
    john = {age = 25, active = true},
    jane = {age = 30, active = false},
    bob = {age = 35, active = true}
}
local activeUsers = table.filter(users, function(user)
    return user.active
end)
-- Result: {john = {age = 25, active = true}, bob = {age = 35, active = true}}

-- Using both value and key in predicate
local items = {"apple", "banana", "cherry"}
local firstTwo = table.filter(items, function(value, index)
    return index <= 2
end)
-- Result: {"apple", "banana"}
```

### `table.map`

Creates a new array by transforming each element using the provided function.

**Parameters:**

* `t` (table): The table to map over
* `predicate` (function): Transform function `predicate(value, index, table)`

**Returns:** New array with transformed values

**Note:** Always returns an array regardless of input table type

**Examples:**

```lua
-- Transform numbers
local numbers = {1, 2, 3, 4, 5}
local squares = table.map(numbers, function(value)
    return value * value
end)
-- Result: {1, 4, 9, 16, 25}

-- Transform objects
local users = {
    {name = "John", age = 25},
    {name = "Jane", age = 30}
}
local names = table.map(users, function(user)
    return user.name
end)
-- Result: {"John", "Jane"}

-- Using all parameters
local items = {"apple", "banana", "cherry"}
local indexed = table.map(items, function(value, index, originalTable)
    return index .. ": " .. value
end)
-- Result: {"1: apple", "2: banana", "3: cherry"}
```

### `table.find`

Finds the first element that matches the predicate condition.

**Parameters:**

* `t` (table): The table to search
* `predicate` (function): Test function `predicate(value, index)`

**Returns:** First matching value, or `nil` if not found

**Examples:**

```lua
-- Find in array
local numbers = {10, 20, 30, 40}
local firstOver25 = table.find(numbers, function(value)
    return value > 25
end)
-- Result: 30

-- Find in object array
local users = {
    {id = 1, name = "John", role = "admin"},
    {id = 2, name = "Jane", role = "user"},
    {id = 3, name = "Bob", role = "admin"}
}
local admin = table.find(users, function(user)
    return user.role == "admin"
end)
-- Result: {id = 1, name = "John", role = "admin"}

-- Not found case
local notFound = table.find(numbers, function(value)
    return value > 100
end)
-- Result: nil
```

### `table.findIndex`

Finds the index of the first element that matches the condition.

**Parameters:**

* `t` (table): The table to search
* `callbackFn` (function): Test function `callbackFn(element, index, table)`

**Returns:** Index of first matching element, or `-1` if not found

**Examples:**

```lua
-- Find index in array
local fruits = {"apple", "banana", "cherry", "date"}
local index = table.findIndex(fruits, function(fruit)
    return fruit == "cherry"
end)
-- Result: 3

-- Find index with condition
local numbers = {5, 10, 15, 20}
local index = table.findIndex(numbers, function(num, idx, arr)
    return num > 10
end)
-- Result: 3 (position of 15)

-- Not found
local index = table.findIndex(fruits, function(fruit)
    return fruit == "mango"
end)
-- Result: -1
```

### `table.forEach`

Executes a function for each element in the table (arrays only).

**Parameters:**

* `t` (table): The array to iterate over
* `callbackFn` (function): Function to execute `callbackFn(value, index)`

**Returns:** Nothing (void)

**Note:** Uses `ipairs`, so only works with array-like tables

**Examples:**

```lua
-- Simple iteration
local fruits = {"apple", "banana", "cherry"}
table.forEach(fruits, function(fruit, index)
    print(index .. ": " .. fruit)
end)
-- Output:
-- 1: apple
-- 2: banana  
-- 3: cherry

-- Modify external state
local total = 0
local numbers = {10, 20, 30}
table.forEach(numbers, function(num)
    total = total + num
end)
-- total = 60
```

### `table.merge`

Merges two arrays by appending t2 to t1.

**Parameters:**

* `t1` (table): Target array (modified in place)
* `t2` (table): Source array to append

**Returns:** The modified t1 table

**Note:** This modifies the original t1 table

**Examples:**

```lua
-- Basic merge
local fruits = {"apple", "banana"}
local moreFruits = {"cherry", "date"}
local combined = table.merge(fruits, moreFruits)
-- fruits is now {"apple", "banana", "cherry", "date"}
-- combined === fruits (same table reference)

-- Chaining merges
local numbers1 = {1, 2}
local numbers2 = {3, 4}
local numbers3 = {5, 6}
table.merge(table.merge(numbers1, numbers2), numbers3)
-- numbers1 is now {1, 2, 3, 4, 5, 6}
```

### `table.deepClone`

Creates a deep copy of a table, recursively cloning nested tables.

**Parameters:**

* `tbl` (table): The table to clone

**Returns:** Deep copy of the table

**Note:** Uses `table.clone` internally and recursively clones nested tables

**Examples:**

```lua
-- Simple deep clone
local original = {
    name = "John",
    age = 30,
    address = {
        street = "123 Main St",
        city = "Anytown",
        coordinates = {x = 100, y = 200}
    },
    hobbies = {"reading", "gaming", "cooking"}
}

local cloned = table.deepClone(original)

-- Modify the clone
cloned.name = "Jane"
cloned.address.city = "Other City"
cloned.hobbies[1] = "swimming"

-- Original remains unchanged
print(original.name)           -- "John"
print(original.address.city)   -- "Anytown"  
print(original.hobbies[1])     -- "reading"

-- Clone is modified
print(cloned.name)             -- "Jane"
print(cloned.address.city)     -- "Other City"
print(cloned.hobbies[1])       -- "swimming"
```

## Usage Patterns

### Functional Data Processing

```lua
-- Chain operations for complex data transformations
local users = {
    {name = "John", age = 25, active = true, score = 85},
    {name = "Jane", age = 30, active = false, score = 92},
    {name = "Bob", age = 35, active = true, score = 78},
    {name = "Alice", age = 28, active = true, score = 95}
}

-- Get names of active users with score > 80, sorted
local activeHighScorers = table.map(
    table.filter(users, function(user)
        return user.active and user.score > 80
    end),
    function(user)
        return user.name
    end
)
-- Result: {"John", "Alice"}
```

### Data Validation and Processing

```lua
-- Validate and process form data
local formData = {
    {field = "email", value = "john@example.com", required = true},
    {field = "name", value = "", required = true},
    {field = "age", value = "25", required = false},
    {field = "phone", value = "123-456-7890", required = false}
}

-- Find missing required fields
local missingFields = table.filter(formData, function(field)
    return field.required and (field.value == "" or field.value == nil)
end)

if #missingFields > 0 then
    local fieldNames = table.map(missingFields, function(field)
        return field.field
    end)
    print("Missing required fields: " .. table.concat(fieldNames, ", "))
end
```

### Working with Game Data

```lua
-- Player inventory management
local inventory = {
    {item = "sword", quantity = 1, value = 100},
    {item = "potion", quantity = 5, value = 10},
    {item = "armor", quantity = 1, value = 200},
    {item = "scroll", quantity = 3, value = 25}
}

-- Find valuable items (value > 50)
local valuableItems = table.filter(inventory, function(item)
    return item.value > 50
end)

-- Calculate total inventory value
local totalValue = 0
table.forEach(inventory, function(item)
    totalValue = totalValue + (item.quantity * item.value)
end)

-- Get just item names for display
local itemNames = table.map(inventory, function(item)
    return item.item
end)
```

### Safe Table Operations

```lua
-- Safe deep cloning for configuration
local defaultConfig = {
    graphics = {
        resolution = {width = 1920, height = 1080},
        quality = "high"
    },
    audio = {
        master = 100,
        effects = 80,
        music = 60
    }
}

-- Create user-specific config without affecting default
local userConfig = table.deepClone(defaultConfig)
userConfig.graphics.quality = "medium"
userConfig.audio.master = 75

-- defaultConfig remains unchanged
```

## Performance Notes

1. **Array Detection**: The library uses `table.type(t) == 'array'` to determine if a table should be treated as an array
2. **Memory Usage**: `deepClone` creates completely new tables, which uses more memory but ensures data isolation
3. **Iteration**: `forEach` uses `ipairs` for better performance with arrays
4. **Functional Style**: These methods create new tables rather than modifying existing ones (except `merge`)

## Integration with Framework

These table extensions integrate seamlessly with other framework components:

```lua
-- Use with i18n for language processing
local translations = i18n.exportData()
local availableLanguages = table.map(translations, function(_, lang)
    return lang
end)

-- Use with game systems
local onlinePlayers = GetAllPlayers()
local playerData = table.map(onlinePlayers, function(playerId)
    return {
        id = playerId,
        name = GetPlayerName(playerId),
        ping = GetPlayerPing(playerId)
    }
end)

local lowPingPlayers = table.filter(playerData, function(player)
    return player.ping < 100
end)
```
