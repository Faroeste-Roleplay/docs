# Internationalization (i18n) Library

The i18n library provides comprehensive internationalization support for your RedM framework, allowing you to manage multiple languages and translations across your resources.

## Overview

The i18n system supports:

* Multi-language translation management
* Server and client-side operation
* Automatic language detection
* KVP (Key-Value Pair) storage for persistence
* Dynamic language switching
* Parameterized translations

## Global Variables

* `lang`: Current active language (default: 'en')
* `languageCache`: Cached translation data
* `avalLangs`: Array of available languages
* `IS_SERVER`: Boolean indicating server context
* `RESOURCE_NAME`: Current resource name

## Core Functions

### `i18n.getCurrentLanguage`

Returns the currently active language code.

**Returns:** String representing the current language

**Example:**

```lua
local currentLang = i18n.getCurrentLanguage()
print("Current language: " .. currentLang)  -- Output: "Current language: en"
```

### `i18n.setLang`

Changes the active language.

**Parameters:**

* `language` (string): Language code to switch to

**Example:**

```lua
i18n.setLang('es')  -- Switch to Spanish
```

### `i18n.importData`

Imports translation data for a specific language.

**Parameters:**

* `language` (string): Language code
* `translationData` (table): Translation data structure

**Translation Data Structure:**

```lua
-- Method 1: Simple flat structure
local translations = {
    greeting = "Hello",
    farewell = "Goodbye"
}

-- Method 2: Prefixed structure  
local translations = {
    ui = {
        button_save = "Save",
        button_cancel = "Cancel"
    },
    messages = {
        success = "Operation completed successfully",
        error = "An error occurred"
    }
}
```

**Example:**

```lua
-- Import English translations
i18n.importData('en', {
    ui = {
        welcome = "Welcome to the server!",
        goodbye = "Thank you for playing!"
    },
    info = {
        language_changed = "Language changed successfully"
    }
})

-- Import Spanish translations
i18n.importData('es', {
    ui = {
        welcome = "¡Bienvenido al servidor!",
        goodbye = "¡Gracias por jugar!"
    },
    info = {
        language_changed = "Idioma cambiado exitosamente"
    }
})
```

### `i18n.exportData`

Exports all cached translation data.

**Returns:** Table containing all language data

**Example:**

```lua
local allTranslations = i18n.exportData()
-- Use this for debugging or data migration
```

### `i18n.translate`

Translates a key to the current language with optional parameter formatting.

**Parameters:**

* `key` (string): Translation key in dot notation (prefix.label)
* `...` (variable): Parameters for string formatting

**Returns:** Translated string

**Key Formats:**

* `"prefix.label"` - Nested key access
* `"simple_key"` - Direct key access
* If no key provided, returns all translations for current language

**Parameter Formatting:** Supports both individual parameters and table parameters for string formatting.

**Examples:**

```lua
-- Basic translation
local welcome = i18n.translate('ui.welcome')
print(welcome)  -- Output: "Welcome to the server!"

-- Translation with parameters
i18n.importData('en', {
    messages = {
        player_joined = "Player %s joined the server with ID %d"
    }
})

local message = i18n.translate('messages.player_joined', 'John', 123)
print(message)  -- Output: "Player John joined the server with ID 123"

-- Translation with table parameters
local params = {"Alice", 456}
local message2 = i18n.translate('messages.player_joined', params)
```

## Automatic Features

### Language Persistence

On the client-side, the library automatically:

* Saves language preferences to KVP storage
* Loads saved language on resource start
* Syncs language changes across resources

### FiveM Integration

The library integrates with FiveM's resource system:

* Automatic resource detection
* Cross-resource language synchronization
* Event-driven language updates

## Events

### `FRP:SetLanguage`

Triggered when the language is changed.

**Parameters:**

* `language` (string): New language code

**Example:**

```lua
-- Manually trigger language change
TriggerEvent('FRP:SetLanguage', 'fr')

-- Listen for language changes
AddEventHandler('FRP:SetLanguage', function(newLanguage)
    print("Language changed to: " .. newLanguage)
end)
```

## Error Handling

The library includes robust error handling:

* Returns "Error 502 : no translation available !" when language cache is unavailable
* Falls back to the original key when translation is not found
* Handles missing translation gracefully

**Example:**

```lua
-- If translation doesn't exist
local missing = i18n.translate('nonexistent.key')
print(missing)  -- Output: "nonexistent.key"
```

## Utility Functions

### `mysplit(inputstr, separator)`

Internal utility function for splitting strings (used for parsing dot notation keys).

**Parameters:**

* `inputstr` (string): String to split
* `separator` (string, optional): Separator character (default: whitespace)

**Returns:** Table array of split strings

## Usage Patterns

### Setting Up Translations

```lua
-- 1. Initialize the system
i18n.setup('en')

-- 2. Import translations for multiple languages
local englishTranslations = {
    ui = {
        title = "Game Interface",
        buttons = {
            save = "Save",
            load = "Load",
            exit = "Exit"
        }
    },
    notifications = {
        saved = "Game saved successfully!",
        loaded = "Game loaded successfully!",
        error = "An error occurred: %s"
    }
}

local spanishTranslations = {
    ui = {
        title = "Interfaz del Juego", 
        buttons = {
            save = "Guardar",
            load = "Cargar", 
            exit = "Salir"
        }
    },
    notifications = {
        saved = "¡Juego guardado exitosamente!",
        loaded = "¡Juego cargado exitosamente!",
        error = "Ocurrió un error: %s"
    }
}

i18n.importData('en', englishTranslations)
i18n.importData('es', spanishTranslations)
```

### Using Translations in Code

```lua
-- Basic usage
local title = i18n.translate('ui.title')
local saveButton = i18n.translate('ui.buttons.save')

-- With parameters
local errorMsg = i18n.translate('notifications.error', 'File not found')

-- Dynamic language switching
function changeLanguage(newLang)
    i18n.setLang(newLang)
    TriggerEvent('FRP:SetLanguage', newLang)
    
    -- Update UI elements
    updateInterfaceTexts()
end

function updateInterfaceTexts()
    local title = i18n.translate('ui.title')
    -- Update your UI with the new translations
end
```

### Client-Server Language Sync

```lua
-- Client-side: Request language change
function requestLanguageChange(language)
    TriggerServerEvent('server:changeLanguage', language)
end

-- Server-side: Handle language change
RegisterServerEvent('server:changeLanguage')
AddEventHandler('server:changeLanguage', function(language)
    local src = source
    -- Validate language
    if isValidLanguage(language) then
        TriggerClientEvent('FRP:SetLanguage', src, language)
    end
end)
```

## Best Practices

1.  **Consistent Key Naming**: Use consistent prefixes and naming conventions

    ```lua
    -- Good
    ui.buttons.save
    ui.buttons.cancel
    messages.errors.file_not_found

    -- Avoid
    SaveButton
    cancel_btn
    FileError
    ```
2.  **Parameterized Strings**: Use parameters for dynamic content

    ```lua
    -- Good
    welcome_message = "Welcome %s! You have %d coins."

    -- Avoid hardcoding values
    welcome_john = "Welcome John! You have 100 coins."
    ```
3.  **Fallback Handling**: Always check for translation availability

    ```lua
    local text = i18n.translate('some.key') or 'Default Text'
    ```
4.  **Resource Organization**: Keep translations organized by resource

    ```lua
    -- each resource should import its own translations
    i18n.importData('en', {
        inventory = { ... },
        shops = { ... },
        vehicles = { ... }
    })
    ```
