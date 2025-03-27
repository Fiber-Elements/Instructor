# Grasshopper Python Syntax Cheatsheet

A quick reference guide for common Grasshopper Python scripting patterns, functions, and best practices.

## Table of Contents
- [Core Imports](#core-imports)
- [GUID Conversion Reference](#guid-conversion-reference)
- [Geometry Creation](#geometry-creation)
- [Geometry Manipulation](#geometry-manipulation)
- [Data Structures](#data-structures)
- [Input/Output Handling](#inputoutput-handling)
- [Error Handling](#error-handling)
- [Common Algorithms](#common-algorithms)
- [Performance Tips](#performance-tips)
- [Quick Reference Tables](#quick-reference-tables)

## Core Imports

```python
# Essential imports
import rhinoscriptsyntax as rs      # RhinoScript functions
import Rhino.Geometry as rg         # Rhino geometry classes
import ghpythonlib.components as ghc # Grasshopper components
import Grasshopper                   # Grasshopper namespace (for data trees)
import scriptcontext as sc           # Script context (for document access)
import System                        # .NET System namespace
import math                          # Python math functions
import random                        # Python random functions
```

## GUID Conversion Reference

| Geometry Type | Conversion Function | Example |
|---------------|---------------------|---------|
| Point | `rs.coerce3dpoint(guid)` | `point = rs.coerce3dpoint(point_guid)` |
| Vector | `rs.coerce3dvector(guid)` | `vector = rs.coerce3dvector(vector_guid)` |
| Line | `rs.coerceline(guid)` | `line = rs.coerceline(line_guid)` |
| Curve | `rs.coercecurve(guid)` | `curve = rs.coercecurve(curve_guid)` |
| Surface | `rs.coercesurface(guid)` | `surface = rs.coercesurface(surface_guid)` |
| Brep | `rs.coercebrep(guid)` | `brep = rs.coercebrep(brep_guid)` |
| Mesh | `rs.coercemesh(guid)` | `mesh = rs.coercemesh(mesh_guid)` |
| Plane | `rs.coerceplane(guid)` | `plane = rs.coerceplane(plane_guid)` |

**Validation Pattern:**
```python
curve_obj = rs.coercecurve(curve_guid)
if curve_obj is None:
    raise ValueError("Invalid curve geometry")
```

## Geometry Creation

### Points and Vectors

```python
# Create a point
point = rg.Point3d(x, y, z)
point_from_list = rg.Point3d(*[x, y, z])  # Unpacking a list

# Create a vector
vector = rg.Vector3d(x, y, z)
unit_vector = vector.Unitize()  # Normalize to length 1

# Vector operations
length = vector.Length
dot_product = vector1 * vector2
cross_product = rg.Vector3d.CrossProduct(vector1, vector2)
```

### Lines and Curves

```python
# Create a line
line = rg.Line(start_point, end_point)
line_from_points = rg.Line(rg.Point3d(x1, y1, z1), rg.Point3d(x2, y2, z2))

# Create a polyline
points = [rg.Point3d(0,0,0), rg.Point3d(1,1,0), rg.Point3d(2,0,0)]
polyline = rg.Polyline(points)

# Create a circle
circle = rg.Circle(center_point, radius)
circle_from_plane = rg.Circle(rg.Plane.WorldXY, radius)

# Create an arc
arc = rg.Arc(start_point, mid_point, end_point)
arc_from_circle = rg.Arc(circle, angle_in_radians)

# Create a NURBS curve
degree = 3
points = [rg.Point3d(0,0,0), rg.Point3d(1,1,0), rg.Point3d(2,0,0)]
curve = rg.NurbsCurve.Create(periodic=False, degree=degree, points=points)

# Create an interpolated curve
points = [rg.Point3d(0,0,0), rg.Point3d(1,1,0), rg.Point3d(2,0,0)]
curve = rg.Curve.CreateInterpolatedCurve(points, degree=3)
```

### Surfaces and Breps

```python
# Create a plane
plane = rg.Plane(origin, x_direction, y_direction)
plane_from_points = rg.Plane(point1, point2, point3)
plane_from_normal = rg.Plane(origin, normal)

# Create a surface from corner points
surface = rg.NurbsSurface.CreateFromCorners(
    rg.Point3d(0,0,0), rg.Point3d(10,0,0),
    rg.Point3d(10,10,0), rg.Point3d(0,10,0)
)

# Create a surface from points
points = [[rg.Point3d(i,j,0) for j in range(5)] for i in range(5)]
surface = rg.NurbsSurface.CreateFromPoints(points, 3, 3, False, False)

# Create a surface from loft
curves = [circle1, circle2, circle3]  # List of curves
surface = ghc.Loft(curves)

# Create a box
box = rg.Box(plane, rg.Interval(-5, 5), rg.Interval(-5, 5), rg.Interval(-5, 5))

# Create a sphere
sphere = rg.Sphere(center, radius)
```

### Meshes

```python
# Create an empty mesh
mesh = rg.Mesh()

# Add vertices
mesh.Vertices.Add(0, 0, 0)
mesh.Vertices.Add(1, 0, 0)
mesh.Vertices.Add(1, 1, 0)
mesh.Vertices.Add(0, 1, 0)

# Add faces (triangles or quads)
mesh.Faces.AddFace(0, 1, 2, 3)  # Quad face
mesh.Faces.AddFace(0, 1, 2)     # Triangle face

# Create mesh from surface
mesh = rg.Mesh.CreateFromBrep(brep, meshingParameters)[0]

# Create mesh from points
mesh = ghc.Delaunay(points)
```

## Geometry Manipulation

### Transformations

```python
# Translation
translation = rg.Transform.Translation(x, y, z)
translation_vector = rg.Transform.Translation(vector)
geometry.Transform(translation)

# Rotation
rotation = rg.Transform.Rotation(angle_in_radians, axis, center_point)
geometry.Transform(rotation)

# Scale
scale = rg.Transform.Scale(center_point, scale_factor)
non_uniform_scale = rg.Transform.Scale(center_point, x_scale, y_scale, z_scale)
geometry.Transform(scale)

# Mirror
mirror = rg.Transform.Mirror(mirror_point, mirror_normal)
geometry.Transform(mirror)

# Combine transformations
transform = rg.Transform.Identity
transform *= translation
transform *= rotation
geometry.Transform(transform)
```

### Boolean Operations

```python
# Boolean union
union = ghc.BrepBoolean(brep_a, brep_b, "Union")

# Boolean difference
difference = ghc.BrepBoolean(brep_a, brep_b, "Difference")

# Boolean intersection
intersection = ghc.BrepBoolean(brep_a, brep_b, "Intersection")

# Mesh boolean operations
mesh_union = ghc.MeshBooleanUnion(mesh_a, mesh_b)
mesh_difference = ghc.MeshBooleanDifference(mesh_a, mesh_b)
mesh_intersection = ghc.MeshBooleanIntersection(mesh_a, mesh_b)
```

## Data Structures

### Lists and Arrays

```python
# Create a list
points = [rg.Point3d(i, 0, 0) for i in range(10)]

# List operations
first_item = points[0]
last_item = points[-1]
subset = points[2:5]  # Items at index 2, 3, and 4
reversed_list = points[::-1]

# List comprehension
squared = [x**2 for x in range(10)]
filtered = [x for x in range(20) if x % 2 == 0]
```

### Data Trees

```python
# Check if input is a data tree
if isinstance(x, Grasshopper.DataTree[object]):
    # Access paths
    for path in x.Paths:
        # Access items in a branch
        branch = x.Branch(path)
        for item in branch:
            # Process item
            print(item)

# Create a new data tree
tree = Grasshopper.DataTree[object]()

# Add data to tree
path = Grasshopper.Kernel.Data.GH_Path(0, 1)  # Path with indices 0,1
tree.Add("value", path)

# Add a list to a branch
path = Grasshopper.Kernel.Data.GH_Path(0)
for item in some_list:
    tree.Add(item, path)
```

## Input/Output Handling

### Input Validation

```python
# Check for None
if input_value is None:
    raise ValueError("Input is required")

# Set default values
count = 10 if count is None else int(count)
include_ends = True if include_ends is None else bool(include_ends)

# Safe type conversion
try:
    value = int(count) if count is not None else 10
except (ValueError, TypeError):
    value = 10  # Default fallback

# Range validation
if count < 2:
    raise ValueError("Count must be at least 2")
if radius <= 0:
    raise ValueError("Radius must be positive")
```

### Multiple Input Processing

```python
# Process list of inputs
if isinstance(points, list):
    # Convert all GUIDs to points
    points = [rs.coerce3dpoint(pt) for pt in points]
    # Filter out None values
    points = [pt for pt in points if pt is not None]
else:
    # Single input
    point = rs.coerce3dpoint(points)
    points = [point] if point is not None else []
```

## Error Handling

### Basic Error Handling

```python
# Initialize outputs
a = None  # result1
b = None  # result2

try:
    # Main logic here
    a = process_data()
    b = calculate_values()
except Exception as e:
    import Rhino
    Rhino.RhinoApp.WriteLine(f"Error: {str(e)}")
    # Optionally, provide more specific error handling
    if "division by zero" in str(e):
        Rhino.RhinoApp.WriteLine("Cannot divide by zero")
    
    # Reset outputs on error
    a = None
    b = None
```

### Custom Error Messages

```python
# Validation with custom errors
if curve is None:
    raise ValueError("Curve input is required")

if count < 2:
    raise ValueError("Count must be at least 2")

# Type checking
if not isinstance(value, (int, float)):
    raise TypeError("Value must be a number")
```

## Common Algorithms

### Curve Operations

```python
# Divide curve by count
def divide_curve_count(curve, count):
    domain = curve.Domain
    params = [domain.ParameterAt(i / (count - 1)) for i in range(count)]
    points = [curve.PointAt(t) for t in params]
    return points, params

# Divide curve by length
def divide_curve_length(curve, segment_length):
    curve_length = curve.GetLength()
    count = max(2, int(curve_length / segment_length) + 1)
    return divide_curve_count(curve, count)

# Get curve parameter at point
def get_curve_parameter(curve, point):
    success, t = curve.ClosestPoint(point)
    if success:
        return t
    return None

# Evaluate curve at parameter
def evaluate_curve(curve, t):
    point = curve.PointAt(t)
    tangent = curve.TangentAt(t)
    curvature = curve.CurvatureAt(t)
    return point, tangent, curvature
```

### Surface Operations

```python
# Evaluate surface at parameters
def evaluate_surface(surface, u, v):
    point = surface.PointAt(u, v)
    normal = surface.NormalAt(u, v)
    return point, normal

# Create surface grid of points
def surface_point_grid(surface, u_count, v_count):
    u_domain = surface.Domain(0)
    v_domain = surface.Domain(1)
    
    points = []
    for i in range(u_count):
        u = u_domain.ParameterAt(i / (u_count - 1))
        row = []
        for j in range(v_count):
            v = v_domain.ParameterAt(j / (v_count - 1))
            point = surface.PointAt(u, v)
            row.append(point)
        points.append(row)
    return points
```

### Mesh Operations

```python
# Get mesh vertices and faces
def get_mesh_data(mesh):
    vertices = [mesh.Vertices[i] for i in range(mesh.Vertices.Count)]
    faces = []
    for i in range(mesh.Faces.Count):
        face = mesh.Faces[i]
        if face.IsQuad:
            faces.append([face.A, face.B, face.C, face.D])
        else:
            faces.append([face.A, face.B, face.C])
    return vertices, faces

# Calculate mesh face centers
def get_mesh_face_centers(mesh):
    centers = []
    for i in range(mesh.Faces.Count):
        center = mesh.Faces.GetFaceCenter(i)
        centers.append(center)
    return centers

# Calculate mesh face normals
def get_mesh_face_normals(mesh):
    normals = []
    for i in range(mesh.Faces.Count):
        normal = mesh.FaceNormals[i]
        normals.append(normal)
    return normals
```

## Performance Tips

### List Comprehensions

```python
# Instead of:
points = []
for i in range(count):
    t = domain.ParameterAt(i / (count - 1))
    point = curve.PointAt(t)
    points.append(point)

# Use:
points = [curve.PointAt(domain.ParameterAt(i / (count - 1))) for i in range(count)]
```

### Efficient Geometry Creation

```python
# Pre-allocate arrays for large meshes
mesh = rg.Mesh()
mesh.Vertices.Capacity = vertex_count
mesh.Faces.Capacity = face_count

# Use bulk operations when possible
mesh.Vertices.AddVertices(points)

# Avoid redundant calculations
domain = curve.Domain  # Store domain once, use many times
length = curve.GetLength()  # Calculate length once
```

### Memory Management

```python
# Dispose of large objects when done
if large_object:
    large_object.Dispose()
    large_object = None

# Clear lists when no longer needed
large_list.Clear()
large_list = None
```

## Quick Reference Tables

### Point3d Properties and Methods

| Property/Method | Description | Example |
|-----------------|-------------|---------|
| `X`, `Y`, `Z` | Coordinate components | `point.X` |
| `DistanceTo()` | Distance to another point | `point1.DistanceTo(point2)` |
| `Subtract()` | Vector from this point to another | `vector = rg.Point3d.Subtract(point1, point2)` |
| `Add()` | Add a vector to a point | `new_point = rg.Point3d.Add(point, vector)` |
| `Origin` | Static point at origin | `origin = rg.Point3d.Origin` |

### Vector3d Properties and Methods

| Property/Method | Description | Example |
|-----------------|-------------|---------|
| `X`, `Y`, `Z` | Vector components | `vector.X` |
| `Length` | Vector length | `length = vector.Length` |
| `Unitize()` | Normalize to unit length | `unit_vector = vector.Unitize()` |
| `CrossProduct()` | Cross product of two vectors | `cross = rg.Vector3d.CrossProduct(v1, v2)` |
| `DotProduct()` | Dot product of two vectors | `dot = rg.Vector3d.DotProduct(v1, v2)` |

### Curve Properties and Methods

| Property/Method | Description | Example |
|-----------------|-------------|---------|
| `Domain` | Parameter domain | `domain = curve.Domain` |
| `GetLength()` | Get curve length | `length = curve.GetLength()` |
| `PointAt()` | Get point at parameter | `point = curve.PointAt(t)` |
| `TangentAt()` | Get tangent at parameter | `tangent = curve.TangentAt(t)` |
| `ClosestPoint()` | Find closest point | `success, t = curve.ClosestPoint(test_point)` |
| `Reverse()` | Reverse curve direction | `reversed_curve = curve.Reverse()` |
| `Offset()` | Offset curve | `offset_curves = curve.Offset(plane, distance, tolerance, corner_style)` |

### Surface Properties and Methods

| Property/Method | Description | Example |
|-----------------|-------------|---------|
| `Domain(0)`, `Domain(1)` | U and V domains | `u_domain = surface.Domain(0)` |
| `PointAt()` | Get point at parameters | `point = surface.PointAt(u, v)` |
| `NormalAt()` | Get normal at parameters | `normal = surface.NormalAt(u, v)` |
| `ClosestPoint()` | Find closest point | `success, u, v = surface.ClosestPoint(test_point)` |
| `IsoCurve()` | Extract isocurve | `curve = surface.IsoCurve(0, u)` # U-direction |

### Math Operations

| Operation | Description | Example |
|-----------|-------------|---------|
| `math.sin()`, `math.cos()` | Trigonometric functions | `y = math.sin(angle)` |
| `math.radians()` | Convert degrees to radians | `rad = math.radians(degrees)` |
| `math.degrees()` | Convert radians to degrees | `deg = math.degrees(radians)` |
| `math.pi` | Pi constant | `circumference = 2 * math.pi * radius` |
| `math.sqrt()` | Square root | `diagonal = math.sqrt(x*x + y*y)` |
| `random.random()` | Random number [0.0, 1.0) | `r = random.random()` |
| `random.uniform()` | Random in range | `r = random.uniform(min, max)` |

---

This cheatsheet provides a quick reference for common Grasshopper Python scripting patterns. For more detailed information, refer to the comprehensive Grasshopper Python documentation.
