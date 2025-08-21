# DeferralCards (Server)

The DeferralCards library provides a comprehensive API for creating Adaptive Cards in Lua. Adaptive Cards are a platform-agnostic way to create rich, interactive content that can be rendered across various applications and platforms.

## Overview

The library is structured into five main modules:

* `Card`: Core card creation
* `CardElement`: Basic content elements
* `Container`: Layout and grouping elements
* `Action`: Interactive elements
* `Input`: Form input elements

## Installation

The library exports itself as a module:

```lua
local DeferralCards = exports('DeferralCards')()
```

## Card Module

### `DeferralCards.Card:Create`

Creates a complete Adaptive Card with the specified options.

**Parameters:**

* `options` (table): Card configuration options

**Default Properties:**

* `type`: "AdaptiveCard"
* `version`: "1.4"
* `body`: {} (empty array)
* `$schema`: "http://adaptivecards.io/schemas/adaptive-card.json"

**Returns:** JSON-encoded string representing the card

**Example:**

```lua
local card = DeferralCards.Card:Create({
    body = {
        DeferralCards.CardElement:TextBlock({
            text = "Welcome to our service!",
            size = "large",
            weight = "bolder"
        }),
        DeferralCards.CardElement:Image({
            url = "https://example.com/logo.png",
            size = "medium"
        })
    }
})
```

## CardElement Module

### `DeferralCards.CardElement:TextBlock`

Creates a text block element for displaying formatted text.

**Parameters:**

* `options` (table): TextBlock configuration

**Default Properties:**

* `type`: "TextBlock"
* `text`: "Text"

**Common Options:**

* `text` (string): The text content
* `size` (string): "small", "default", "medium", "large", "extraLarge"
* `weight` (string): "lighter", "default", "bolder"
* `color` (string): "default", "dark", "light", "accent", "good", "warning", "attention"
* `wrap` (boolean): Whether text should wrap
* `horizontalAlignment` (string): "left", "center", "right"

**Example:**

```lua
local textBlock = DeferralCards.CardElement:TextBlock({
    text = "Important Notice",
    size = "large",
    weight = "bolder",
    color = "attention",
    horizontalAlignment = "center"
})
```

### `DeferralCards.CardElement:Image`

Creates an image element.

**Parameters:**

* `options` (table): Image configuration

**Default Properties:**

* `type`: "Image"
* `url`: "https://via.placeholder.com/100x100?text=Temp+Image"

**Common Options:**

* `url` (string): Image URL
* `size` (string): "auto", "stretch", "small", "medium", "large"
* `horizontalAlignment` (string): "left", "center", "right"
* `altText` (string): Alternative text for accessibility

**Example:**

```lua
local image = DeferralCards.CardElement:Image({
    url = "https://example.com/product.jpg",
    size = "medium",
    altText = "Product image"
})
```

### `DeferralCards.CardElement:Media`

Creates a media element for video/audio content.

**Parameters:**

* `options` (table): Media configuration

**Default Properties:**

* `type`: "Media"
* `sources`: {} (empty array)

**Example:**

```lua
local media = DeferralCards.CardElement:Media({
    poster = "https://example.com/video-poster.jpg",
    sources = {
        DeferralCards.CardElement:MediaSource({
            mimeType = "video/mp4",
            url = "https://example.com/video.mp4"
        })
    }
})
```

### `DeferralCards.CardElement:MediaSource`

Creates a media source for use within Media elements.

**Parameters:**

* `options` (table): MediaSource configuration

**Default Properties:**

* `mimeType`: "video/mp4"
* `url`: ""

### `DeferralCards.CardElement:RichTextBlock`

Creates a rich text block with inline formatting.

**Parameters:**

* `options` (table): RichTextBlock configuration

**Default Properties:**

* `type`: "RichTextBlock"
* `inline`: {} (empty array)

**Example:**

