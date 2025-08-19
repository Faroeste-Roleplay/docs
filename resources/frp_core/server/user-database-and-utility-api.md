# User Database & Utility API

## Overview

The User Database & Utility API provides comprehensive functions for user management, database operations, and cross-resource communication. This system handles user creation, lookups, ban management, and unique identifier generation, utilizing the Tunnel/Proxy pattern for seamless client-server communication.

## Tunnel & Proxy System

The API utilizes a modified vRP-style Tunnel and Proxy system for inter-resource communication:

* **Tunnel**: Provides two-way communication between server and clients with async support
* **Proxy**: Enables function calls between different resources on the same side

### Basic Usage

To enable the library inside of your resource just add @frp\_lib/library/linker.lua as a shared\_script in your fxmanifest.lua file.

```lua
shared_scripts {
    '@frp_lib/library/linker.lua',
}
```

Or if the library is the only shared script you use you can do:

```lua
shared_script '@frp_lib/library/linker.lua'
```

```lua
-- Import the API using Proxy
local API = Proxy.getInterface("API")

-- Or using Tunnel for client-server communication
local cAPI = Tunnel.getInterface("API")
```

## User Creation & Management

### API.CreateUser

Creates a new user account with credentials in the database.

#### Parameters

* `playerId` (number): The player's server source ID
* `mappedIdentifiers` (table): Mapped player identifiers containing all platform identifiers

#### Returns

* `userId` (number): The newly created user ID
* `nil`: If creation failed

#### Example

```lua
local identifiers = GetPlayerIdentifiers(source)
local mappedIdentifiers = MapIdentifiers(identifiers)

local userId = API.CreateUser(source, mappedIdentifiers)
if userId then
    print("User created with ID:", userId)
else
    print("Failed to create user")
end
```

#### What it does

* Creates user record with player name
* Stores all platform credentials (Steam, Discord, License, etc.)
* Integrates with external systems (prime\_api)
* Logs user creation for auditing

#### Database Structure

```sql
-- user table
INSERT INTO user (name) VALUES(?)

-- user_credentials table  
INSERT INTO user_credentials (
    userId, fivem, steam, ip, license, license2, 
    xbl, live, discord
) VALUES(?, ?, ?, ?, ?, ?, ?, ?, ?)
```

***

## User Lookup Functions

### API.GetUserIdentifiersByIdentifier

Retrieves all identifiers for a user by a specific identifier.

#### Parameters

* `identifier` (string): The identifier value to search for
* `identifierKey` (string): The type of identifier ("steam", "discord", "license", etc.)

#### Returns

* `credentials` (table): Complete user credentials record
* `nil`: If no user found

#### Example

```lua
local userCredentials = API.GetUserIdentifiersByIdentifier("steam:110000123456789", "steam")
if userCredentials then
    print("User ID:", userCredentials.userId)
    print("Discord:", userCredentials.discord)
    print("License:", userCredentials.license)
end
```

***

### API.GetUserIdByIdentifier

Gets a user ID by a specific identifier.

#### Parameters

* `identifier` (string): The identifier value to search for
* `identifierKey` (string): The type of identifier

#### Returns

* `userId` (number): The user's database ID
* `nil`: If no user found

#### Example

```lua
local userId = API.GetUserIdByIdentifier("discord:123456789012345678", "discord")
if userId then
    print("Found user ID:", userId)
else
    print("User not found")
end
```

***

### API.GetUserIdByIdentifiers

Gets a user ID using the primary identifier from a list of identifiers.

#### Parameters

* `identifiers` (table): Array of player identifiers
* `name` (string): Player name (currently unused)

**Returns**

* `userId` (number): The user's database ID
* `nil`: If no user found

#### Example

```lua
local identifiers = GetPlayerIdentifiers(source)
local userId = API.GetUserIdByIdentifiers(identifiers, GetPlayerName(source))

if userId then
    print("Existing user found:", userId)
else
    print("New user - needs to be created")
end
```

***

## Character & User Data Retrieval

### API.GetCharacterFromCitizenIdOffline

Retrieves character data by citizen ID from the database (offline lookup).

#### Parameters

* `citizenId` (string): The character's citizen ID

#### Returns

* `character` (table): Complete character database record
* `nil`: If character not found

#### Example

```lua
local character = API.GetCharacterFromCitizenIdOffline("A1234")
if character then
    print("Character:", character.firstName, character.lastName)
    print("User ID:", character.userId)
    print("Birth Date:", character.birthDate)
end
```

