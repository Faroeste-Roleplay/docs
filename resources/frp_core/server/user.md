# User

Theres is a User instance, to get user check [API Methods](user-database-and-utility-api.md)

* user
  * source: `number`
  * id: `number`
  * ipAddress: `string`
  * numMaxSlots: `number`
  * name: `string`
  * identifiers: `array<string>`
  * primaryIdentifier: `string`
  * groups: `array<group>`

### User:GetSource

```lua
User:GetSource()
```

### User:GetId

```lua
User:GetId()
```

### User:GetIpAddress

```lua
User:GetIpAddress()
```

### User:GetIdentifiers

```lua
User:GetIdentifiers()
```

### User:GetCharactersAppearance

```lua
User:GetCharactersAppearance()
```

### User:CreateCharacter

```lua
User:CreateCharacter(
firstName, lastName, birthDate, playerProfileCreation, equippedApparelsByType)
```

### User:DeleteCharacter

```lua
User:DeleteCharacter( charId )
```

### User:GetCharacter

```lua
User:GetCharacter()
```

### User:SetCharacter

```lua
User:SetCharacter( charId )
```

### User:DrawCharacter

```lua
User:DrawCharacter()
```

### User:Notify

```lua
User:Notify( type, text, time )
```

### User:Logout

```lua
User:Logout()
```

### User:Drop

```lua
User:Drop( reason )
```

### User:GetGroups

```lua
User:GetGroups()
```

### User:GetGroupByName

```lua
User:GetGroupByName( groupName )
```

### User:HasGroup

```lua
User:HasGroup( groupName )
```

### User:JoinGroup

Add user temporary a group

```lua
User:JoinGroup( group, addPrincipal )
```

### User:LeaveGroup

```lua
User:LeaveGroup( group )
```

### User:SetGroupFlagState

<pre class="language-lua"><code class="lang-lua"><strong>User:SetGroupFlagState( group, flag, enabled )
</strong></code></pre>

### User:SetGroupFlagEnabled

<pre class="language-lua"><code class="lang-lua"><strong>User:SetGroupFlagEnabled( group, flag )
</strong></code></pre>

### User:SetGroupFlagDisabled

```lua
User:SetGroupFlagDisabled( group, flag )
```

### User:GetEnabledGroupFlags

```lua
User:GetEnabledGroupFlags( group )
```