```lua
local richText = DeferralCards.CardElement:RichTextBlock({
    horizontalAlignment = "center",
    inline = {
        DeferralCards.CardElement:RichTextBlockItem({
            text = "Bold text ",
            weight = "bolder"
        }),
        DeferralCards.CardElement:RichTextBlockItem({
            text = "with color",
            color = "accent"
        })
    }
})
```

### `DeferralCards.CardElement:RichTextBlockItem`

Creates an inline text run for use within RichTextBlock.

**Parameters:**

* `options` (table): TextRun configuration

**Default Properties:**

* `type`: "TextRun"
* `text`: "Text"

**Common Options:**

* `text` (string): The text content
* `color` (string): Text color
* `fontType` (string): "default", "monospace"
* `highlight` (boolean): Whether to highlight
* `italic` (boolean): Whether to italicize
* `strikethrough` (boolean): Whether to strike through
* `weight` (string): Font weight

## Container Module

### `DeferralCards.Container:Create`

Creates a basic container for grouping elements.

**Parameters:**

* `options` (table): Container configuration

**Default Properties:**

* `type`: "Container"
* `items`: {} (empty array)

**Example:**

```lua
local container = DeferralCards.Container:Create({
    items = {
        DeferralCards.CardElement:TextBlock({ text = "Header" }),
        DeferralCards.CardElement:TextBlock({ text = "Content" })
    }
})
```

### `DeferralCards.Container:ActionSet`

Creates a container for action buttons.

**Parameters:**

* `options` (table): ActionSet configuration

**Default Properties:**

* `type`: "ActionSet"
* `actions`: {} (empty array)

**Example:**

```lua
local actionSet = DeferralCards.Container:ActionSet({
    actions = {
        DeferralCards.Action:Submit({ title = "Save" }),
        DeferralCards.Action:OpenUrl({ 
            title = "Learn More", 
            url = "https://example.com" 
        })
    }
})
```

### `DeferralCards.Container:ColumnSet`

Creates a set of columns for layout.

**Parameters:**

* `options` (table): ColumnSet configuration

**Default Properties:**

* `type`: "ColumnSet"
* `columns`: {} (empty array)

### `DeferralCards.Container:Column`

Creates a column within a ColumnSet.

**Parameters:**

* `options` (table): Column configuration

**Default Properties:**

* `type`: "Column"
* `items`: {} (empty array)

**Common Options:**

* `width` (string/number): "auto", "stretch", or specific width

**Example:**

```lua
local columnSet = DeferralCards.Container:ColumnSet({
    columns = {
        DeferralCards.Container:Column({
            width = "auto",
            items = {
                DeferralCards.CardElement:TextBlock({ text = "Left" })
            }
        }),
        DeferralCards.Container:Column({
            width = "stretch",
            items = {
                DeferralCards.CardElement:TextBlock({ text = "Right" })
            }
        })
    }
})
```

### `DeferralCards.Container:FactSet`

Creates a fact set for displaying key-value pairs.

**Parameters:**

* `options` (table): FactSet configuration

**Default Properties:**

* `type`: "FactSet"
* `facts`: {} (empty array)

**Example:**

```lua
local factSet = DeferralCards.Container:FactSet({
    facts = {
        DeferralCards.Container:Fact({
            title = "Name",
            value = "John Doe"
        }),
        DeferralCards.Container:Fact({
            title = "Age",
            value = "30"
        })
    }
})
```

### `DeferralCards.Container:ImageSet`

Creates a set of images displayed together.

**Parameters:**

* `options` (table): ImageSet configuration

**Default Properties:**

* `type`: "ImageSet"
* `images`: {} (empty array)

## Action Module

### `DeferralCards.Action:OpenUrl`

Creates an action that opens a URL when triggered.

**Parameters:**

* `options` (table): OpenUrl action configuration

**Default Properties:**

* `type`: "Action.OpenUrl"
* `url`: "https://www.google.co.uk/"

**Example:**

```lua
local openAction = DeferralCards.Action:OpenUrl({
    title = "Visit Website",
    url = "https://example.com"
})
```

### `DeferralCards.Action:Submit`

Creates a submit action for form submission.

**Parameters:**

* `options` (table): Submit action configuration