***

### API.GetCharacterFromCharId

Retrieves character data by character ID from the database.

#### Parameters

* `charId` (number): The character's database ID

#### Returns

* `character` (table): Complete character database record
* `nil`: If character not found

#### Example

```lua
local character = API.GetCharacterFromCharId(456)
if character then
    print("Character found:", character.citizenId)
end
```

***

### API.GetUserFromUserIdOffline

Retrieves user data by user ID from the database (offline lookup).

#### Parameters

* `userId` (number): The user's database ID

#### Returns

* `user` (table): Complete user database record
* `nil`: If user not found

#### Example

```lua
local userData = API.GetUserFromUserIdOffline(123)
if userData then
    print("User name:", userData.name)
    print("Character slots:", userData.numCharSlots)
    print("Created at:", userData.createdAt)
end
```

***

### API.GetUserCharactersOffline

Retrieves all characters for a user (offline lookup).

#### Parameters

* `userId` (number): The user's database ID

#### Returns

* `characters` (table): Array of character records
* `{}`: Empty array if no characters found

#### Example

```lua
local characters = API.GetUserCharactersOffline(123)
print("User has", #characters, "characters:")

for i, char in ipairs(characters) do
    print("Character", i, ":", char.firstName, char.lastName, "(" .. char.citizenId .. ")")
end
```

***

## Active User Management

### API.GetUserFromUserId

Gets an active user object by user ID.

#### Parameters

* `userId` (number): The user's database ID

#### Returns

* `User` (table): The active user object
* `nil`: If user is not online

#### Example

```lua
local user = API.GetUserFromUserId(123)
if user then
    print("User is online:", user:GetName())
    user:Notify("info", "Admin message!")
else
    print("User is not online")
end
```

***

### API.GetUserIdFromServerId

Gets a user ID from a server source ID.

#### Parameters

* `source` (number): The player's server source ID

#### Returns

* `userId` (number): The user's database ID
* `nil`: If user not found

#### Example

```lua
local userId = API.GetUserIdFromServerId(source)
if userId then
    print("Player source", source, "is user ID", userId)
end
```

***

### API.GetUserFromSource

Gets an active user object by server source ID.

#### Parameters

* `source` (number): The player's server source ID

#### Returns

* `User` (table): The active user object
* `nil`: If user not found

#### Example

```lua
-- Common usage in commands
RegisterCommand("balance", function(source, args)
    local user = API.GetUserFromSource(source)
    if not user then
        return
    end
    
    local character = user:GetCharacter()
    if character then
        local balance = character:GetBankBalance()
        user:Notify("info", "Balance: $" .. balance)
    end
end)
```

***

### API.GetUserFromCharId

Gets an active user object by character ID.

#### Parameters

* `charId` (number): The character's database ID

#### Returns

* `User` (table): The active user object
* `nil`: If user not online or character not found

#### Example

```lua
local user = API.GetUserFromCharId(789)
if user then
    print("Character owner is online:", user:GetName())
end
```

***

### API.GetUserFromCitizenId

Gets an active user object by citizen ID.

#### Parameters

* `citizenId` (string): The character's citizen ID

#### Returns

* `User` (table): The active user object
* `nil`: If user not online or character not found

#### Example

```lua
local user = API.GetUserFromCitizenId("A1234")
if user then
    local character = user:GetCharacter()
    print("Character is online:", character:GetFullName())
end
```

***

### API.GetUserFromDiscordId

Gets an active user object by Discord ID.

#### Parameters

* `discordId` (string): The user's Discord ID (without "discord:" prefix)

#### Returns

* `User` (table): The active user object
* `nil`: If user not online

#### Example

```lua
local discordId = "123456789012345678"
local user = API.GetUserFromDiscordId(discordId)

if user then
    print("Discord user is online as:", user:GetName())
    user:Notify("info", "You have a Discord message!")
end
```

***

### API.GetUserIdFromCharId

Gets a user ID from a character ID (with fallback to database).

#### Parameters

* `charId` (number): The character's database ID

#### Returns

* `userId` (number): The user's database ID
* `nil`: If character not found

#### Example

```lua
local userId = API.GetUserIdFromCharId(456)
if userId then
    print("Character 456 belongs to user:", userId)
end
```

***

### API.GetUsers

Gets all currently active user objects.

#### Returns

* `users` (table): Table of all active users indexed by user ID

#### Example

