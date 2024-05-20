# Server

### API Methods

To use API methos on script do you need import the proxy on server-side

```lua
local Proxy = module("frp_lib", "lib/Proxy")
API = Proxy.getInterface("API")
```



### API.GetUsers()

Return all users loggeds instances

```lua
API.GetUsers()
```



```lua
API.DropPlayer(source, reason)
```

```lua
API.Kick(source, reason)
```

```lua
API.GetUserFromUserId(userId)
```

```lua
API.GetUserFromSource(source)
```

```lua
API.GetUserFromCharId(charId)
```

```lua
API.GetUserIdFromCharId(charId)
```

```lua
API.ClearUserFromCache(source, userId)
```
