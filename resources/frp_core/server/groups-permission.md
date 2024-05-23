# Groups Permission

The group system structure has been completed with hierarchy. [Group System Commit](https://github.com/Faroeste-Roleplay/frp\_core/commit/aad56d72a22eec15756b4407cdc0ffee329b7d37) You can add a group to a **user only**, which will apply to all of the **user's characters**. \
You can also add a group to a specific **character only**.&#x20;

To check if a user has group permission, use the following methods:&#x20;

### API.AddUserToGroupByName

Add user to group by name

```lua
API.AddUserToGroupByName( userId, groupName )
```

### API.RemoveUserFromGroupByName

Remove user from group by group name

```lua
API.RemoveUserFromGroupByName( userId, groupName )
```

### API.AddCharacterToGroupByName

Add character to group by group name

```lua
API.AddCharacterToGroupByName( charId, groupName )
```

### API.RemoveCharacterFromGroupByName

Remove character from group by group name

```lua
API.RemoveCharacterFromGroupByName( charId, groupName )
```

###

### API.IsPlayerAceAllowedGroup

Returns <mark style="color:green;">`true`</mark> or <mark style="color:red;">`false`</mark> to check permission.

```lua
API.IsPlayerAceAllowedGroup( source, groupName)
```

### API.IsUserAceAllowedGroup

```lua
API.IsUserAceAllowedGroup( userId, groupName )
```

### API.IsCharacterAceAllowedGroup

```lua
API.IsCharacterAceAllowedGroup( charId, groupName )
```

###

### API.IsPlayerAceAllowedGroupFlag

Returns <mark style="color:green;">`true`</mark> or <mark style="color:red;">`false`</mark> to check for additional flags. For example: `API.IsPlayerAceAllowedGroupFlag(1, "law", "workshift")`&#x20;

Returns whether the user is on duty in the specified job.

```lua
API.IsPlayerAceAllowedGroupFlag( source, groupName, groupFlagName )
```

### API.IsUserAceAllowedGroupFlag

```lua
API.IsUserAceAllowedGroupFlag( userId, groupName, groupFlagName )
```

### API.IsCharacterAceAllowedGroupFlag

```lua
API.IsCharacterAceAllowedGroupFlag( charId, groupName, groupFlagName )
```

