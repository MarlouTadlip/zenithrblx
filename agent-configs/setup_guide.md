# Project Setup & Dependency Guide

This document covers the initial setup, toolchain requirements, and necessary libraries for the `zenithrblx` project.

---

## 🛠 1. Toolchain Setup
Ensure you have these tools installed on your system. We use **Aftman** to manage tool versions.

| Tool | Purpose | Install Command |
| :--- | :--- | :--- |
| **Aftman** | Toolchain Manager | [Download Here](https://github.com/LPGhatguy/aftman) |
| **Rojo** | VS Code -> Roblox Sync | `aftman add rojo` |
| **Wally** | Package Manager (NPM equivalent) | `aftman add wally` |
| **Selene** | Linter (ESLint equivalent) | `aftman add selene` |

Run `aftman install` in the root directory to ensure all tools are ready.

---

## 📦 2. Core Dependencies (Wally)
Add these to your `wally.toml` to support our **React + ECS** architecture.

### [dependencies] (Shared/Client)
*   **React**: `jsdotlua/react@17.0.1` (The UI Library)
*   **ReactRoblox**: `jsdotlua/react-roblox@17.0.1` (The Renderer)
*   **Jecs**: `ukendio/jecs@0.1.0` (High-performance ECS Engine)
*   **Promise**: `evaera/promise@4.0.0` (Handling async operations)
*   **Signal**: `sleitnick/signal@2.0.3` (The Bridge/Event Bus)

### [server-dependencies] (Server-only)
*   **Janitor**: `howmanysmall/janitor@1.18.3` (Memory management/Cleanup)
*   **ByteNet**: `ffrostfall/bytenet@0.4.0` (Efficient Networking)

---

## 🚀 3. Initial Installation Steps

1.  **Sync Dependencies**:
    ```bash
    wally install
    ```
    *This creates the `Packages/` and `ServerPackages/` folders.*

2.  **Generate Sourcemap**:
    ```bash
    rojo sourcemap default.project.json --output sourcemap.json
    ```
    *This helps VS Code (Luau Language Server) understand where your modules are.*

3.  **Start Rojo Sync**:
    *   Open Roblox Studio.
    *   Open the Rojo Plugin.
    *   In VS Code, press `F1` and run `Rojo: Start Server`.
    *   In Studio, click **Connect**.

---

## 🏗 4. Development Workflow
1.  **Create a Feature**: Add a folder in `src/features/Name`.
2.  **Write Logic**: Use ECS systems in `features/Name/systems`.
3.  **Write UI**: Create React components in `features/Name/ui`.
4.  **Connect**: Use the `shared/bridge` signals to send data from ECS to React.
5.  **Commit**: `git add .` -> `git commit -m "feat: description"`

---

## 💡 Pro-Tip for Full-Stack Developers
If you are coming from `npm`, you might be tempted to look for a `node_modules` folder. In this project, that is the `Packages` folder. **Do not modify files in Packages manually**, as they are overwritten whenever you run `wally install`.
