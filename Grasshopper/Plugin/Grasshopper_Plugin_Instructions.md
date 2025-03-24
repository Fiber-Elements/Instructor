# Grasshopper Plugin Creation Instructions

When you want me to create a Grasshopper plugin, provide these instructions along with your specific requirements:

```
CREATE GRASSHOPPER PLUGIN
-------------------------
Name: [Your plugin name]
Description: [Brief description of what your plugin does]
Components:
- [Component 1 name]: [Brief description of what this component does]
  Inputs: [List of inputs with types]
  Outputs: [List of outputs with types]
- [Component 2 name]: [Brief description]
  Inputs: [List of inputs with types]
  Outputs: [List of outputs with types]
Additional Requirements: [Any special requirements or features]
```

## Example:

```
CREATE GRASSHOPPER PLUGIN
-------------------------
Name: Geometry Tools
Description: A plugin with various geometry manipulation tools
Components:
- Point Grid: Creates a grid of points in 3D space
  Inputs: 
    - Base Point (Point): Origin point for the grid
    - X Count (Integer): Number of points in X direction
    - Y Count (Integer): Number of points in Y direction
    - X Spacing (Number): Distance between points in X direction
    - Y Spacing (Number): Distance between points in Y direction
  Outputs:
    - Points (Point List): Generated grid points
- Line Divider: Divides a line into equal segments
  Inputs:
    - Line (Line): Line to divide
    - Count (Integer): Number of divisions
  Outputs:
    - Points (Point List): Division points
    - Segments (Line List): Line segments
Additional Requirements: Include icons for components
```

I'll create the following files and structure for your plugin:

1. Solution file (.sln)
2. Project file (.csproj) with proper references
3. Assembly info (AssemblyInfo.cs)
4. Plugin info class (YourPluginNameInfo.cs)
5. Component classes for each component
6. Build script (build.bat)
7. Manifest file (manifest.yml)

Each component will be properly structured with:
- Unique GUID
- Proper input/output parameter registration
- Implementation of the SolveInstance method
- Error handling

The plugin will be built as a .gha file that can be installed in the Grasshopper Libraries folder.
