# Character

Theres is a Character instance, to get user check in [User Instance](https://faroeste.gitbook.io/docs/resources/frp\_core/server/user#user-getcharacter)

* ### character
  * id: `number`
  * firstName: `string`
  * lastName: `string`
  * birthDate: `string`
  * favoriteReserveType: `number`
  * deathState: `string`
  * metaData: `object`
  * outfitId: `number`
  * session: `object`

### Character:GetSource

```lua
Character:GetSource()
```

### Character:GetId

```lua
Character:GetId()
```

### Character:GetUserId

```lua
Character:GetUserId()
```

### Character:GetInventory

```lua
Character:GetInventory()
```

### Character:GetFullName

```lua
Character:GetFullName()
```

### Character:GetBirthDate

```lua
Character:GetBirthDate()
```

### Character:GetMetadata

```lua
Character:GetMetadata( key )
```

### Character:SetMetadata

```lua
Character:SetMetadata( key, value )
```

### Character:SavePosition

```lua
Character:SavePosition( position )
```

### Character:GetLastPosition

```lua
Character:GetLastPosition()
```

### Character:SetCharacterAppearance

```lua
Character:SetCharacterAppearance( characterAppearance )
```

### Character:SetCharacterAppearanceCustomizable

```lua
Character:SetCharacterAppearanceCustomizable( characterAppearanceCustomizable )
```

### Character:GetCurrentOutfit

```lua
Character:GetCurrentOutfit()
```

### Character:SetCurrentOutfit

```lua
Character:SetCurrentOutfit( outfitId )
```

### Character:CreateCharacterOutfit

```lua
Character:CreateCharacterOutfit( outfitData, name )
```

### Character:UpdateCharacterOutfitData

```lua
Character:UpdateCharacterOutfitData( outfitId, outfitData )
```

### Character:SetCharacterAppearanceOverlays

```lua
Character:SetCharacterAppearanceOverlays( characterAppearanceOverlays )
```

### Character:SetCharacterAppearanceOverlaysCustomizable

<pre class="language-lua"><code class="lang-lua"><strong>Character:SetCharacterAppearanceOverlaysCustomizable( characterAppearanceOverlays  )
</strong></code></pre>

### Character:SetCharacterExpessions

<pre class="language-lua"><code class="lang-lua"><strong>Character:SetCharacterExpessions( expressions )
</strong></code></pre>

### Character:SetCharacterWhistle

```lua
Character:SetCharacterWhistle( whistle )
```

### Character:GetAppearance

```lua
Character:GetAppearance()
```

### Character:SetGameAppearance

```lua
Character:SetGameAppearance()
```

### Character:Release

```lua
Character:Release()
```

### Character:SetSessionVar

<pre class="language-lua"><code class="lang-lua"><strong>Character:SetSessionVar( key, value )
</strong></code></pre>

### Character:GetSessionVar

```lua
Character:GetSessionVar( key )
```
