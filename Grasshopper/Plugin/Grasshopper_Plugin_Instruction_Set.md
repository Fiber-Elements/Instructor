# Grasshopper Plugin Development Instructions

This document provides a step-by-step guide for creating a Grasshopper plugin from scratch. Follow these instructions to create a properly structured plugin that can be loaded by Grasshopper.

## 1. Project Setup

### 1.1 Create Project Structure

```
YourPluginName/
├── YourPluginName.sln                 # Solution file
├── src/                               # Source code directory
│   ├── YourPluginName.csproj          # Project file
│   ├── YourPluginNameInfo.cs          # Plugin info class
│   ├── Properties/                    # Assembly properties
│   │   └── AssemblyInfo.cs            # Assembly info
│   ├── Components/                    # Components directory
│   │   ├── Category1/                 # Components organized by category
│   │   │   └── Component1.cs          # Component implementation
│   │   └── Category2/
│   │       └── Component2.cs
│   └── Resources/                     # Resources directory (optional)
│       └── Icons/                     # Component icons
├── build.bat                          # Build script
├── manifest.yml                       # Package manifest
├── LICENSE                            # License file
└── README.md                          # Documentation
```

### 1.2 Create Project File (YourPluginName.csproj)

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net48</TargetFramework>
    <Version>1.0.0</Version>
    <Title>Your Plugin Name</Title>
    <Description>Description of your plugin</Description>
    <TargetExt>.gha</TargetExt>
    <Authors>Your Name</Authors>
    <Company>Your Company</Company>
    <Copyright>Copyright © 2025</Copyright>
    <PackageLicenseExpression>MIT</PackageLicenseExpression>
    <PackageRequireLicenseAcceptance>false</PackageRequireLicenseAcceptance>
    <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Grasshopper" Version="7.13.21348.13001" IncludeAssets="compile;build" />
    <!-- Add other dependencies here -->
  </ItemGroup>

  <PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Debug|AnyCPU'">
    <DefineConstants>TRACE;DEBUG</DefineConstants>
    <DebugType>full</DebugType>
    <DebugSymbols>true</DebugSymbols>
  </PropertyGroup>

  <PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Release|AnyCPU'">
    <DefineConstants>TRACE</DefineConstants>
    <DebugType>none</DebugType>
    <DebugSymbols>false</DebugSymbols>
  </PropertyGroup>

  <ItemGroup>
    <Reference Include="System" />
    <Reference Include="System.Core" />
    <Reference Include="System.Drawing" />
    <!-- Add other system references here -->
  </ItemGroup>

  <Target Name="PostBuild" AfterTargets="PostBuildEvent" Condition="'$(OS)' == 'Windows_NT'">
    <Exec Command="Copy &quot;$(TargetPath)&quot; &quot;$(AppData)\Grasshopper\Libraries\$(ProjectName).gha&quot;" ContinueOnError="true" />
  </Target>

</Project>
```

### 1.3 Create Assembly Info (AssemblyInfo.cs)

```csharp
using System.Reflection;
using System.Runtime.InteropServices;

[assembly: AssemblyTitle("Your Plugin Name")]
[assembly: AssemblyDescription("Description of your plugin")]
[assembly: AssemblyConfiguration("")]
[assembly: AssemblyCompany("Your Company")]
[assembly: AssemblyProduct("Your Plugin Name")]
[assembly: AssemblyCopyright("Copyright © 2025")]
[assembly: AssemblyTrademark("")]
[assembly: AssemblyCulture("")]

[assembly: ComVisible(false)]

// Replace with your own unique GUID
[assembly: Guid("00000000-0000-0000-0000-000000000000")]

[assembly: AssemblyVersion("1.0.0.0")]
[assembly: AssemblyFileVersion("1.0.0.0")]
```

## 2. Plugin Registration

### 2.1 Create Plugin Info Class (YourPluginNameInfo.cs)

```csharp
using System;
using System.Drawing;
using Grasshopper.Kernel;

namespace YourPluginName
{
    public class YourPluginNameInfo : GH_AssemblyInfo
    {
        public override string Name => "Your Plugin Name";

        public override Bitmap Icon => null; // Add your icon here

        public override string Description => "Description of your plugin";

        // Replace with your own unique GUID
        public override Guid Id => new Guid("00000000-0000-0000-0000-000000000000");

