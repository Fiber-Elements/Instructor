# Grasshopper Python Component Generator Prompt

## Task Description
Create a Python script component for Grasshopper that follows best practices for stability, error handling, and performance. The component should use the direct script approach and handle all inputs and outputs properly.

## Component Structure Template
```python
"""
[COMPONENT_NAME]
[BRIEF_DESCRIPTION]
Inputs:
    [INPUT_NAME_1]: [INPUT_DESCRIPTION_1]
    [INPUT_NAME_2]: [INPUT_DESCRIPTION_2]
    ...
Outputs:
    [OUTPUT_NAME_1]: [OUTPUT_DESCRIPTION_1]
    [OUTPUT_NAME_2]: [OUTPUT_DESCRIPTION_2]
    ...
"""
import rhinoscriptsyntax as rs
import Rhino.Geometry as rg
import ghpythonlib.components as ghc
# Import any other required libraries

# 1. Initialize all outputs to empty lists or None
[OUTPUT_1] = []  # or None if not a list
[OUTPUT_2] = []  # or None if not a list

try:
    # 2. Validate inputs and set defaults
    # Check for None values and provide sensible defaults
    # Example:
    if [INPUT_1] is None:
        raise ValueError("Input required")
    [INPUT_2] = [DEFAULT_VALUE] if [INPUT_2] is None else [INPUT_2]
    
    # 3. Convert GUIDs to geometry if needed
    # Example:
    [GEOMETRY] = rs.coerce[TYPE]([INPUT])
    if [GEOMETRY] is None:
        raise ValueError("Invalid geometry")
    
    # 4. Main logic
    # Implement the component's functionality
    # Use Rhino/Grasshopper built-in functions when possible
    # Use list comprehensions for concise operations
    
    # 5. Assign results to output variables
    [OUTPUT_1] = [RESULT_1]
    [OUTPUT_2] = [RESULT_2]
    
except Exception as e:
    import Rhino
    Rhino.RhinoApp.WriteLine(f"Error: {str(e)}")
    # Reset outputs on error
    [OUTPUT_1] = []  # or None if not a list
    [OUTPUT_2] = []  # or None if not a list
```

## Critical Requirements

1. **Documentation**
   - Include docstring at the top with component name, description, inputs, and outputs
   - Add inline comments for complex operations

2. **Input Handling**
   - Always check for None values
   - Provide sensible defaults for optional inputs
   - Use safe type conversion: `count = int(count) if count is not None else 10`
   - Validate input ranges and types

3. **GUID Conversion**
   - Always convert GUIDs to geometry before using: `curve = rs.coercecurve(curve_guid)`
   - Validate conversion results: `if curve is None: raise ValueError("Invalid curve")`
   - Use appropriate coerce function for each geometry type:
     - `rs.coerce3dpoint()` for points
     - `rs.coercecurve()` for curves
     - `rs.coercesurface()` for surfaces
     - `rs.coercebrep()` for breps
     - `rs.coercemesh()` for meshes

4. **Error Handling**
   - Wrap main logic in try/except block
   - Report errors to console: `Rhino.RhinoApp.WriteLine(f"Error: {str(e)}")`
   - Return initialized outputs even on error
   - Use clear, descriptive error messages: `raise ValueError("Invalid input")`

5. **Output Management**
   - Initialize ALL outputs to None at the start
   - Ensure all outputs are assigned values before returning
   - Return outputs in the correct order

6. **Code Efficiency**
   - Use Rhino/Grasshopper built-in functions instead of custom logic
   - Use list comprehensions for concise operations
   - Leverage Rhino.Geometry namespace for geometric operations
   - Keep code focused and modular

7. **Variable Naming**
   - Use descriptive parameter names and variables
   - Avoid using Python built-ins as variable names (`list`, `range`, `str`)
   - Be consistent with naming conventions

## Common Pitfalls to Avoid

1. **Name Conflicts**: Don't use Python built-ins as variable names
2. **Type Safety**: Don't assume input types, use safe conversion patterns
3. **Missing Validation**: Don't skip input validation, don't assume inputs are connected
4. **GUID Handling**: Don't use GUID properties directly, always convert first
5. **Performance Issues**: Use list comprehensions and built-in functions

## Example Component

```python
"""
Curve Divider
Divides a curve into equal segments and returns points and parameters
Inputs:
    curve: Curve to divide
    count: Number of divisions (default: 10)
    include_ends: Include endpoints (default: True)
Outputs:
    a: Points along the curve
    b: Parameter values at division points
    c: Tangent vectors at division points
"""
import rhinoscriptsyntax as rs
import Rhino.Geometry as rg

# Initialize outputs
a = []  # points
b = []  # params
c = []  # tangents

try:
    # Validate inputs with defaults
    if curve is None:
        raise ValueError("Curve input is required")
    
    count = 10 if count is None else int(count)
    include_ends = True if include_ends is None else bool(include_ends)
    
    if count < 2:
        raise ValueError("Count must be at least 2")
    
    # Convert GUID to geometry if needed
    curve_obj = rs.coercecurve(curve)
    if curve_obj is None:
        raise ValueError("Invalid curve geometry")
    
    # Main logic
    domain = curve_obj.Domain
    
    # Calculate division parameters
    if include_ends:
        # Include start and end points
        division_count = count - 1
        t_values = [domain.ParameterAt(i / division_count) for i in range(count)]
    else:
        # Exclude start and end points
        division_count = count + 1
        t_values = [domain.ParameterAt((i + 1) / division_count) for i in range(count)]
    
    # Calculate points and tangents at parameters
    a = [curve_obj.PointAt(t) for t in t_values]  # points
    c = [curve_obj.TangentAt(t) for t in t_values]  # tangents
    b = t_values  # params
    
except Exception as e:
    import Rhino
    Rhino.RhinoApp.WriteLine(f"Error: {str(e)}")
    # Reset outputs on error
    a = []
    b = []
    c = []
```

## Instructions for Use

1. Copy the template and modify it for your specific component
2. Replace placeholders with actual values
3. Implement the main logic for your component
4. Test with various input conditions, including None values
5. Ensure proper error handling and input validation
