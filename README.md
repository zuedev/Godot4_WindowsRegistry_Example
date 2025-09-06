# Godot 4 Windows Registry Example

This repository provides a simple Godot C# example demonstrating how to interact with the Windows Registry. The project shows how to write a value to a registry key and then read it back.

## About the Project

This project was created to provide a straightforward example for Godot developers on how to perform basic Windows Registry operations using C#. This can be useful for saving application settings, user preferences, or other data that needs to persist between sessions in a standard Windows location.

The example uses the `Microsoft.Win32` namespace, which is part of the .NET framework and is readily available in Godot C# projects.

## How to Use

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/zuedev/Godot_WindowsRegistry_Example.git
    ```
2.  **Open the project:**
    - Open the Godot Engine (v4.1.1-stable_mono or later).
    - Click "Import" and browse to the `source` directory inside the cloned repository.
    - Select the `project.godot` file.
3.  **Run the project:**
    - Press F5 or click the "Play" button in the Godot editor.
4.  **Check the output:**
    - The Godot output panel will display the results of the registry operations. It will show the attempt to write a value, the success message, and then the attempt to read the value back.

## The Code (`RegistryDemo.cs`)

The core logic is contained in `RegistryDemo.cs`. Here is the code with explanations:

```csharp
using Godot;
using Microsoft.Win32; // <-- Import this for registry access
using System;

public partial class RegistryDemo : Node
{
    public override void _Ready()
    {
        // 1. DEFINE OUR TEST KEY AND VALUE
        // We use HKEY_CURRENT_USER (Registry.CurrentUser) so we don't need admin rights.
        string keyPath = @"Software\ZUEDEV\Godot_WindowsRegistry_Example"; // A safe path for testing
        string valueName = "LastRunTime";
        string valueData = DateTime.Now.ToString();

        try
        {
            // 2. WRITE THE VALUE
            GD.Print($"Attempting to write to registry at: HKEY_CURRENT_USER\{keyPath}");

            // CreateSubKey will create the path if it doesn't exist AND open it for writing.
            using (RegistryKey key = Registry.CurrentUser.CreateSubKey(keyPath))
            {
                key.SetValue(valueName, valueData);
                GD.Print($"SUCCESS: Wrote value '{valueData}'");
            }

            // 3. READ THE VALUE BACK
            GD.Print("Attempting to read value back...");
            using (RegistryKey key = Registry.CurrentUser.OpenSubKey(keyPath))
            {
                if (key != null)
                {
                    // GetValue returns an 'object', so we cast it to a string.
                    string readData = key.GetValue(valueName) as string;
                    GD.Print($"SUCCESS: Read back: '{readData}'");
                }
                else
                {
                    GD.PrintErr("Error: Could not find key to read.");
                }
            }
        }
        catch (Exception ex)
        {
            GD.PrintErr($"REGISTRY ERROR: {ex.Message}");
        }
    }
}
```

### Explanation:

1.  **Namespace:** We import `Microsoft.Win32` to get access to the `Registry` and `RegistryKey` classes.
2.  **Key Path:** We define a path within `HKEY_CURRENT_USER`. Using `HKEY_CURRENT_USER` is important because it does not require the application to have administrative privileges. The path `Software\YourCompany\YourApp` is a standard convention.
3.  **Writing a Value:**
    - `Registry.CurrentUser.CreateSubKey(keyPath)` attempts to open the specified key. If the key (or any part of the path) does not exist, it will be created.
    - `key.SetValue(valueName, valueData)` writes the data to the specified value. If the value doesn't exist, it's created; if it does, it's overwritten.
4.  **Reading a Value:**
    - `Registry.CurrentUser.OpenSubKey(keyPath)` opens the key for reading.
    - `key.GetValue(valueName)` retrieves the data associated with the value. It's returned as an `object`, so you may need to cast it to the appropriate type (in this case, `string`).
5.  **Error Handling:** The `try...catch` block is used to handle potential `SecurityException` or other issues that might occur if the application doesn't have the necessary permissions.

## Important Notes

-   **Platform Specific:** This code will only work on Windows. You should add platform checks if you are developing a cross-platform application.
-   **Permissions:** Be mindful of where you are writing in the registry. `HKEY_CURRENT_USER` is generally safe. Writing to `HKEY_LOCAL_MACHINE` requires administrator rights.

## License

This project is licensed under the Unlicense. See the [LICENSE](LICENSE) file for details.