        public override string AuthorName => "Your Name";

        public override string AuthorContact => "your.email@example.com";

        public override string Version => "1.0.0";
    }
}
```

## 3. Creating Components

### 3.1 Basic Component Template

```csharp
using System;
using System.Drawing;
using Grasshopper.Kernel;

namespace YourPluginName.Components.Category
{
    public class YourComponent : GH_Component
    {
        public YourComponent()
            : base(
                "Component Name",       // Component name
                "Nickname",             // Component nickname
                "Component description", // Description
                "Your Plugin Name",     // Category (tab name)
                "Subcategory")          // Subcategory
        {
        }

        // Replace with your own unique GUID
        public override Guid ComponentGuid => new Guid("00000000-0000-0000-0000-000000000000");

        protected override Bitmap Icon => null; // Add your icon here

        protected override void RegisterInputParams(GH_Component.GH_InputParamManager pManager)
        {
            // Register input parameters
            // Example: pManager.AddTextParameter("Input", "I", "Input description", GH_ParamAccess.item);
        }

        protected override void RegisterOutputParams(GH_Component.GH_OutputParamManager pManager)
        {
            // Register output parameters
            // Example: pManager.AddTextParameter("Output", "O", "Output description", GH_ParamAccess.item);
        }

        protected override void SolveInstance(IGH_DataAccess DA)
        {
            // Get input data
            // Example: string input = string.Empty; DA.GetData(0, ref input);
            
            // Process data
            
            // Set output data
            // Example: DA.SetData(0, result);
        }
    }
}
```

### 3.2 Input Parameter Types

```csharp
// Common input parameter types
pManager.AddBooleanParameter("Boolean", "B", "Boolean description", GH_ParamAccess.item, false);
pManager.AddIntegerParameter("Integer", "I", "Integer description", GH_ParamAccess.item, 0);
pManager.AddNumberParameter("Number", "N", "Number description", GH_ParamAccess.item, 0.0);
pManager.AddTextParameter("Text", "T", "Text description", GH_ParamAccess.item, "Default");
pManager.AddPointParameter("Point", "P", "Point description", GH_ParamAccess.item);
pManager.AddVectorParameter("Vector", "V", "Vector description", GH_ParamAccess.item);
pManager.AddPlaneParameter("Plane", "Pl", "Plane description", GH_ParamAccess.item);
pManager.AddLineParameter("Line", "L", "Line description", GH_ParamAccess.item);
pManager.AddCurveParameter("Curve", "C", "Curve description", GH_ParamAccess.item);
pManager.AddSurfaceParameter("Surface", "S", "Surface description", GH_ParamAccess.item);
pManager.AddMeshParameter("Mesh", "M", "Mesh description", GH_ParamAccess.item);
pManager.AddGeometryParameter("Geometry", "G", "Geometry description", GH_ParamAccess.item);
```

### 3.3 Access Types

```csharp
// Access types
GH_ParamAccess.item      // Single item
GH_ParamAccess.list      // List of items
GH_ParamAccess.tree      // Data tree
```

### 3.4 Getting and Setting Data

```csharp
// Getting data
bool boolValue = false;
int intValue = 0;
double doubleValue = 0.0;
string textValue = string.Empty;
Point3d pointValue = Point3d.Origin;

DA.GetData(0, ref boolValue);
DA.GetData(1, ref intValue);
DA.GetData(2, ref doubleValue);
DA.GetData(3, ref textValue);
DA.GetData(4, ref pointValue);

// Getting lists
List<double> doubleList = new List<double>();
DA.GetDataList(5, doubleList);

// Setting data
DA.SetData(0, resultValue);