```lua
local allUsers = API.GetUsers()
local userCount = 0

for userId, user in pairs(allUsers) do
    userCount = userCount + 1
    print("User", userId, ":", user:GetName())
end

print("Total online users:", userCount)
```

***

## Ban Management System

### API.SetBanned

Bans a user and kicks them from the server.

#### Parameters

* `source` (number): The player's server source ID (for kicking)
* `userId` (number): The user's database ID to ban
* `reason` (string): Reason for the ban

#### Returns

* None

#### Example

```lua
RegisterCommand("ban", function(source, args)
    if not CheckAdminPermission(source) then return end
    
    local targetId = tonumber(args[1])
    local reason = table.concat(args, " ", 2) or "No reason provided"
    
    local targetUser = API.GetUserFromSource(targetId)
    if targetUser then
        API.SetBanned(targetId, targetUser:GetId(), reason)
        print("Banned user:", targetUser:GetName(), "Reason:", reason)
    end
end)
```

***

### API.UnBan

Removes a ban from a user account.

#### Parameters

* `source` (number): Command source (not used currently)
* `userId` (number): The user's database ID to unban

#### Returns

* None

#### Example

```lua
RegisterCommand("unban", function(source, args)
    if not CheckAdminPermission(source) then return end
    
    local userId = tonumber(args[1])
    if userId then
        API.UnBan(source, userId)
        print("Unbanned user ID:", userId)
    end
end)
```

***

### API.IsBanned

Checks if a user is currently banned.

#### Parameters

* `userId` (number): The user's database ID to check

#### Returns

* `banned` (boolean): True if user is banned, false otherwise

#### Example

```lua
-- Check during connection
AddEventHandler('playerConnecting', function(name, setKickReason)
    local source = source
    local identifiers = GetPlayerIdentifiers(source)
    local userId = API.GetUserIdByIdentifiers(identifiers, name)
    
    if userId and API.IsBanned(userId) then
        setKickReason("You are banned from this server")
        CancelEvent()
    end
end)
```

***

## Cache Management

### API.ClearUserFromCache

Removes a user from all caches and tracking tables.

#### Parameters

* `source` (number): The player's server source ID
* `userId` (number): The user's database ID

#### Returns

* None

#### Example

```lua
-- Automatic cleanup when player disconnects
AddEventHandler('playerDropped', function(reason)
    local source = source
    local user = API.GetUserFromSource(source)
    
    if user then
        API.ClearUserFromCache(source, user:GetId())
        print("Cleaned up user cache for:", user:GetName())
    end
end)
```

#### Console Command

```
clearUser <userId>
```

Clears a specific user from cache (console only).

***

## Unique ID Generation

### API.CreateCitizenId

Generates a unique citizen ID for new characters.

#### Returns

* `citizenId` (string): A unique citizen ID (format: A1234)

#### Example

```lua
local citizenId = API.CreateCitizenId()
print("Generated citizen ID:", citizenId) -- Example: "A1234"
```

#### Algorithm

* Combines random string (1 char) + random number (4 digits)
* Ensures uniqueness by checking against existing characters
* Uppercase format for consistency

***

### API.GenerateCharFingerPrint

Generates a unique fingerprint for new characters.

#### Returns

* `fingerprint` (string): A unique fingerprint string

#### Example

```lua
local fingerprint = API.GenerateCharFingerPrint()
print("Generated fingerprint:", fingerprint) -- Example: "AB123C45DEF6789"
```

#### Algorithm

* Complex pattern: String(2) + Number(3) + String(1) + Number(2) + String(3) + Number(4)
* Stored in character metadata as JSON
* Ensures uniqueness against existing character fingerprints

***

## Usage Examples

### Complete User Connection Flow

```lua
AddEventHandler('playerConnecting', function(name, setKickReason, deferrals)
    local source = source
    local identifiers = GetPlayerIdentifiers(source)
    
    deferrals.defer()
    deferrals.update("Checking user data...")
    
    -- Check if user exists
    local userId = API.GetUserIdByIdentifiers(identifiers, name)
    
    if not userId then
        -- Create new user
        deferrals.update("Creating new user...")
        local mappedIdentifiers = MapIdentifiers(identifiers)
        userId = API.CreateUser(source, mappedIdentifiers)
        
        if not userId then
            deferrals.done("Failed to create user account")
            return
        end
    else
        -- Check if banned
        if API.IsBanned(userId) then
            deferrals.done("You are banned from this server")
            return
        end
    end
    
    deferrals.done()
end)
```

### Admin User Management Commands

