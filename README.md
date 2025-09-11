# 🛠️dnlib.BAML [![Nuget](https://img.shields.io/nuget/v/dnlib.BAML)](https://www.nuget.org/packages/dnlib.BAML/)
**The ultimate BAML editor for .NET applications.** Edit BAML resources embedded in compiled executables — **no source code required**.

## ✨ Features
- **Direct BAML Editing** – Modify embedded BAML resources without the original XAML files.  
- **Post-Build Tool** – Works on compiled assemblies, fully compatible with obfuscators.  
- **Targeted Modifications** – Change UI elements like **Window Titles** and **TextBlock** content effortlessly.  
- **Memory-Efficient** – Reads and writes BAML directly via the powerful `dnlib` library.  
- **Cross-Version Support** – Works across different .NET framework versions.

## 💡 What is BAML?
BAML (**Binary Application Markup Language**) is a compiled, binary representation of XAML used in WPF applications.  
It is usually embedded inside a `.g.resources` file within your `.NET` assembly.  

`dnlib.BAML` allows you to:
1. Read `.baml` files from compiled assemblies.
2. Modify UI elements programmatically.
3. Write the edited BAML back into the executable.

This means you can **change the UI of any WPF app** without needing the original source.

## 🖼️ How It Works
Inside a compiled assembly:
```
Assembly
└── Resources
    └── *.g.resources
        ├── Window1.baml
        └── MainPage.baml
```
Each `.baml` file corresponds to a XAML file. `dnlib.BAML` gives you programmatic access to these files for reading, modifying, and saving.

## ⚙️ Quick Start
### Step 1 – Install the Package
```bash
Install-Package dnlib.BAML
```
### Step 2 – Usage Example
```csharp
using dnlib.DotNet;
using dnlib.DotNet.Resources;

using (ModuleDefMD module = ModuleDefMD.Load("WpfApp1.exe"))
{
    if (module.HasGResources()) //Check if exist g.resources
    {
        var gResources = module.gResources(); //Get the g.resources
        foreach (var element in gResources.Elements) //Get the Elements
        {
            var em = gResources.Read(element.Name); //Read the element
            foreach (var record in em.Document) //Edit...
            {
                if (record.Type == BamlRecordType.PropertyWithConverter)
                {
                    PropertyWithConverterRecord pwcr = record as PropertyWithConverterRecord;
                    if (pwcr.Value.ToLower().Contains("window title"))
                    {
                        pwcr.Value = "dnlib.BAML";
                    }
                    else if (pwcr.Value.ToLower().Contains("change my text"))
                    {
                        pwcr.Value = "Coded By Soheil MV.";
                    }
                }
            }
            gResources.Write(em); //Write the element
        }
        module.RemoveGResources(); //Delete the g.resources in the module
        module.Resources.Add(gResources.GetAsEmbeddedResource()); //Add the edited g.resources to the module
    }
}
```
**Explanation:**
- **Load Assembly:** Opens the target `.exe`.
- **Check Resources:** Verifies if `g.resources` exists.
- **Edit Elements:** Iterates over BAML records and modifies desired values.
- **Save Changes:** Writes the updated BAML back into the assembly.

# :pushpin:Credits
- [dnlib](https://github.com/0xd4d/dnlib) (Reads and writes .NET assemblies and modules)
