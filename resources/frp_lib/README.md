# frp\_lib

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