```lua
RegisterCommand("userinfo", function(source, args)
    if not CheckAdminPermission(source) then return end
    
    local targetId = tonumber(args[1])
    if not targetId then return end
    
    -- Try to get online user first
    local user = API.GetUserFromSource(targetId)
    if user then
        print("=== ONLINE USER INFO ===")
        print("Name:", user:GetName())
        print("User ID:", user:GetId())
        print("Character ID:", user:GetCharacterId())
        print("IP:", user:GetIpAddress())
        return
    end
    
    -- Get offline data
    local userId = API.GetUserIdFromServerId(targetId)
    if userId then
        local userData = API.GetUserFromUserIdOffline(userId)
        if userData then
            print("=== OFFLINE USER INFO ===")
            print("Name:", userData.name)
            print("User ID:", userData.id)
            print("Created:", userData.createdAt)
        end
    end
end)

RegisterCommand("finduser", function(source, args)
    if not CheckAdminPermission(source) then return end
    
    local searchType = args[1] -- "steam", "discord", "citizen"
    local searchValue = args[2]
    
    if not searchType or not searchValue then
        print("Usage: finduser <type> <value>")
        print("Types: steam, discord, citizen, char")
        return
    end
    
    local user = nil
    
    if searchType == "steam" then
        user = API.GetUserFromSource(API.GetUserIdByIdentifier(searchValue, "steam"))
    elseif searchType == "discord" then
        local discordId = searchValue:gsub("discord:", "")
        user = API.GetUserFromDiscordId(discordId)
    elseif searchType == "citizen" then
        user = API.GetUserFromCitizenId(searchValue)
    elseif searchType == "char" then
        local charId = tonumber(searchValue)
        user = API.GetUserFromCharId(charId)
    end
    
    if user then
        print("Found user:", user:GetName(), "ID:", user:GetId())
    else
        print("User not found or not online")
    end
end)
```

### Character Management Integration

```lua
-- When creating a character
function CreateCharacter(source, characterData)
    local user = API.GetUserFromSource(source)
    if not user then return false end
    
    -- Generate unique IDs
    local citizenId = API.CreateCitizenId()
    local fingerprint = API.GenerateCharFingerPrint()
    
    -- Add to metadata
    characterData.metadata = characterData.metadata or {}
    characterData.metadata.fingerprint = fingerprint
    
    -- Create character with unique citizen ID
    local charId = user:CreateCharacter(
        characterData.firstName,
        characterData.lastName,
        characterData.birthDate,
        characterData.appearance,
        characterData.clothing,
        characterData.metadata
    )
    
    return charId
end

-- Cross-character messaging
RegisterCommand("message", function(source, args)
    local user = API.GetUserFromSource(source)
    if not user then return end
    
    local targetCitizenId = args[1]
    local message = table.concat(args, " ", 2)
    
    local targetUser = API.GetUserFromCitizenId(targetCitizenId)
    if targetUser then
        targetUser:Notify("info", "Message from " .. user:GetCharacter():GetFullName() .. ": " .. message)
        user:Notify("success", "Message sent to " .. targetCitizenId)
    else
        user:Notify("error", "Character not found or not online")
    end
end)
```

### Ban Management System

```lua
RegisterCommand("ban", function(source, args)
    if not CheckAdminPermission(source) then return end
    
    local targetId = tonumber(args[1])
    local reason = table.concat(args, " ", 2) or "Violation of server rules"
    
    local targetUser = API.GetUserFromSource(targetId)
    if targetUser then
        local userId = targetUser:GetId()
        local userName = targetUser:GetName()
        
        API.SetBanned(targetId, userId, reason)
        
        -- Log the ban
        local adminUser = API.GetUserFromSource(source)
        local adminName = adminUser and adminUser:GetName() or "Console"
        
        print(("BAN: %s (ID: %s) banned %s (ID: %s) - Reason: %s"):format(
            adminName, source, userName, userId, reason
        ))
        
        -- Notify other admins
        local allUsers = API.GetUsers()
        for _, user in pairs(allUsers) do
            if user:HasGroup("admin") then
                user:Notify("warning", userName .. " was banned by " .. adminName)
            end
        end
    else
        print("Target player not found")
    end
end)

RegisterCommand("checkban", function(source, args)
    if not CheckAdminPermission(source) then return end
    
    local targetId = tonumber(args[1])
    
    -- Try multiple methods to find user ID
    local userId = nil
    
    -- Method 1: Online player
    local targetUser = API.GetUserFromSource(targetId)
    if targetUser then
        userId = targetUser:GetId()
    else
        -- Method 2: Use server ID to get user ID
        userId = API.GetUserIdFromServerId(targetId)
    end
    
    if userId then
        local isBanned = API.IsBanned(userId)
        print("User ID", userId, "ban status:", isBanned and "BANNED" or "NOT BANNED")
    else
        print("Could not find user ID for target:", targetId)
    end
end)
```

