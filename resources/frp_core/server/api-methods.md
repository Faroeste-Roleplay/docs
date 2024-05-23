# API Methods

## API Methods

To use API methos on script do you need import the proxy on server-side

```lua
local Proxy = module("frp_lib", "lib/Proxy")
API = Proxy.getInterface("API")
```

### API.GetUsers

Return all users loggeds instances

```lua
API.GetUsers()
```

### API.DropPlayer

Drop player from server by source with reason

```lua
API.DropPlayer(source, reason)
```

### API.GetUserFromUserId

Return user instance from persistent userId

```lua
API.GetUserFromUserId(userId)
```

### API.GetUserFromSource

Return user instance from temporary player source

```lua
API.GetUserFromSource(source)
```

### API.GetUserFromCharId

Return user instance from charId active

```lua
API.GetUserFromCharId(charId)
```

### API.GetUserIdFromCharId

Return userId from charId active

```lua
API.GetUserIdFromCharId(charId)
```
