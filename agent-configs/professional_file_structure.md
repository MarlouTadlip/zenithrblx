# Hybrid Feature-ECS Architecture Plan

This document outlines a high-end, professional architecture for the `zenithrblx` project. It combines the scalability of **Feature-Based** organization with the data-driven decoupling of **ECS**, while maintaining standard Roblox patterns like **Services/Controllers**.

---

## The Core Philosophy
1.  **Feature-Based**: Code is grouped by functional domain (e.g., Combat, Inventory).
2.  **ECS Focused**: Logic is separated into **Systems** (behavior) and **Components** (data).
3.  **Model/Logic Separation**: Data structures and pure models are defined separately from the systems that manipulate them.

---

## Proposed Directory Structure

```text
src/
├── features/                     # Functional domains
│   ├── Combat/                   # Example Feature: Combat
│   │   ├── components/           # ECS Data (Health, Stunned, Reach)
│   │   ├── systems/              # ECS Logic (DamageSystem, KnockbackSystem)
│   │   ├── models/               # Pure data models & Luau types
│   │   ├── services/             # Server-only feature logic (Secure API)
│   │   ├── controllers/          # Client-only feature logic (VFX, Input)
│   │   └── utils/                # Feature-specific helper functions
│   │
│   ├── Inventory/                # Example Feature: Inventory
│   │   ├── components/           # InventoryItem, Equipped
│   │   ├── systems/              # ToolGripSystem, ReloadSystem
│   │   ├── models/               # Item definitions & schemas
│   │   └── ...
│   │
│   └── Core/                     # Essential engine-level features
│       ├── Data/                 # PlayerData, Persistence
│       └── Network/              # Remotes, Middleware
│
├── shared/                       # Global Infrastructure
│   ├── ecs/                      # ECS Framework (Matter/Jecs) setup
│   ├── utils/                    # Global utilities (Math, Table, String)
│   ├── constants/                # Global Game Constants
│   └── ui/                       # Reusable UI Components (Fusion/Roact/Video)
│
├── server/                       # Bootstrap
│   ├── init.server.luau          # Main entry (Starts ECS and Services)
│   └── runtime/                  # Global server-only systems
│
└── client/                       # Bootstrap
    ├── init.client.luau          # Main entry (Starts ECS and Controllers)
    └── runtime/                  # Global client-only systems (Camera, Parallax)
```

---

## Detailed Component Breakdown

### 1. `features/[FeatureName]/`
Each feature is a "micro-module" that contains everything it needs to function.
-   **`components/`**: Table-based data containers. *Example: `MeleeData.luau`*.
-   **`systems/`**: Functions that iterate over entities with specific components. *Example: `AttackSystem.luau`*.
-   **`models/`**: The "Source of Truth" for data structure. No logic, just type definitions and default state.
-   **`services/`**: Bridges the gap between ECS and external systems (like DataStores) or provides secure RemoteEvents for that specific feature.
-   **`utils/`**: Functions used frequently within this feature but nowhere else.

### 2. `shared/`
Contains code that is NOT feature-specific. If multiple features need a specific math function, it goes in `shared/utils`.

### 3. `server/` & `client/`
These folders are strictly for **Bootstrapping**. Their only job is to:
1. Initialize the ECS engine.
2. Require all Feature Services/Controllers.
3. Start the game loop.

---

## Why this works for Roblox
-   **Isolation**: You can delete the entire `Combat` folder, and the rest of the game will still compile and run (it just won't have combat).
-   **Collaboration**: Two developers can work on `Inventory` and `Combat` simultaneously without ever touching the same files.
-   **Performance**: ECS allows you to handle thousands of objects (bullets, drops, NPCs) with minimal overhead.
-   **Maintainability**: Finding where code lives is intuitive. If you have an issue with Inventory logic, you look in `features/Inventory/systems`.

---

## Implementation Roadmap
1. **Create the `features/` directory** and move your existing logic into a `Core/` feature.
2. **Define Shared ECS Logic** in `shared/ecs` to provide the bridge for feature systems.
3. **Map in Rojo**: Update `default.project.json` to mount `features` into `ReplicatedStorage` (Shared portions) and `ServerScriptService` (Server portions).