**Default Properties:**

* `type`: "Action.Submit"

**Example:**

```lua
local submitAction = DeferralCards.Action:Submit({
    title = "Submit Form",
    data = {
        formType = "registration",
        version = 1
    }
})
```

### `DeferralCards.Action:ShowCard`

Creates an action that shows another card.

**Parameters:**

* `options` (table): ShowCard action configuration

**Default Properties:**

* `type`: "Action.ShowCard"

### `DeferralCards.Action:ToggleVisibility`

Creates an action that toggles element visibility.

**Parameters:**

* `options` (table): ToggleVisibility action configuration

**Default Properties:**

* `type`: "Action.ToggleVisibility"
* `targetElements`: {} (empty array)

**Example:**

```lua
local toggleAction = DeferralCards.Action:ToggleVisibility({
    title = "Show Details",
    targetElements = {
        DeferralCards.Action:TargetElement({
            elementId = "details",
            isVisible = true
        })
    }
})
```

### `DeferralCards.Action:Execute`

Creates a custom execute action.

**Parameters:**

* `options` (table): Execute action configuration

**Default Properties:**

* `type`: "Action.Execute"

## Input Module

### `DeferralCards.Input:Text`

Creates a text input field.

**Parameters:**

* `options` (table): Text input configuration

**Default Properties:**

* `type`: "Input.Text"
* `id`: "input\_text"

**Example:**

```lua
local textInput = DeferralCards.Input:Text({
    id = "username",
    placeholder = "Enter your username",
    maxLength = 50
})
```

### `DeferralCards.Input:Number`

Creates a number input field.

**Parameters:**

* `options` (table): Number input configuration

**Default Properties:**

* `type`: "Input.Number"
* `id`: "input\_number"

**Example:**

```lua
local numberInput = DeferralCards.Input:Number({
    id = "age",
    placeholder = "Enter your age",
    min = 0,
    max = 120
})
```

### `DeferralCards.Input:Date` / `DeferralCards.Input:Time`

Creates date and time input fields.

### `DeferralCards.Input:Toggle`

Creates a toggle (checkbox) input.

**Parameters:**

* `options` (table): Toggle input configuration

**Default Properties:**

* `type`: "Input.Toggle"
* `title`: "Title"
* `id`: "input\_toggle"

### `DeferralCards.Input:ChoiceSet`

Creates a choice set (dropdown/radio buttons).

**Parameters:**

* `options` (table): ChoiceSet configuration

**Default Properties:**

* `type`: "Input.ChoiceSet"
* `choices`: {} (empty array)
* `id`: "choice\_set"

**Example:**

```lua
local choiceSet = DeferralCards.Input:ChoiceSet({
    id = "color",
    placeholder = "Select a color",
    choices = {
        DeferralCards.Input:Choice({
            title = "Red",
            value = "red"
        }),
        DeferralCards.Input:Choice({
            title = "Blue", 
            value = "blue"
        })
    }
})
```

## Complete Example

```lua
local card = DeferralCards.Card:Create({
    body = {
        DeferralCards.CardElement:TextBlock({
            text = "User Registration",
            size = "large",
            weight = "bolder",
            horizontalAlignment = "center"
        }),
        DeferralCards.Container:Create({
            items = {
                DeferralCards.Input:Text({
                    id = "name",
                    placeholder = "Full Name"
                }),
                DeferralCards.Input:Number({
                    id = "age",
                    placeholder = "Age",
                    min = 18,
                    max = 100
                }),
                DeferralCards.Input:ChoiceSet({
                    id = "country",
                    placeholder = "Select Country",
                    choices = {
                        DeferralCards.Input:Choice({
                            title = "United States",
                            value = "US"
                        }),
                        DeferralCards.Input:Choice({
                            title = "Canada",
                            value = "CA"
                        })
                    }
                })
            }
        }),
        DeferralCards.Container:ActionSet({
            actions = {
                DeferralCards.Action:Submit({
                    title = "Register",
                    data = { action = "register" }
                })
            }
        })
    }
})
```
