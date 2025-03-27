# Prompt-Optimized Instructions for Stable Grasshopper Python Components

This guide provides a concise, optimized approach to creating reliable Python script components in Grasshopper, followed by comprehensive reference material.

## Quick Start Guide

### Core Structure Pattern (Direct Script)
```python
"""Component description"""
import rhinoscriptsyntax as rs
import Rhino.Geometry as rg

# 1. Initialize outputs
result1 = None
result2 = None

try:
    # 2. Validate inputs with defaults
    if input1 is None:
        raise ValueError("Input required")
    input2 = default_value if input2 is None else input2
    
    # 3. Convert GUIDs to geometry if needed
    geometry = rs.coercecurve(input1)
    if geometry is None:
        raise ValueError("Invalid geometry")
    
    # 4. Main logic - use Rhino/Grasshopper functions when possible
    # ...processing...
    
    # 5. Assign results
    result1 = processed_data
    
except Exception as e:
    import Rhino
    Rhino.RhinoApp.WriteLine(f"Error: {str(e)}")
    # Reset outputs on error
    result1 = None
    result2 = None
```

### Essential Rules

1. **Structure & Naming**
   - Use descriptive parameter names and variables
   - Initialize ALL outputs at start
   - Right-click parameters to set human-readable names and tooltips
   - Use the direct script approach consistently

2. **Input Handling**
   - Always check for `None` values
   - Provide sensible defaults
   - Convert types safely: `count = int(count) if count is not None else 10`
   - Use Type Hints for automatic conversions (right-click parameter → Type Hint)
   - Mark critical inputs as Required (right-click → Required)
   - Use list comprehensions for processing multiple inputs: `points = [rs.coerce3dpoint(pt) for pt in point_list]`

3. **GUID Conversion**
   - Always convert GUIDs to geometry: `curve = rs.coercecurve(curve_guid)`
   - Use Type Hint of "ghdoc Object" for parameters that need GUID conversion
   - Always validate conversion results: `if curve_obj is None: raise ValueError("Invalid curve")`
   - Handle type conversion in a consistent way

4. **Data Tree Access**
   - Set appropriate Parameter Access (Item, List, or Tree)
   - Check type before accessing:
     ```python
     if isinstance(x, Grasshopper.DataTree[object]):
         for path in x.Paths:
             for item in x.Branch(path):
                 # Process item
     ```

5. **Error Handling**
   - Wrap main logic in try/except
   - Report errors to console with descriptive messages
   - Return initialized outputs even on error
   - Use clear, descriptive error messages: `raise ValueError("Invalid input")`

6. **Code Efficiency**
   - Use Rhino/Grasshopper built-in functions instead of custom logic
   - Keep code short and focused
   - Use list comprehensions for concise operations
   - Leverage Rhino.Geometry namespace for geometric operations

### Component Structure Approaches

There are several ways to structure Grasshopper Python components:

1. **Direct Assignment (Recommended)**
   - Process inputs and assign directly to output variables
   - Simplest approach and most straightforward
   - Example: `a = process_data(x, y)`

2. **Main Function with Global Variables**
   - Define a main() function that processes inputs and sets global output variables
   - Used in more complex components with multiple helper functions
   - Example: 
   ```python
   def main():
       global a, b
       # Process inputs
       a = result1
       b = result2
   
   main()
   ```

3. **Object-Oriented Approach**
   - Create classes to encapsulate functionality
   - Use a main function to orchestrate
   - Best for complex components with many operations
   - Example:
   ```python
   class Processor:
       def __init__(self, input1, input2):
           self.input1 = input1
           self.input2 = input2
           
       def process(self):
           # Processing logic
           return result
   
   processor = Processor(x, y)
   a = processor.process()
   ```

4. **RunScript Function (Legacy)**
   - Wrap all code in a RunScript function that takes inputs and returns outputs
   - Used in older components
   - Not recommended for new components

### Common Pitfalls to Avoid

1. **Name Conflicts**: Don't use Python built-ins as variable names (`list`, `range`, `str`)
2. **Type Safety**: Don't assume input types, use safe conversion patterns
3. **Missing Validation**: Don't skip input validation, don't assume inputs are connected
4. **GUID Handling**: Don't use GUID properties directly, always convert first
5. **Performance Issues**: Toggle off unused "out" parameter, use list comprehensions

---

# Comprehensive Grasshopper Python Scripting Guide

