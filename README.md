# Lyapos UI Library — Documentation

A clean, dark-themed Roblox UI library with smooth animations, theming support, and config saving.

---

## Getting Started

```lua
local Library = loadstring(game:HttpGet("YOUR_RAW_URL"))()

local Window = Library:Window({
    Name    = "My Script",
    SubName = "v1.0",
    Logo    = "ASSET_ID"
})
```

> ⚠️ Always write your UI code **after** the `loadstring(...)()` call.

---

## Window

```lua
local Window = Library:Window({
    Name    = "Title",
    SubName = "Subtitle",
    Logo    = "rbxassetid_number_here"
})
```

| Option    | Type   | Description             |
|-----------|--------|-------------------------|
| `Name`    | string | Main title              |
| `SubName` | string | Subtitle below title    |
| `Logo`    | string | Asset ID for logo image |

---

## Page

```lua
local Page = Window:Page({
    Name    = "Combat",
    Icon    = "ASSET_ID",
    Columns = 2        -- optional, default: 2
})
```

---

## Section

```lua
local Section = Page:Section({
    Name        = "Aimbot",
    Description = "optional subtitle",
    Icon        = "ASSET_ID",
    Side        = 1    -- 1 = left column, 2 = right column
})
```

---

## Elements

### Toggle
```lua
Section:Toggle({
    Name     = "Enable Aimbot",
    Flag     = "AimbotEnabled",
    Default  = false,
    Callback = function(value)
        print("Toggle:", value)
    end
})
```

---

### Button
```lua
Section:Button({
    Name     = "Teleport",
    Icon     = "ASSET_ID",   -- optional
    Callback = function()
        print("Clicked!")
    end
})
```

---

### Slider
```lua
Section:Slider({
    Name     = "FOV",
    Flag     = "AimbotFOV",
    Min      = 0,
    Max      = 360,
    Default  = 90,
    Decimals = 1,
    Suffix   = "°",
    Callback = function(value)
        print("Slider:", value)
    end
})
```

---

### Dropdown
```lua
Section:Dropdown({
    Name    = "Bone",
    Flag    = "AimbotBone",
    Items   = { "Head", "Torso", "HumanoidRootPart" },
    Default = "Head",
    Multi   = false,
    Size    = 125,
    Callback = function(value)
        print("Selected:", value)
    end
})
```

Set `Multi = true` to allow multiple selections — value will be a table.

---

### Keybind
```lua
Section:Keybind({
    Name     = "Toggle Menu",
    Flag     = "MenuBind",
    Default  = Enum.KeyCode.RightShift,
    Mode     = "Toggle",   -- "Toggle" | "Hold" | "Always"
    Callback = function(toggled)
        print("Active:", toggled)
    end
})
```

---

### Textbox
```lua
Section:Textbox({
    Flag        = "PlayerName",
    Placeholder = "Enter name...",
    Default     = "",
    Numeric     = false,
    Finished    = true,    -- true = fires only on Enter
    Callback    = function(value)
        print("Input:", value)
    end
})
```

---

### Label
```lua
local lbl = Section:Label("Player Info")
lbl:SetText("HP: 100")
```

A label can also hold a **color picker**:
```lua
lbl:Colorpicker({
    Flag     = "LabelColor",
    Default  = Color3.fromRGB(255, 100, 100),
    Callback = function(color, alpha)
        print(color, alpha)
    end
})
```

---

### Listbox
```lua
Section:Listbox({
    Flag     = "PlayerList",
    Items    = { "Player1", "Player2" },
    Multi    = false,
    Size     = 125,
    Callback = function(value)
        print("Picked:", value)
    end
})
```
Includes a built-in **search bar**.

---

## Flags

Every element with a `Flag` stores its value in `Library.Flags`:

```lua
print(Library.Flags["AimbotEnabled"])  -- true / false
print(Library.Flags["AimbotFOV"])      -- number
```

---

## Notifications

```lua
Library:Notification({
    Title       = "Success",
    Description = "Script loaded.",
    Icon        = "ASSET_ID",
    Duration    = 3
})
```

---

## Theming

```lua
-- Change accent color at runtime
Library:ChangeTheme("Accent", Color3.fromRGB(0, 120, 255))
Library:ChangeTheme("AccentGradient", Color3.fromRGB(0, 200, 255))
```

Built-in theme keys: `Accent`, `AccentGradient`, `Background`, `Background 2`, `Text`, `Outline`, `Section Top`, `Section Background`, `Section Background 2`, `Element`

---

## Config System

```lua
-- Save
writefile("configs/myconfig.json", Library:GetConfig())

-- Load
Library:LoadConfig(readfile("configs/myconfig.json"))
```

Or use the built-in Settings page:
```lua
Library:CreateSettingsPage(Window)
```

---

## Keybind List Widget

Shows active keybinds in a floating overlay.

```lua
local KeyList = Library:KeybindList("Binds")
```

Keybinds registered with a `Flag` appear here automatically.

---

## Unload

```lua
Library:Unload()  -- destroys all GUI, disconnects all connections
```

---

## Full Example

```lua
local Library = loadstring(game:HttpGet("YOUR_URL"))()

local Window = Library:Window({ Name = "MyHub", SubName = "v1.0", Logo = "0" })

local Page = Window:Page({ Name = "Main", Icon = "0" })

local Section = Page:Section({ Name = "Combat", Side = 1 })

Section:Toggle({
    Name = "Silent Aim", Flag = "SilentAim", Default = false,
    Callback = function(v) print("SilentAim:", v) end
})

Section:Slider({
    Name = "FOV", Flag = "FOV", Min = 1, Max = 360, Default = 90,
    Suffix = "°", Decimals = 1,
    Callback = function(v) print("FOV:", v) end
})

Section:Dropdown({
    Name = "Bone", Flag = "Bone",
    Items = {"Head","Torso"}, Default = "Head",
    Callback = function(v) print("Bone:", v) end
})
```
