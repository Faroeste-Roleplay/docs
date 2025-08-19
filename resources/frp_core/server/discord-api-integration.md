# Discord API Integration

### Overview

The Discord API Integration provides a comprehensive interface for interacting with Discord guild members through the Discord REST API. This system allows you to manage guild members, roles, and nicknames directly from your server application.

### Prerequisites

#### Configuration Requirements

1. **Discord Bot Token**: Set up a Discord application and bot at [Discord Developer Portal](https://discord.com/developers/applications)
2.  **Server Configuration**: Add the bot token to your server configuration:

    ```
    set discord_bot_token "YOUR_BOT_TOKEN_HERE"
    ```
3.  **Guild ID**: Configure your Discord guild ID in the Config table:

    ```lua
    Config.DiscordGuildId = "YOUR_GUILD_ID_HERE"
    ```

#### Bot Permissions

Your Discord bot requires the following permissions in your guild:

* **Manage Roles**: To assign and remove roles
* **Manage Nicknames**: To change member nicknames
* **View Server Members**: To fetch member information

### API Reference

#### API.GetDiscordGuildMember(discordId)

Retrieves detailed information about a specific guild member from Discord.

**Parameters**

* `discordId` (string): The Discord user ID of the member to fetch

**Returns**

* `member` (table): Discord guild member object containing user data, roles, and guild-specific information
* `nil`: If the request fails or member is not found

**Example**

```lua
local member = API.GetDiscordGuildMember("123456789012345678")
if member then
    print("Member nickname:", member.nick)
    print("Member roles count:", #member.roles)
    print("Username:", member.user.username)
else
    print("Member not found or error occurred")
end
```

**Response Structure**

```lua
{
    user = {
        id = "123456789012345678",
        username = "johndoe",
        global_name = "John Doe",
        discriminator = "0001",
        avatar = "avatar_hash"
    },
    nick = "Server Nickname",
    roles = {"role_id_1", "role_id_2"},
    joined_at = "2021-01-01T00:00:00.000000+00:00"
}
```

***

#### API.GetDiscordNameCached(discordId)

Retrieves a cached Discord username for performance optimization.

**Parameters**

* `discordId` (string): The Discord user ID to retrieve cached name for

**Returns**

* `username` (string): The cached username, or empty string if not cached
* `nil`: If discordId is not provided

**Example**

```lua
local cachedName = API.GetDiscordNameCached("123456789012345678")
if cachedName and cachedName ~= "" then
    print("Cached name:", cachedName)
else
    -- Need to fetch from Discord API
    local freshName = API.GetDiscordMemberName("123456789012345678")
end
```

**Notes**

* This function provides instant access to previously fetched usernames
* Returns empty string if the user hasn't been cached yet
* Use this for performance-critical operations

***

#### API.DefineDiscordMemberRole(discordId, roleId)

Assigns a role to a Discord guild member.

**Parameters**

* `discordId` (string): The Discord user ID of the member
* `roleId` (string): The Discord role ID to assign

**Returns**

* `response` (table): Discord API response object
* `nil`: If discordId is not provided

**Example**

```lua
-- Assign VIP role to a member
local vipRoleId = "987654321098765432"
local response = API.DefineDiscordMemberRole("123456789012345678", vipRoleId)
if response then
    print("Role assigned successfully")
else
    print("Failed to assign role")
end
```

**Notes**

* The member must be in the guild
* The bot must have permission to manage roles
* The bot's highest role must be higher than the role being assigned

***

#### API.GetDiscordRolesFromUser(discordId)

Retrieves all roles assigned to a specific guild member.

**Parameters**

* `discordId` (string): The Discord user ID to query

**Returns**

* `roles` (table): Array of role IDs assigned to the user
* `nil`: If discordId is not provided or user not found

**Example**

```lua
local userRoles = API.GetDiscordRolesFromUser("123456789012345678")
if userRoles then
    print("User has", #userRoles, "roles:")
    for i, roleId in ipairs(userRoles) do
        print("Role", i, ":", roleId)
    end
else
    print("Could not fetch user roles")
end
```

***

#### API.DiscordMemberHasRole(discordId, roleId)

Checks if a guild member has a specific role.

**Parameters**

* `discordId` (string): The Discord user ID to check
* `roleId` (string): The Discord role ID to verify

**Returns**

* `hasRole` (boolean): True if the member has the role, false otherwise

**Example**

```lua
local adminRoleId = "111222333444555666"
local isAdmin = API.DiscordMemberHasRole("123456789012345678", adminRoleId)

if isAdmin then
    print("User is an administrator")
    -- Grant admin permissions
else
    print("User is not an administrator")
    -- Restrict access
end
```

**Use Cases**

* Permission verification
* Access control systems
* Role-based feature unlocking

***

#### API.RemoveDiscordMemberRole(discordId, roleId)

Removes a role from a Discord guild member.

**Parameters**

* `discordId` (string): The Discord user ID of the member
* `roleId` (string): The Discord role ID to remove

**Returns**

* `response` (table): Discord API response object
* `nil`: If discordId is not provided

**Example**

```lua
-- Remove temporary role after event ends
local tempRoleId = "999888777666555444"
local response = API.RemoveDiscordMemberRole("123456789012345678", tempRoleId)
if response then
    print("Role removed successfully")
else
    print("Failed to remove role")
end
```

**Notes**

* The member must currently have the role
* The bot must have permission to manage roles
* The bot's highest role must be higher than the role being removed

***

#### API.GetDiscordMemberName(discordId)

Fetches and caches the display name of a Discord guild member.

**Parameters**

* `discordId` (string): The Discord user ID to fetch the name for

**Returns**

* `username` (string): The member's display name (global\_name if available, otherwise username)
* `nil`: If discordId is not provided or member not found

**Example**

```lua
local displayName = API.GetDiscordMemberName("123456789012345678")
if displayName then
    print("Member's display name:", displayName)
    -- Name is now cached for future use
else
    print("Could not fetch member name")
end
```

**Name Priority**

1. **global\_name**: Discord's new display name system
2. **username**: Fallback to traditional username

**Caching Behavior**

* Automatically caches the retrieved name for performance
* Subsequent calls to `GetDiscordNameCached` will return this cached value

***

#### API.DefineDiscordMemberName(discordId, name)

Sets the server nickname for a Discord guild member.

**Parameters**

* `discordId` (string): The Discord user ID of the member
* `name` (string, optional): The new nickname to set. If not provided, uses cached name

**Returns**

* `response` (table): Discord API response object
* `nil`: If discordId is not provided

**Example**

```lua
-- Set a custom nickname for a member
local response = API.DefineDiscordMemberName("123456789012345678", "VIP Player John")
if response then
    print("Nickname updated successfully")
end

-- Reset to cached name (if available)
local response2 = API.DefineDiscordMemberName("123456789012345678")
```

**Notes**

* The bot must have "Manage Nicknames" permission
* Cannot change the nickname of users with higher roles than the bot
* Passing `nil` or empty name uses the cached username

### Usage Examples

#### Role Management System

```lua
-- Create a role-based permission system
local ROLES = {
    ADMIN = "111111111111111111",
    MODERATOR = "222222222222222222",
    VIP = "333333333333333333",
    MEMBER = "444444444444444444"
}

function PromoteToModerator(discordId)
    -- Check if user is already admin
    if API.DiscordMemberHasRole(discordId, ROLES.ADMIN) then
        return false, "User is already an admin"
    end
    
    -- Remove member role and add moderator role
    API.RemoveDiscordMemberRole(discordId, ROLES.MEMBER)
    local success = API.DefineDiscordMemberRole(discordId, ROLES.MODERATOR)
    
    if success then
        return true, "Promoted to moderator successfully"
    else
        return false, "Failed to promote user"
    end
end
```

#### User Information Display

```lua
function GetUserInfo(discordId)
    -- Try to get cached name first for performance
    local name = API.GetDiscordNameCached(discordId)
    
    if not name or name == "" then
        -- Fetch fresh name if not cached
        name = API.GetDiscordMemberName(discordId)
    end
    
    -- Get user's roles
    local roles = API.GetDiscordRolesFromUser(discordId)
    local roleCount = roles and #roles or 0
    
    return {
        name = name or "Unknown",
        roleCount = roleCount,
        isVIP = API.DiscordMemberHasRole(discordId, ROLES.VIP)
    }
end
```

#### Event-Based Role Assignment

```lua
-- Assign roles based on in-game achievements
function OnPlayerAchievement(playerId, achievementType)
    local discordId = GetPlayerDiscordId(playerId) -- Your implementation
    
    if not discordId then return end
    
    local roleId = nil
    local nickname = nil
    
    if achievementType == "first_win" then
        roleId = "555555555555555555" -- Winner role
        nickname = "🏆 " .. API.GetDiscordNameCached(discordId)
    elseif achievementType == "level_50" then
        roleId = "666666666666666666" -- Veteran role
        nickname = "⭐ " .. API.GetDiscordNameCached(discordId)
    end
    
    if roleId then
        API.DefineDiscordMemberRole(discordId, roleId)
    end
    
    if nickname then
        API.DefineDiscordMemberName(discordId, nickname)
    end
end
```

#### Batch Operations

```lua
-- Process multiple users efficiently
function UpdateMultipleUsers(discordIds, roleId, action)
    local results = {}
    
    for i, discordId in ipairs(discordIds) do
        local success = false
        
        if action == "add" then
            success = API.DefineDiscordMemberRole(discordId, roleId) ~= nil
        elseif action == "remove" then
            success = API.RemoveDiscordMemberRole(discordId, roleId) ~= nil
        end
        
        results[discordId] = success
        
        -- Add delay to respect rate limits
        if i % 10 == 0 then
            Citizen.Wait(1000) -- Wait 1 second every 10 requests
        end
    end
    
    return results
end
```

### Best Practices

#### Error Handling

```lua
function SafeRoleCheck(discordId, roleId)
    if not discordId or not roleId then
        print("Invalid parameters provided")
        return false
    end
    
    local success, hasRole = pcall(API.DiscordMemberHasRole, discordId, roleId)
    
    if not success then
        print("Error checking role for user:", discordId)
        return false
    end
    
    return hasRole
end
```

#### Rate Limiting

```lua
-- Implement rate limiting for bulk operations
local rateLimiter = {
    requests = 0,
    resetTime = 0
}

function MakeRateLimitedRequest(requestFunction, ...)
    local currentTime = GetGameTimer()
    
    if currentTime > rateLimiter.resetTime then
        rateLimiter.requests = 0
        rateLimiter.resetTime = currentTime + 60000 -- Reset every minute
    end
    
    if rateLimiter.requests >= 50 then -- Discord rate limit
        print("Rate limit reached, waiting...")
        Citizen.Wait(rateLimiter.resetTime - currentTime)
        rateLimiter.requests = 0
    end
    
    rateLimiter.requests = rateLimiter.requests + 1
    return requestFunction(...)
end
```

#### Caching Strategy

```lua
-- Implement smart caching for frequently accessed data
local cacheExpiry = {}
local CACHE_DURATION = 300000 -- 5 minutes

function GetDiscordNameSmart(discordId)
    local currentTime = GetGameTimer()
    local cachedName = API.GetDiscordNameCached(discordId)
    
    -- Check if cache is expired
    if cachedName ~= "" and cacheExpiry[discordId] and currentTime < cacheExpiry[discordId] then
        return cachedName
    end
    
    -- Fetch fresh data
    local freshName = API.GetDiscordMemberName(discordId)
    if freshName then
        cacheExpiry[discordId] = currentTime + CACHE_DURATION
    end
    
    return freshName
end
```

### Troubleshooting

#### Common Issues

**"Unauthorized" Error (401)**

* Verify your bot token is correct
* Ensure the bot is invited to your Discord guild
* Check that the bot has necessary permissions

**"Forbidden" Error (403)**

* Bot lacks required permissions (Manage Roles, Manage Nicknames)
* Bot's role is lower than the target role in hierarchy
* Bot cannot modify users with higher roles

**"Not Found" Error (404)**

* Discord user ID is invalid
* User is not a member of the specified guild
* Role ID doesn't exist in the guild

**Rate Limiting (429)**

* Too many requests in a short period
* Implement delays between requests
* Use batch operations efficiently

#### Debug Information

```lua
function DebugDiscordAPI(discordId)
    print("=== Discord API Debug ===")
    print("Discord ID:", discordId)
    
    -- Test member fetch
    local member = API.GetDiscordGuildMember(discordId)
    if member then
        print("✅ Member found")
        print("Username:", member.user.username)
        print("Nickname:", member.nick or "None")
        print("Roles count:", #member.roles)
    else
        print("❌ Member not found")
    end
    
    -- Test cached name
    local cachedName = API.GetDiscordNameCached(discordId)
    print("Cached name:", cachedName ~= "" and cachedName or "Not cached")
    
    print("=== End Debug ===")
end
```

#### Configuration Validation

```lua
function ValidateDiscordConfig()
    local token = GetConvar("discord_bot_token", '')
    local guildId = Config.DiscordGuildId
    
    if token == '' then
        print("ERROR: Discord bot token not configured")
        return false
    end
    
    if not guildId or guildId == '' then
        print("ERROR: Discord guild ID not configured")
        return false
    end
    
    print("✅ Discord configuration appears valid")
    return true
end
```

### Security Considerations

1. **Token Security**: Never expose your bot token in client-side code
2. **Permission Validation**: Always verify user permissions before role operations
3. **Input Sanitization**: Validate Discord IDs and role IDs before API calls
4. **Rate Limiting**: Implement proper rate limiting to avoid API abuse
5. **Error Logging**: Log failed requests for security monitoring

### API Limits

* **Rate Limit**: 50 requests per minute per endpoint
* **Burst Limit**: 5 requests per 5 seconds
* **Global Limit**: 50 requests per second across all endpoints
* **Role Hierarchy**: Bots cannot modify users with higher roles
