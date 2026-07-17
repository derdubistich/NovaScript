# novaCode v0.0.1 BETA – Specification & Documentation

Welcome to the official specification for novaCode, the lightweight, event-driven scripting language built natively for the PurpleNova Unturned framework. Designed to be simple, highly readable, and fast to write.

---

## 🔑 Core Concepts & Keywords

### `func`
Declares a new executable function or command. Currently, functions are mapped to in-game chat commands.
* **Usage:** `func('command_name'):`
* **Example:** `func('tp'):` triggers when a player types `/tp` in chat.

### `tMsg`
The trigger message that registers and binds a specific command keyword to its parent function. 
* **Usage:** Assigned inside a function block to link the chat trigger to the script logic.
* **Example:** `tMsg = '/tp'`

### `Player`
Represents the current active player (wrapping their SteamID, game object, and connection state under the hood).
* **Usage:** Used to listen to player-specific events or execute actions on them.
* **Example:** `Player:Connect(Chatted)` registers a listener for when the player sends a chat message.

### `Vector3`
Represents a 3D coordinate (X, Y, Z) in the game world.
* **Reading Position:** `Player.Vector3` retrieves the player's current location.
* **Updating Position:** `Player.Vector3.new` assigns a brand-new position to the player, immediately teleporting them in-game.

---

## 📊 Input Handling & Validation

To keep command parsing simple, novaCode uses a built-in validation system to ensure players type the correct arguments.

### `attributes`
Defines the arguments expected by a command function. It forces the interpreter to validate inputs before executing the code.
* **Syntax:** `attributes(count, format(types))`
* **Example:** `attributes(1, format(int, int, int))` requires exactly one attribute containing three integers (e.g., `100 50 -200`).

### `format()`
Specifies the strict data types required for attributes. 
* **Supported Types:** `int` (Integer), `float` (Decimal), `str` (Text/String).
* **Usage:** Combined with the `attributes` method.
* **Example:** `format(str, int)` requires a text string followed by a number.

---

## Engine Constants & Additions

To make beta scripts functional, the engine handles these structural rules:

### 1. Indentation-Based Scope (No Brackets)
Like Python, blocks of code are defined strictly by indentation.
* Any code indented under a `func` or a `:Connect` block belongs to that scope and runs only when triggered.

### 2. `Player:SendMessage()`
Allows scripts to send feedback text directly to the player's chat box.
* **Syntax:** `Player:SendMessage("Your message here", "color")`
* **Example:** `Player:SendMessage("Teleporting...", "green")`

### 3. Basic Variable Assignment (`=`)
To store coordinates or state temporarily.
* **Example:** `targetPos = tp:Connect:GetAttribute(attribute[1])`

---

## 📝 Example Script: Teleport Command (`/tp`)

Here is how a complete `/tp` script looks in novaCode v0.0.1 BETA:

```text
# Define the teleport function
func('tp'):
    Player:Connect(Chatted)
        # Require 1 attribute formatted as 3 integers (X, Y, Z)
        attributes(1, format(int, int, int))
        tMsg = '/tp', (attribute[1])
        
        # Get the validated coordinate from the command
        targetLocation = tp:Connect:GetAttribute(attribute[1])
        
        # Apply the new coordinates to the player
        Player.Vector3.new = targetLocation
        Player:SendMessage("Teleported successfully!", "green")
