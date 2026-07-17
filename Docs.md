# novaCode v0.0.1 BETA – Specification & Documentation

Welcome to the official specification for novaCode, the lightweight, event-driven scripting language built natively for the PurpleNova Unturned framework. Designed to be simple, highly readable, and fast to write.

---

## 🔑 Core Concepts & Typings

To prevent runtime errors, you must understand the distinction between **Events**, **Actions**, and **Methods/Properties**, as well as their strict dependencies. Attempting to call an Action or Method outside its required context will cause the execution to fail.

| Type | Definition | Example |
| :--- | :--- | :--- |
| **Event** | A trigger fired by the game server (e.g., player chatting, joining). You connect code to these. | `Chatted` |
| **Action** | An operation executed on an object that performs a task (requires parenthesis). | `Player:SendMessage(...)` |
| **Method / Property** | Retrieves, formats, or updates specific values on an object. | `Player.Vector3` or `format(...)` |

---

## 🔑 Keywords & Structural Elements

### `func` *(Type: Keyword / Declaration)*
Declares a new executable function or command wrapper. 
* **Usage:** `func('command_name'):`
* **Example:** `func('tp'):`
* **Dependencies:** Must be declared at the root level of your file (no indentation).

### `tMsg` *(Type: Property / Variable Binding)*
The trigger message that registers and binds a specific chat command keyword to its parent function.
* **Usage:** `tMsg = '/command', (attribute_source)`
* **Dependencies:** **STRICTLY DEPENDENT** on being inside a `func` block that contains a `Player:Connect(Chatted)` event block.

---

## 👤 Player Object & API Interactivity

### `Player` *(Type: Context Object)*
Represents the active player executing the script.

### `Player:Connect(Event)` *(Type: Event Connection)*
Binds a block of code to a specific server trigger.
* **Usage:** `Player:Connect(Chatted)`
* **Dependencies:** Currently, the `Chatted` event is the only supported event. All command parameters, attributes, and messages depend on this being established first.

### `Player.Vector3` *(Type: Property)*
Retrieves the player's current 3D position vector in the game world.

### `Player.Vector3.new` *(Type: Action)*
Assigns a brand-new position to the player, immediately teleporting them in-game.
* **Usage:** `Player.Vector3.new = target_vector`

### `Player:SendMessage("Text", "Color")` *(Type: Action)*
Sends a colored chat message directly to the targeted player.
* **Usage:** `Player:SendMessage("Teleported!", "green")`

---

## 📊 Input Handling & Validation

### `attributes` *(Type: Validation Method)*
Defines the expected command arguments. It forces the interpreter to validate inputs before proceeding.
* **Syntax:** `attributes(count, format(types))`
* **Dependencies:** **STRICTLY DEPENDENT** on the `Player:Connect(Chatted)` event. This method cannot be called inside any other event context.

### `format()` *(Type: Formatting Method)*
Specifies the exact types required for your command attributes.
* **Supported Types:** `int` (Integer), `float` (Decimal), `str` (String).
* **Usage:** Must be passed directly as the second argument of `attributes`.
* **Dependencies:** **STRICTLY DEPENDENT** on the `attributes` method. Calling `format()` on its own is invalid.

---

## ⚙️ Engine Constants & Syntax Rules

1. **Indentation-Based Scope (No Brackets)**
   Like Python, blocks of code are defined strictly by indentation levels. Any code indented under a `func` or a `:Connect` block belongs entirely to that scope.
2. **Strict Call Hierarchy**
   Do not attempt to read attributes, format strings, or access command variables (`tMsg`) unless you are explicitly nested within an active, connected event scope. 

---

## 📝 Example Script: Teleport Command (`/tp`)

Here is how a complete `/tp` script looks in novaCode v0.0.1 BETA, showcasing how methods and actions connect together:

```text
# Define the teleport function (Root Level)
func('tp'):
    # Event Connection (Level 1 Indent)
    Player:Connect(Chatted)
    
        # Attribute and formatting declarations (Level 2 Indent)
        # DEPENDENCY: Must exist inside the 'Chatted' event scope
        attributes(1, format(int, int, int))
        tMsg = '/tp', (attribute[1])
        
        # Action: Retrieve the validated coordinates
        targetLocation = tp:Connect:GetAttribute(attribute[1])
        
        # Action: Update the player's position in-game
        Player.Vector3.new = targetLocation
        Player:SendMessage("Teleported successfully!", "green")