### Performance Monitoring

```lua
-- Monitor cache sizes
RegisterCommand("cachestats", function(source, args)
    if source ~= 0 then return end -- Console only
    
    local allUsers = API.GetUsers()
    local activeUsers = 0
    local loggedInUsers = 0
    
    for userId, user in pairs(allUsers) do
        activeUsers = activeUsers + 1
        if user:GetCharacter() then
            loggedInUsers = loggedInUsers + 1
        end
    end
    
    print("=== CACHE STATISTICS ===")
    print("Active Users:", activeUsers)
    print("Logged In Users:", loggedInUsers)
    print("Memory Usage: ~" .. (activeUsers * 50) .. "KB estimated")
end)

-- Cleanup disconnected users
CreateThread(function()
    while true do
        Wait(300000) -- 5 minutes
        
        local allUsers = API.GetUsers()
        local cleanupCount = 0
        
        for userId, user in pairs(allUsers) do
            local source = user:GetSource()
            if GetPlayerName(source) == nil then
                -- Player disconnected but not cleaned up
                API.ClearUserFromCache(source, userId)
                cleanupCount = cleanupCount + 1
            end
        end
        
        if cleanupCount > 0 then
            print("Cleaned up", cleanupCount, "disconnected users from cache")
        end
    end
end)
```

## Tunnel/Proxy Integration Examples

### Server-Side API Export

```lua
-- In your main server file
local API = {}

-- Define all your API functions here...

-- Export via Proxy for other server resources
Proxy.addInterface("frp_core", API)

-- Export via Tunnel for client communication  
Tunnel.bindInterface("frp_core", API)
```

### Client-Side Usage

```lua
-- Client-side script accessing server API
local Tunnel = module("lib/Tunnel")
local API = Tunnel.getInterface("frp_core", "your_resource_name")

-- Now you can call server functions from client
RegisterCommand("mybalance", function()
    local userId = API.GetUserIdFromServerId(GetPlayerServerId(PlayerId()))
    if userId then
        -- This calls the server and waits for response
        print("My user ID is:", userId)
    end
end)
```

### Cross-Resource Server Usage

```lua
-- In another server resource
local Proxy = module("lib/Proxy")
local API = Proxy.getInterface("frp_core", GetCurrentResourceName())

-- Use API functions from another resource
RegisterCommand("getuser", function(source, args)
    local user = API.GetUserFromSource(source)
    if user then
        print("Found user:", user.GetName()) -- Note: Proxy calls return values directly
    end
end)
```

## Best Practices

### Error Handling

```lua
function SafeUserLookup(source)
    local success, user = pcall(API.GetUserFromSource, source)
    if not success then
        print("Error getting user for source", source, ":", user)
        return nil
    end
    return user
end
```

### Performance Optimization

```lua
-- Cache frequently accessed data
local userIdCache = {}

function GetCachedUserId(source)
    if userIdCache[source] then
        return userIdCache[source]
    end
    
    local userId = API.GetUserIdFromServerId(source)
    if userId then
        userIdCache[source] = userId
        
        -- Clear cache when player disconnects
        local cleanup = function()
            userIdCache[source] = nil
        end
        
        -- Set cleanup timer
        SetTimeout(600000, cleanup) -- 10 minutes
    end
    
    return userId
end
```

### Database Safety

```lua
function SafeDatabaseOperation(operation, ...)
    local success, result = pcall(operation, ...)
    if not success then
        print("Database operation failed:", result)
        return nil
    end
    return result
end

-- Usage
local user = SafeDatabaseOperation(API.GetUserFromUserIdOffline, 123)
```

## Security Considerations

1. **Input Validation**: Always validate user inputs before database queries
2. **Rate Limiting**: Implement rate limits for user creation and lookups
3. **Permission Checks**: Verify permissions before sensitive operations
4. **Audit Logging**: Log all user management operations
5. **Cache Management**: Regularly clean up disconnected users from memory

This comprehensive API provides robust user management capabilities with efficient caching, cross-resource communication, and database safety features.
