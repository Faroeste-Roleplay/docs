---
description: >-
  An experimental framework for RedM. Limited support and breaking changes
  guaranteed.
---

# frp\_core

{% hint style="success" %}
This resource is now in a stable (v1.0) release; breaking changes are unlikely. Documentation is kept updated.
{% endhint %}

### Installation <a href="#installation" id="installation"></a>

1. #### Install all resource dependencies.
   * [oxmysql](https://overextended.dev/oxmysql)
   * [ox\_lib](https://overextended.dev/ox_lib)
   * frp\_creator
   * frp\_spawn\_selector
2.  #### Download a [release](https://github.com/faroeste-roleplay/frp_core/) or build the source code

    ```
    git clone https://github.com/faroeste-roleplay/frp_core.git
    ```
3.  #### Install optional dependencies. <a href="#install-optional-dependencies" id="install-optional-dependencies"></a>

    These resources aren't required but provide additional functionality.

    * [ox\_inventory](https://github.com/Faroeste-Roleplay/ox_inventory)

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

server-side

<pre class="language-lua"><code class="lang-lua"><strong>-- Import the API using Proxy
</strong>local API = Proxy.getInterface("API")

-- Or using Tunnel for client-server communication
local cAPI = Tunnel.getInterface("API")
</code></pre>

client-side

```lua
-- Import the API using Proxy
local cAPI = Proxy.getInterface("API")

-- Or using Tunnel for server-client communication
local API = Tunnel.getInterface("API")
```
