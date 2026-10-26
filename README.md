# 3D-modelling
creating 3d modeling techniques for digital purpose

import cadquery as cq

# -----------------------------
# PISTON PARAMETERS (mm)
# -----------------------------
piston_diameter = 80.0
piston_height = 60.0
ring_groove_width = 2.0
ring_groove_depth = 2.5
ring_positions = [43.0, 48.0, 53.0]
wrist_pin_diameter = 20.0
wrist_pin_height = 22.0

radius = piston_diameter / 2

# -----------------------------
# 1. Create piston body
# -----------------------------
piston = (
    cq.Workplane("XY")
    .circle(radius)
    .extrude(piston_height)
)

# -----------------------------
# 2. Cut piston ring grooves
# -----------------------------
for z in ring_positions:
    groove_outer = (
        cq.Workplane("XY")
        .workplane(offset=z)
        .circle(radius + 0.5)
        .extrude(ring_groove_width)
    )

    groove_inner = (
        cq.Workplane("XY")
        .workplane(offset=z)
        .circle(radius - ring_groove_depth)
        .extrude(ring_groove_width)
    )

    groove_tool = groove_outer.cut(groove_inner)
    piston = piston.cut(groove_tool)

# -----------------------------
# 3. Create wrist-pin bore
# -----------------------------
pin_bore = (
    cq.Workplane("YZ")
    .center(0, wrist_pin_height)
    .circle(wrist_pin_diameter / 2)
    .extrude(100, both=True)
)

piston = piston.cut(pin_bore)

# -----------------------------
# 4. Export the 3D model
# -----------------------------
piston.export("piston.step")
piston.export("piston.stl")

print("Piston model created!")
print("Exported: piston.step and piston.stl")
