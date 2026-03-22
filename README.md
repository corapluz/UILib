# UI Library

> A fully-themed, feature-rich GUI framework with smooth animations, a built-in flag/config system, and a wide variety of interactive elements.

---

## Table of Contents

- [Overview](#overview)
- [Initialization](#initialization)
- [Window Structure](#window-structure)
- [Sections](#sections)
- [Elements](#elements)
  - [Toggle](#toggle)
  - [Slider](#slider)
  - [Button](#button)
  - [Dropdown](#dropdown)
  - [Listbox](#listbox)
  - [Textbox](#textbox)
  - [Keybind](#keybind)
  - [Label](#label)
  - [Colorpicker](#colorpicker)
- [Keybind List](#keybind-list)
- [Settings Page](#settings-page)
- [Notifications](#notifications)
- [Config System](#config-system)
- [Theme System](#theme-system)
- [Quick Reference](#quick-reference)
- [Full Example](#full-example)

---

## Overview

Key capabilities:
- Multi-page window with sidebar tab navigation
- Draggable & resizable window with minimize support
- Dark theme with customizable accent colors
- Built-in config save/load system
- Keybind tracking with Toggle / Hold / Always modes
- Toast notifications
- Full color picker with hue, saturation, alpha, and hex input

---

## Initialization

### `Library:Window( data )`

Creates the main window. Must be called before anything else. Returns a `Window` object.

| Property | Type | Description |
|----------|------|-------------|
| `Name` | `string` | Title shown in the top-left of the window. |
| `SubName` | `string` | Subtitle / version string shown below the title. |
| `Logo` | `string` | Asset ID *(without `rbxassetid://`)* for the logo icon. |

```lua
local Window = Library:Window({
    Name    = "My Tool",
    SubName = "v1.0.0",
    Logo    = "120959262762131"
})
```

---

### `Window:Init()`

Finalizes and shows the window. Call this **once** after all pages and elements have been created. Nothing will be visible until `Init()` is called.

```lua
Window:Init()
```

---

## Window Structure

The library uses a four-level hierarchy to organize content:

```
Window
└── Category  (sidebar group label)
    └── Page  (navigable tab)
        └── Section  (content panel)
            └── Element  (toggle, slider, button, etc.)
```

### `Window:Category( name )`

Adds a named group label to the left sidebar. Categories are visual separators that appear above the pages that follow them.

```lua
Window:Category("Utilities & Settings")
```

---

### `Window:Page( data )`

Creates a navigable page that appears under the most recently created category. Returns a `Page` object.

| Property | Type | Description |
|----------|------|-------------|
| `Name` | `string` | Label shown in the sidebar tab. |
| `Icon` | `string` | Asset ID for the tab icon. |
| `Columns` | `number` | Number of side-by-side section columns *(default: 2)*. |

```lua
local MyPage = Window:Page({
    Name    = "Settings",
    Icon    = "123456789",
    Columns = 2
})
```

---

## Sections

### `Page:Section( data )`

Creates a content panel inside a page. All interactive elements are added to a section. Returns a `Section` object.

| Property | Type | Description |
|----------|------|-------------|
| `Name` | `string` | Header title of the section panel. |
| `Description` | `string` | Subtitle text shown below the name. |
| `Icon` | `string` | Asset ID for the section header icon. |
| `Side` | `number` | Column index *(1 = left, 2 = right, etc.)*. |

```lua
local Sec = MyPage:Section({
    Name        = "General",
    Description = "Basic settings",
    Icon        = "987654321",
    Side        = 1
})
```

---

## Elements

All elements are created by calling a method on a `Section` object. Every element that stores a value accepts a `Flag` — a unique string key used by the config and keybind systems.

---

### Toggle

**`Section:Toggle( data )`** — An on/off switch. Returns a `Toggle` object.

| Property | Type | Description |
|----------|------|-------------|
| `Name` | `string` | Label displayed next to the toggle. |
| `Flag` | `string` | Unique key for config save/load. |
| `Default` | `boolean` | Initial state (`true` = on, `false` = off). |
| `Callback` | `function` | Called with the new boolean value on change. |

```lua
local T = Sec:Toggle({
    Name     = "Enable Feature",
    Flag     = "myFeatureEnabled",
    Default  = false,
    Callback = function(value) print(value) end
})
```

> **Sub-settings:** Call `T:Settings(width)` on a toggle to get a nested sub-panel. Elements added to it are only visible when the toggle is expanded.
>
> ```lua
> local Sub = T:Settings(300)
> Sub:Slider({ Name = "Intensity", Flag = "intensity", Min = 0, Max = 100, Default = 50 })
> ```

---

### Slider

**`Section:Slider( data )`** — A draggable range input. Returns a `Slider` object.

| Property | Type | Description |
|----------|------|-------------|
| `Name` | `string` | Label shown above the slider. |
| `Flag` | `string` | Unique key for config save/load. |
| `Min` | `number` | Minimum value. |
| `Max` | `number` | Maximum value. |
| `Default` | `number` | Initial value. |
| `Decimals` | `number` | Decimal places (`0` = integer). |
| `Suffix` | `string` | Unit label shown after the value *(e.g. `"km/h"`)*. |
| `Callback` | `function` | Called with the new number value on change. |

```lua
Sec:Slider({
    Name     = "Speed",
    Flag     = "speedValue",
    Min      = 1,
    Max      = 200,
    Default  = 50,
    Decimals = 1,
    Suffix   = "km/h",
    Callback = function(v) print(v) end
})
```

---

### Button

**`Section:Button( data )`** — A clickable button that fires a callback.

| Property | Type | Description |
|----------|------|-------------|
| `Name` | `string` | Button label text. |
| `Callback` | `function` | Called with no arguments when clicked. |

```lua
Sec:Button({
    Name     = "Execute",
    Callback = function() print("clicked!") end
})
```

---

### Dropdown

**`Section:Dropdown( data )`** — A collapsible list for single or multi-select. Returns a `Dropdown` object.

| Property | Type | Description |
|----------|------|-------------|
| `Name` | `string` | Label shown above the dropdown. |
| `Flag` | `string` | Unique key for config save/load. |
| `Items` | `table` | Array of option strings. |
| `Default` | `table` | Array of pre-selected option strings. |
| `Multi` | `boolean` | Allow multiple simultaneous selections. |
| `Callback` | `function` | Called with the selected value (or table if `Multi = true`). |

```lua
Sec:Dropdown({
    Name     = "Mode",
    Flag     = "driveMode",
    Items    = { "Sport", "Eco", "Drift" },
    Default  = { "Eco" },
    Multi    = false,
    Callback = function(v) print(v) end
})
```

---

### Listbox

**`Section:Listbox( data )`** — A scrollable, always-visible selection list. Returns a `Listbox` object.

| Property | Type | Description |
|----------|------|-------------|
| `Flag` | `string` | Unique key for config save/load. |
| `Items` | `table` | Array of option strings. |
| `Default` | `string` | Initially selected item. |
| `Size` | `number` | Pixel height of the listbox. |
| `Multi` | `boolean` | Allow multiple simultaneous selections. |
| `Callback` | `function` | Called with the selected value on change. |

```lua
Sec:Listbox({
    Flag     = "vehicleList",
    Items    = { "Sedan", "SUV", "Truck" },
    Default  = "Sedan",
    Size     = 150,
    Multi    = false,
    Callback = function(v) print(v) end
})
```

---

### Textbox

**`Section:Textbox( data )`** — A single-line text input field.

| Property | Type | Description |
|----------|------|-------------|
| `Flag` | `string` | Unique key for config save/load. |
| `Default` | `string` | Initial text content. |
| `Placeholder` | `string` | Hint text shown when empty. |
| `Numeric` | `boolean` | Restrict input to numbers only. |
| `Finished` | `boolean` | If `true`, callback fires only on Enter/focus lost. If `false`, fires on every keystroke. |
| `Callback` | `function` | Called with the current string value. |

```lua
Sec:Textbox({
    Flag        = "plateText",
    Default     = "KING",
    Placeholder = "Enter plate...",
    Numeric     = false,
    Finished    = true,
    Callback    = function(v) print(v) end
})
```

---

### Keybind

**`Section:Keybind( data )`** — Lets the user assign a keyboard shortcut. Supports **Toggle**, **Hold**, and **Always** modes.

| Property | Type | Description |
|----------|------|-------------|
| `Name` | `string` | Label for the keybind row. |
| `Flag` | `string` | Unique key for config save/load. |
| `Default` | `Enum.KeyCode` | Initial key assignment. |
| `Callback` | `function` | Called when the bound key is triggered. |

```lua
Sec:Keybind({
    Name     = "Boost",
    Flag     = "boostKey",
    Default  = Enum.KeyCode.X,
    Callback = function() print("boost!") end
})
```

---

### Label

**`Section:Label( text )`** — A non-interactive text label. Useful as a sub-header inside a section.

```lua
Sec:Label("Visual Customization")
```

Labels also support chaining a Colorpicker directly:

```lua
Sec:Label("Accent Color"):Colorpicker({ ... })
```

---

### Colorpicker

**`Label:Colorpicker( data )`** — A color picker attached to a label row. Opens a floating palette with hue, saturation, alpha controls, and a hex input.

| Property | Type | Description |
|----------|------|-------------|
| `Name` | `string` | Internal identifier. |
| `Flag` | `string` | Unique key for config save/load. |
| `Default` | `Color3` | Initial color value. |
| `Callback` | `function` | Called with a `Color3` value on change. |

```lua
Sec:Label("Accent Color"):Colorpicker({
    Name     = "Accent",
    Flag     = "accentColor",
    Default  = Color3.fromRGB(0, 116, 224),
    Callback = function(c) print(c) end
})
```

---

## Keybind List

**`Library:KeybindList( title )`** — Creates a floating overlay that lists all currently active keybinds and their assigned keys.

```lua
local KeybindList = Library:KeybindList("Active Keybinds")
```

Pass `KeybindList` into `Library:CreateSettingsPage()` so the settings page can reference it.

---

## Settings Page

**`Library:CreateSettingsPage( window, keybindList )`** — Creates a pre-built Settings page with config management (create, load, save, delete) and theme color pickers. Returns the settings `Page` object.

```lua
local SettingsPage = Library:CreateSettingsPage(Window, KeybindList)
```

The settings page is added as a regular sidebar tab — no manual wiring required.

---

## Notifications

**`Library:Notification( data )`** — Shows a timed toast notification in the corner of the screen.

| Property | Type | Description |
|----------|------|-------------|
| `Title` | `string` | Bold heading of the notification. |
| `Description` | `string` | Body text. |
| `Duration` | `number` | Seconds before the notification fades out. |
| `Icon` | `string` | Asset ID for the notification icon. |

```lua
Library:Notification({
    Title       = "Loaded",
    Description = "Welcome, " .. game.Players.LocalPlayer.Name,
    Duration    = 5,
    Icon        = "73789337996373"
})
```

---

## Config System

Every element with a `Flag` automatically participates in the config system. Configs are stored as JSON files on the filesystem.

### `Library.Flags`

A global table holding the current value of every flagged element. Read it at any time:

```lua
print(Library.Flags["speedValue"])        -- current slider value
print(Library.Flags["myFeatureEnabled"])  -- current toggle state
```

### `Library.SetFlags`

A table of setter functions keyed by `Flag`. Use them to programmatically update element values:

```lua
Library.SetFlags["speedValue"](75)
Library.SetFlags["driveMode"]("Sport")
```

### Save / Load

```lua
-- Get the current config as a JSON string
local json = Library:GetConfig()

-- Load a config from a JSON string
Library:LoadConfig(json)
```

The built-in Settings page exposes a full UI for create / load / save / delete without any extra code.

### Folder Structure

| Path | Description |
|------|-------------|
| `Library.Folders.Directory` | Root folder for all library data. |
| `Library.Folders.Configs` | Stores saved config JSON files. |
| `Library.Folders.Assets` | Stores downloaded font and asset files. |

---

## Theme System

Every UI element is registered with the theme system. Changing a color propagates instantly to all registered items:

```lua
Library:ChangeTheme("Accent", Color3.fromRGB(255, 100, 0))
```

| Key | Description |
|-----|-------------|
| `Accent` | Primary interactive color (buttons, toggles, sliders). |
| `AccentGradient` | Secondary color used in gradient highlights. |
| `Background` | Main window background. |
| `Background 2` | Alternate background layer. |
| `Text` | Primary text color. |
| `Outline` | Border / stroke color. |
| `Section Top` | Section header background. |
| `Section Background` | Section body background. |
| `Section Background 2` | Alternate section background. |
| `Element` | Color for individual UI elements. |

---

## Quick Reference

| Method | Returns |
|--------|---------|
| `Library:Window( data )` | `Window` |
| `Window:Category( name )` | — |
| `Window:Page( data )` | `Page` |
| `Window:Init()` | — |
| `Page:Section( data )` | `Section` |
| `Section:Toggle( data )` | `Toggle` |
| `Section:Slider( data )` | `Slider` |
| `Section:Button( data )` | — |
| `Section:Dropdown( data )` | `Dropdown` |
| `Section:Listbox( data )` | `Listbox` |
| `Section:Textbox( data )` | — |
| `Section:Keybind( data )` | `Keybind` |
| `Section:Label( text )` | `Label` |
| `Label:Colorpicker( data )` | — |
| `Toggle:Settings( width )` | `Section` |
| `Library:KeybindList( title )` | `KeybindList` |
| `Library:CreateSettingsPage( win, kbl )` | `Page` |
| `Library:Notification( data )` | — |
| `Library:GetConfig()` | `string` (JSON) |
| `Library:LoadConfig( json )` | — |
| `Library:ChangeTheme( key, color )` | — |
| `Library:Unload()` | — |

---

## Full Example

```lua
-- Keybind list (pass to settings page)
local KeybindList = Library:KeybindList("Active Keybinds")

-- Create the window
local Window = Library:Window({
    Name    = "My Tool",
    SubName = "v1.0.0",
    Logo    = "120959262762131"
})

-- Pages
Window:Category("General")
local MainPage = Window:Page({ Name = "Main", Icon = "123456789" })

-- Sections & elements
local Sec = MainPage:Section({ Name = "Options", Side = 1 })

local SpeedToggle = Sec:Toggle({
    Name     = "Enable Speed",
    Flag     = "enableSpeed",
    Default  = false,
    Callback = function(v) print("Speed:", v) end
})

Sec:Slider({
    Name     = "Speed",
    Flag     = "speedValue",
    Min      = 1, Max = 200, Default = 50,
    Decimals = 1, Suffix = "km/h",
    Callback = function(v) print("Speed value:", v) end
})

Sec:Dropdown({
    Name     = "Mode",
    Flag     = "driveMode",
    Items    = { "Sport", "Eco", "Drift" },
    Default  = { "Eco" },
    Multi    = false,
    Callback = function(v) print("Mode:", v) end
})

Sec:Label("Theme"):Colorpicker({
    Name     = "Accent",
    Flag     = "accentColor",
    Default  = Color3.fromRGB(0, 116, 224),
    Callback = function(c) print("Color:", c) end
})

Sec:Keybind({
    Name     = "Toggle Key",
    Flag     = "toggleKey",
    Default  = Enum.KeyCode.RightControl,
    Callback = function() print("toggled!") end
})

-- Settings page
Window:Category("Utilities")
local SettingsPage = Library:CreateSettingsPage(Window, KeybindList)

-- Notification on load
Library:Notification({
    Title       = "Ready",
    Description = "Welcome, " .. game.Players.LocalPlayer.Name,
    Duration    = 5,
    Icon        = "73789337996373"
})

-- Finalize
Window:Init()
getgenv().Library = Library
return Library
```