// Setting lists
DA.SetDataList(1, resultList);
```

## 4. Advanced Component Features

### 4.1 Component with Custom Icon

```csharp
protected override Bitmap Icon
{
    get
    {
        // Method 1: Load from embedded resource
        return Properties.Resources.YourIconName;
        
        // Method 2: Create programmatically
        Bitmap bitmap = new Bitmap(24, 24);
        using (Graphics g = Graphics.FromImage(bitmap))
        {
            g.Clear(Color.White);
            g.DrawRectangle(Pens.Black, 2, 2, 20, 20);
        }
        return bitmap;
    }
}
```

### 4.2 Component with Menu Items

```csharp
protected override void AppendAdditionalComponentMenuItems(ToolStripDropDown menu)
{
    base.AppendAdditionalComponentMenuItems(menu);
    
    // Add a menu item
    ToolStripMenuItem item = Menu_AppendItem(menu, "Menu Item", MenuItemClicked, true, false);
    
    // Add a separator
    Menu_AppendSeparator(menu);
    
    // Add a checkbox item
    bool checkState = true; // Get your current state
    Menu_AppendItem(menu, "Checkbox Item", MenuItemClicked, true, checkState);
}

private void MenuItemClicked(object sender, EventArgs e)
{
    // Handle menu item click
    ExpireSolution(true);
}
```

### 4.3 Component with Custom Parameters

```csharp
protected override void RegisterInputParams(GH_Component.GH_InputParamManager pManager)
{
    pManager.AddParameter(new MyCustomParameter(), "Custom", "C", "Custom parameter", GH_ParamAccess.item);
}

// Custom parameter class
public class MyCustomParameter : GH_Param<GH_ObjectWrapper>
{
    public MyCustomParameter() : base("Custom", "C", "Custom parameter", "Category", "Subcategory", GH_ParamAccess.item) { }
    
    public override Guid ComponentGuid => new Guid("00000000-0000-0000-0000-000000000000");
    
    // Implement other required methods
}
```

## 5. Building and Deployment

### 5.1 Build Script (build.bat)

```batch
@echo off
echo Building Your Plugin...

REM Build the project
dotnet build src\YourPluginName.csproj -c Release

REM Create the dist directory
if not exist dist mkdir dist

REM Copy the built files to the dist directory
copy src\bin\Release\net48\YourPluginName.gha dist\
copy LICENSE dist\
copy README.md dist\
copy manifest.yml dist\

echo Build completed successfully!
echo The plugin is available in the 'dist' directory.
```

### 5.2 Manifest File (manifest.yml)

```yaml
---
name: your-plugin-name
version: 1.0.0
authors:
  - Your Name
description: >
  Description of your plugin.
url: https://github.com/yourusername/YourPluginName
keywords:
  - grasshopper
  - your-keyword
  - another-keyword
  # Include component GUIDs
  - guid:00000000-0000-0000-0000-000000000000
```

## 6. Testing and Debugging

### 6.1 Debug Messages

```csharp
protected override void SolveInstance(IGH_DataAccess DA)
{
    // Add runtime messages
    AddRuntimeMessage(GH_RuntimeMessageLevel.Remark, "Informational message");
    AddRuntimeMessage(GH_RuntimeMessageLevel.Warning, "Warning message");
    AddRuntimeMessage(GH_RuntimeMessageLevel.Error, "Error message");
}
```

### 6.2 Debugging with Visual Studio

1. Set the Rhino executable as the external program in project properties
2. Set command line arguments to `/nosplash /notemplate`
3. Set breakpoints in your code
4. Start debugging (F5)

## 7. Distribution

### 7.1 Manual Installation

1. Build the plugin using the build script
2. Copy the .gha file to the Grasshopper Libraries folder:
   - Windows: `%APPDATA%\Grasshopper\Libraries`
   - Mac: `~/Library/Application Support/Grasshopper/Libraries`

### 7.2 Package with Yak

1. Install Yak from Rhino
2. Create a manifest.yml file
3. Run `yak build` in the dist directory
4. Distribute the .yak file

## 8. Common Patterns and Best Practices

### 8.1 Component Organization

- Group related components in the same category and subcategory
- Use meaningful names and descriptions
- Provide helpful tooltips and descriptions

### 8.2 Error Handling

```csharp
protected override void SolveInstance(IGH_DataAccess DA)
{
    try
    {
        // Component logic
    }
    catch (Exception ex)
    {
        AddRuntimeMessage(GH_RuntimeMessageLevel.Error, $"Error: {ex.Message}");
    }
}
```

### 8.3 Performance Optimization

- Cache results when possible
- Use ExpireSolution(false) to prevent unnecessary recalculation
- Implement IGH_PreviewObject for custom preview

### 8.4 Component Persistence

```csharp
public override bool Write(GH_IO.Serialization.GH_IWriter writer)
{
    // Write custom properties
    writer.SetBoolean("MyProperty", myPropertyValue);
    return base.Write(writer);
}