## Table of Contents
- [Basic Setup](#basic-setup)
- [Parameter Configuration](#parameter-configuration)
- [Script Development](#script-development)
- [Debugging](#debugging)
- [Advanced Features](#advanced-features)
- [Publishing and Distribution](#publishing-and-distribution)
- [Best Practices](#best-practices)
- [Examples](#examples)

## Basic Setup

### Creating Python Components

1. Access the Python component:
   - Go to the **Maths** tab and **Script** panel
   - Drop a Python 3 Script component onto the canvas
   - Alternatively, use the generic _Script_ component and choose Python 3

2. Script Editor Interface:
   - Double-click component to open script editor
   - Status bar shows Python version
   - Editor provides syntax highlighting and code completion

3. Component Options:
   - Right-click menu provides core functionality
   - Advanced options available via Shift + Right-click
   - Preview, Enable, and Bake options standard

4. Layout Customization:
   - Toggle Dashboard visibility
   - Compact mode options
   - Script tabs configuration
   - Browser and Console layout options

5. Template Usage:
   - Access via Templates panel
   - Double-click to replace script content
   - Create custom templates via User Objects

## Parameter Configuration

### Input/Output Setup

1. Parameter Management:
   - Use ZUI (Zoomable User Interface)
   - Add parameters with ⊕ button
   - Remove parameters with ⊖ button
   - Right-click to rename parameters

2. Type System:
   - Type Hints for input validation
   - Automatic type conversion
   - Reserved names handling
   - Type safety considerations

3. Parameter Access Modes:
   - **Item**: Individual data processing
   - **List**: Branch-level processing
   - **Tree**: Full data structure access

4. Standard Output:
   - Special 'out' parameter
   - Console output capture
   - Multi-line vs single-line output
   - Toggle output visibility

5. Output Preview Controls:
   - Individual preview toggles
   - Preview customization
   - Performance considerations

## Script Development

### Script Modes

1. Script-Mode:
   - Simple variable-based scripting
   - Direct input/output access
   - Automatic variable definition
   - Best for straightforward operations
   - Default mode for new components
   - Perfect for most scripting needs

2. SDK-Mode:
   - Full component lifecycle control
   - BeforeRunScript/AfterRunScript hooks
   - Custom preview rendering
   - Complex state management

### Converting to SDK-Mode

1. Using the Editor's Conversion Tool:
   - Double-click component to open script editor
   - Click "Convert To GH_ScriptInstance" button on editor dashboard
   - Editor automatically:
     * Creates proper class structure
     * Sets up RunScript with correct signature
     * Handles initialization
     * Maintains existing code

2. Important Notes:
   - Do NOT implement SDK-Mode manually
   - Always use the editor's conversion button
   - Let Grasshopper handle the class creation
   - Conversion is one-way (cannot convert back to Script-Mode)

3. When to Use SDK-Mode:
   - Need BeforeRunScript/AfterRunScript hooks
   - Want custom preview rendering
   - Require complex component lifecycle management
   - Otherwise, prefer Script-Mode for simplicity

### Data Handling

1. Marshalling:
   - Guid handling
   - CPython type conversions
   - Input/Output data transformation
   - Performance implications

2. Type Safety:
   - Python dynamic typing
   - Type hint usage
   - Error handling
   - Conversion patterns

3. Caching:
   - Script compilation cache
   - Manual cache control
   - Performance optimization
   - Cache invalidation

## Debugging

1. Breakpoint Management:
   - Set/remove breakpoints
   - Enable/disable breakpoints
   - Breakpoint navigation
   - Execution control

2. Debug Tools:
   - Variables tray
   - Watch window
   - Call stack navigation
   - Runtime inspection

3. Debug Controls:
   - Continue execution
   - Step Over
   - Step In
   - Step Out
   - Stop debugging

## Advanced Features

### Custom Preview Rendering

1. Viewport Integration:
   - DrawViewportWires
   - DrawViewportMeshes
   - ClippingBox implementation
   - Custom graphics

2. Package Management:
   - PyPI package installation
   - NuGet integration
   - Assembly references
   - Dependency management

3. Shared Scripts:
   - Input script handling
   - Output script sharing
   - Language specifier directives
   - Multi-component coordination

## Publishing and Distribution

1. Component Documentation:
   - Tooltip configuration
   - Parameter descriptions
   - Human-readable names
   - Usage instructions

2. Distribution:
   - User Object creation
   - Icon management
   - Script export
   - Version control

## Best Practices

1. Code Organization:
   - Modular structure
   - Clear naming conventions
   - Documentation standards
   - Error handling patterns

2. Performance:
   - Data structure optimization
   - Cache utilization
   - Memory management
   - Computation efficiency

3. Error Handling:
   - Input validation
   - Exception management
   - User feedback
   - Graceful degradation

## Examples

### Basic Input/Output (Script-Mode)

```python
# Simple addition example
a = x + y
```

### Random Lines Generator (Script-Mode)

```python
"""Generates random lines in 3D space"""
import Rhino.Geometry as rg
import random

# Create random points for line endpoints
lines = []
count = 10  # Number of lines to generate

for i in range(count):
    # Random points in range -10 to 10 for x,y,z
    start = rg.Point3d(
        random.uniform(-10, 10),
        random.uniform(-10, 10),
        random.uniform(-10, 10)
    )
    end = rg.Point3d(
        random.uniform(-10, 10),
        random.uniform(-10, 10),
        random.uniform(-10, 10)
    )
    
    # Create line from points
    line = rg.Line(start, end)
    lines.append(line)

# Assign to output
a = lines
```

### Custom Preview (SDK-Mode)

```python
def DrawViewportWires(self, args):
    # Draw custom preview
    args.Display.DrawPoint(rg.Point3d(0,0,0))
```

### Data Structure Handling

```python
# Tree access example
if isinstance(x, Grasshopper.DataTree[object]):
    for path in x.Paths:
        for item in x.Branch(path):
            # Process item
```

## Tips and Tricks

1. Development Workflow:
   - Use template scripts for quick starts
   - Leverage shared scripts for reusability
   - Implement proper error handling
   - Maintain clear documentation

2. Debugging:
   - Strategic breakpoint placement
   - Watch window for complex objects
   - Call stack for execution flow
   - Variable inspection for state

3. Performance:
   - Cache when appropriate
   - Optimize data structures
   - Minimize type conversions
   - Profile execution time

## Learning from Examples

### Random Lines Case Study

This case study demonstrates common pitfalls and best practices in Grasshopper Python scripting.

1. Initial Attempt (With Issues):
```python
"""Generates random lines in 3D space"""
import Rhino.Geometry as rg
import random

# Problem 1: Using 'range' as both variable name and function
for i in range(int(range)):  # This causes TypeError
    start = rg.Point3d(
        random.uniform(-range, range),
        random.uniform(-range, range),
        random.uniform(-range, range)
    )
```

2. Fixed Version:
```python
"""Generates random lines in 3D space"""
import Rhino.Geometry as rg
import random

# Solution 1: Clear variable naming
# Solution 2: Default value handling
if count is None:
    count = 10
if size is None:  # 'size' instead of 'range'
    size = 10

for i in range(int(count)):
    start = rg.Point3d(
        random.uniform(-size, size),
        random.uniform(-size, size),
        random.uniform(-size, size)
    )
```

### Key Learnings

1. Variable Naming:
   - Avoid using Python built-in names (like 'range', 'list', 'str')
   - Use descriptive names that reflect purpose
   - Be consistent with naming conventions

2. Input Handling:
   - Always check for None values
   - Provide sensible defaults
   - Use clear type hints
   - Validate input ranges

3. Error Prevention:
   - Test with disconnected inputs
   - Handle optional parameters properly
   - Consider edge cases
   - Use clear error messages

4. Code Organization:
   - Group related operations
   - Add descriptive comments
   - Keep functions focused
   - Use consistent formatting

### Common Pitfalls

1. GUID Handling:
   ```python
   # Bad: Using GUID directly
   domain = curve.Domain  # Error: 'Guid' has no attribute 'Domain'
   points = curve.Points  # Error: 'Guid' has no attribute 'Points'
   
   # Good: Convert GUID to geometry first
   import rhinoscriptsyntax as rs
   curve_obj = rs.coercecurve(curve)
   domain = curve_obj.Domain  # Works correctly
   ```

2. Name Conflicts:
   ```python
   # Bad: Using built-in names
   list = [1, 2, 3]  # Shadows built-in list()
   range = 10        # Shadows built-in range()
   
   # Good: Descriptive names
   value_list = [1, 2, 3]
   size_range = 10
   ```

### Working with GUIDs

1. Understanding GUIDs:
   - GUID = Globally Unique Identifier
   - References geometry in Rhino/Grasshopper
   - Cannot access geometry properties directly
   - Must convert to appropriate geometry type

2. Type Hints for GUIDs:
   ```python
   # In Grasshopper component:
   # Right-click input parameter -> Type Hint -> ghdoc Object
   # This ensures proper GUID handling
   ```

3. Converting GUIDs to Geometry:
   ```python
   import rhinoscriptsyntax as rs
   
   # Points
   point = rs.coerce3dpoint(guid)
   
   # Curves
   curve = rs.coercecurve(guid)
   
   # Surfaces
   surface = rs.coercesurface(guid)
   
   # Breps
   brep = rs.coercebrep(guid)
   ```

4. Validation Pattern:
   ```python
   def validate_inputs():
       if curve is None:
           raise ValueError("Input required")
       
       curve_obj = rs.coercecurve(curve)
       if curve_obj is None:
           raise ValueError("Failed to convert to curve")
       
       return curve_obj
   ```

5. Working with Lists:
   ```python
   # Converting multiple GUIDs
   points = [rs.coerce3dpoint(guid) for guid in point_guids]
   
   # Validate conversions
   valid_points = [pt for pt in points if pt is not None]
   ```

6. Real-World Example:
   ```python
   """Distributes points along a curve"""
   import rhinoscriptsyntax as rs
   
   def distribute_points(curve_guid, count=10):
       # Convert GUID to curve
       curve = rs.coercecurve(curve_guid)
       if curve is None:
           raise ValueError("Invalid curve input")
           
       # Now we can use curve methods
       domain = curve.Domain
       points = []
       
       for i in range(count):
           t = domain.ParameterAt(i / (count - 1))
           point = curve.PointAt(t)
           points.append(point)
           
       return points
   ```

2. Name Conflicts:
   ```python
   # Bad: Using built-in names
   list = [1, 2, 3]  # Shadows built-in list()
   range = 10        # Shadows built-in range()
   
   # Good: Descriptive names
   value_list = [1, 2, 3]
   size_range = 10
   ```

### Working with GUIDs

1. Understanding GUIDs:
   - GUID = Globally Unique Identifier
   - Used to reference Rhino geometry
   - Cannot access geometry properties directly
   - Must convert to appropriate object type

2. Type Hints for GUIDs:
   ```python
   # In Grasshopper component:
   # Right-click input -> Type Hint -> ghdoc Object
   ```

3. Converting GUIDs:
   ```python
   import rhinoscriptsyntax as rs
   
   # Curves
   curve_obj = rs.coercecurve(guid)
   
   # Points
   point_obj = rs.coerce3dpoint(guid)
   
   # Surfaces
   surface_obj = rs.coercesurface(guid)
   
   # Breps
   brep_obj = rs.coercebrep(guid)
   ```

4. Best Practices:
   - Always validate GUID conversion results
   - Use appropriate coerce function for geometry type
   - Handle conversion failures gracefully
   ```python
   def validate_inputs():
       if curve is None:
           raise ValueError("Input curve is required")
       
       curve_obj = rs.coercecurve(curve)
       if curve_obj is None:
           raise ValueError("Failed to convert input to curve")
       
       return curve_obj
   ```

5. Common GUID Operations:
   ```python
   # Check if GUID exists
   if rs.IsObject(guid):
       # Object exists in document
   
   # Get object type
   obj_type = rs.ObjectType(guid)
   
   # Get object attributes
   attrs = rs.ObjectAttributes(guid)
   
   # Delete object
   rs.DeleteObject(guid)
   ```

6. Real-World Example:
   ```python
   """Distributes points along a curve"""
   import rhinoscriptsyntax as rs
   
   def process_curve(curve_guid, count=10):
       # Convert GUID to curve
       curve = rs.coercecurve(curve_guid)
       if curve is None:
           raise ValueError("Invalid curve input")
           
       # Now we can use curve methods
       domain = curve.Domain
       points = [curve.PointAt(t) for t in 
                rs.frange(domain.Min, domain.Max, count)]
       return points
   ```

2. Missing Input Validation:
   ```python
   # Bad: No validation
   result = x + y  # Crashes if x or y is None
   
   # Good: With validation
   if x is None or y is None:
       x = 0 if x is None else x
       y = 0 if y is None else y
   result = x + y
   ```

3. Type Safety Issues:
   ```python
   # Bad: Unsafe type usage
   value = count  # Might be None or non-numeric
   
   # Good: Safe type conversion
   try:
       value = int(count) if count is not None else 10
   except (ValueError, TypeError):
       value = 10  # Default fallback
   ```

## Common Issues and Solutions

1. Type Conversion:
   - Use appropriate type hints
   - Handle null values
   - Validate input data
   - Implement error checking

2. Memory Management:
   - Clear large objects
   - Dispose of resources
   - Monitor memory usage
   - Implement cleanup

3. Error Handling:
   - Validate inputs
   - Provide clear messages
   - Handle edge cases
   - Log errors appropriately

---

This guide serves as a comprehensive reference for creating Python components in Grasshopper. For specific use cases or additional details, refer to the official Rhino Developer Documentation.
