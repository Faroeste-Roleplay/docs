# Random Generation

### RandomStr

Generates a random string containing uppercase letters, lowercase letters.

**Parameters:**

* `length` (`number`) - Length of the string to generate

**Returns:** `string` - Random alphanumeric string

**Character Set:** A-Z, a-z (62 characters total)

```lua
local randomId = RandomStr(8)
print(randomId) -- Example: "aBc3XyZ1"

local sessionToken = RandomStr(32)
-- Generates a 32-character random string
```

### RandomInt

Generates a random numeric string.

**Parameters:**

* `length` (`number`) - Length of the numeric string to generate

**Returns:** `string` - Random numeric string

**Character Set:** 0-9 (10 characters total)

```lua
local randomCode = RandomInt(6)
print(randomCode) -- Example: "482759"

local playerId = RandomInt(10)
-- Generates a 10-digit random number as string
```

**Use Cases:**

* Unique identifiers
* Session tokens
* Random passwords
* Database keys
* Temporary file names

***

## Best Practices

### Random Generation

* Use `RandomStr` for user-facing identifiers
* Use `RandomInt` for numeric codes
* Consider the security implications for sensitive data