public override bool Read(GH_IO.Serialization.GH_IReader reader)
{
    // Read custom properties
    if (reader.ItemExists("MyProperty"))
        myPropertyValue = reader.GetBoolean("MyProperty");
    return base.Read(reader);
}
```

## 9. Example Components

### 9.1 Simple Text Component

```csharp
using System;
using System.Drawing;
using Grasshopper.Kernel;

namespace YourPluginName.Components.Examples
{
    public class TextComponent : GH_Component
    {
        public TextComponent()
            : base(
                "Text Component",
                "Text",
                "A simple component that takes text input and outputs the same text",
                "Your Plugin Name",
                "Examples")
        {
        }

        public override Guid ComponentGuid => new Guid("00000000-0000-0000-0000-000000000000");

        protected override Bitmap Icon => null;

        protected override void RegisterInputParams(GH_Component.GH_InputParamManager pManager)
        {
            pManager.AddTextParameter("Input", "I", "Text input", GH_ParamAccess.item);
        }

        protected override void RegisterOutputParams(GH_Component.GH_OutputParamManager pManager)
        {
            pManager.AddTextParameter("Output", "O", "Text output", GH_ParamAccess.item);
        }

        protected override void SolveInstance(IGH_DataAccess DA)
        {
            string input = string.Empty;
            if (!DA.GetData(0, ref input)) return;

            DA.SetData(0, input);
        }
    }
}
```

### 9.2 Math Component

```csharp
using System;
using System.Drawing;
using Grasshopper.Kernel;

namespace YourPluginName.Components.Examples
{
    public class MathComponent : GH_Component
    {
        public MathComponent()
            : base(
                "Math Component",
                "Math",
                "Performs basic math operations on two numbers",
                "Your Plugin Name",
                "Examples")
        {
        }

        public override Guid ComponentGuid => new Guid("00000000-0000-0000-0000-000000000001");

        protected override Bitmap Icon => null;

        protected override void RegisterInputParams(GH_Component.GH_InputParamManager pManager)
        {
            pManager.AddNumberParameter("Number A", "A", "First number", GH_ParamAccess.item, 0.0);
            pManager.AddNumberParameter("Number B", "B", "Second number", GH_ParamAccess.item, 0.0);
            pManager.AddIntegerParameter("Operation", "Op", "0=Add, 1=Subtract, 2=Multiply, 3=Divide", GH_ParamAccess.item, 0);
        }

        protected override void RegisterOutputParams(GH_Component.GH_OutputParamManager pManager)
        {
            pManager.AddNumberParameter("Result", "R", "Result of the operation", GH_ParamAccess.item);
        }

        protected override void SolveInstance(IGH_DataAccess DA)
        {
            double a = 0.0;
            double b = 0.0;
            int operation = 0;

            if (!DA.GetData(0, ref a)) return;
            if (!DA.GetData(1, ref b)) return;
            if (!DA.GetData(2, ref operation)) return;

            double result = 0.0;

            switch (operation)
            {
                case 0: // Add
                    result = a + b;
                    break;
                case 1: // Subtract
                    result = a - b;
                    break;
                case 2: // Multiply
                    result = a * b;
                    break;
                case 3: // Divide
                    if (Math.Abs(b) < 1e-10)
                    {
                        AddRuntimeMessage(GH_RuntimeMessageLevel.Error, "Cannot divide by zero");
                        return;
                    }
                    result = a / b;
                    break;
                default:
                    AddRuntimeMessage(GH_RuntimeMessageLevel.Error, "Unknown operation");
                    return;
            }

            DA.SetData(0, result);
        }
    }
}
```

## 10. Resources and References

- [Grasshopper Developer Documentation](https://developer.rhino3d.com/guides/grasshopper/)
- [Grasshopper SDK Reference](https://developer.rhino3d.com/api/grasshopper/html/R_Project_Grasshopper.htm)
- [Rhino Developer Tools](https://www.rhino3d.com/download/rhino/7.0/developer/)
- [Yak Package Manager](https://developer.rhino3d.com/guides/yak/)
- [Grasshopper Developer Samples](https://github.com/mcneel/rhino-developer-samples)
