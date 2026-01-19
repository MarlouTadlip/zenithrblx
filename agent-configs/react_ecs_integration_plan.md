# React-Lua + ECS Integration Plan (No Knit)

This plan outlines how to integrate **React-Lua** (UI Layer) with an **ECS-driven** (Logic Layer) architecture. This approach follows a "Pure" unidirectional data flow, similar to modern React/Redux/ECS patterns in high-performance games.

---

## 1. Core Architecture: The "State-Sync" Pattern

Since ECS is procedural (Systems loop every frame) and React is declarative (Components update on change), we use a **Bridge** logic to connect them:

1.  **ECS (The Source of Truth)**: ECS systems handle all logic (Health, Positions, Timers).
2.  **The Store/Bridge**: When an ECS component changes, it updates a shared "UI State" or triggers an Event.
3.  **React (The View)**: React components listen to the Bridge and re-render only when necessary.

---

## 2. Directory Structure

```text
src/
├── features/                     # Functional Domains
│   ├── [FeatureName]/            # Example: HealthSystem
│   │   ├── ui/                   # React Components (PascalCase)
│   │   │   ├── HealthBar.luau
│   │   │   └── DamageIndicator.luau
│   │   ├── systems/              # ECS Logic (camelCase)
│   │   ├── components/           # ECS Data Definitions
│   │   ├── hooks/                # Feature-specific React Hooks
│   │   └── models/               # Pure data schemas/types
│   │
├── shared/                       # Global Infrastructure
│   ├── ui/                       # Foundation (Design System, Contexts)
│   │   ├── hooks/                # useEntity, useComponent, etc.
│   │   └── providers/            # ECSDataProvider, ThemeProvider
│   ├── ecs/                      # ECS Framework configuration
│   └── bridge/                   # Global Signal/Store (Redux/Cortex)
│
├── client/                       # Bootstrap (No Knit)
│   ├── init.client.luau          # Root React Mount & ECS Initialization
│   └── app.luau                  # The Root React Component (Portals)
│
└── server/                       # Bootstrap
    └── init.server.luau          # Server ECS Loop Initialization
```

---

## 3. Communication Strategy (How React sees ECS)

Without Knit's dependency injection, we rely on **Hooks** and **Signals**:

### The "Watcher" System
Instead of React checking the ECS every frame (slow), we create a **Shared Signal/Event Bus** in `shared/bridge`.
-   **ECS System**: Modifies Health -> Fires `UI_REFRESH` Signal.
-   **React Hook**: `useComponent(entity, "Health")` -> Listens to the Signal and updates local state.

### React Root Management
Your `client/init.client.luau` will handle the manual lifecycle:
```lua
-- client/init.client.luau example
local React = require(Packages.React)
local ReactRoblox = require(Packages.ReactRoblox)
local App = require(script.Parent.app)

-- 1. Initialize ECS Loop
local world = require(Shared.ecs.world)
RunService.Heartbeat:Connect(function(dt)
    -- System updates...
end)

-- 2. Mount React
local root = ReactRoblox.createRoot(Instance.new("Folder")) -- Or PlayerGui
root:render(React.createElement(App, {
    world = world
}))
```

---

## 4. Key Integration Components

### `shared/ui/hooks/useComponent.luau`
A custom hook that makes React "reactive" to ECS changes. It mimics how you might use `useSelector` in Web development.

### `client/app.luau`
The main Layout component that uses `ReactRoblox.createPortal` to inject UI from different features into the `PlayerGui`.

---

## 5. Benefits for Full-Stack Developers
-   **Separation of Concerns**: Your UI logic (React) and Game logic (ECS) are completely decoupled.
-   **Predictable State**: Game state flows one way: ECS -> Bridge -> React.
-   **Familiar Tooling**: You can use your React mental model (Context, Refs, Hooks) to build professional-grade UIs while keeping the heavy lifting in high-performance ECS systems.

---

## 6. Implementation Steps
1.  **Wally Setup**: Add `jsdotlua/react` and `jsdotlua/react-roblox` to `wally.toml`.
2.  **Bridge Creation**: Setup a simple Signal or Store in `shared/bridge`.
3.  **Root Component**: Create `client/app.luau` to act as the entry point for all feature UI.
4.  **Feature Integration**: Move UI logic from `features/X/controllers` to `features/X/ui`.
