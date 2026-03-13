# coordstometers

[![PyPI](https://img.shields.io/pypi/v/coordstometers)](https://pypi.org/project/coordstometers/)

A Python package ([available on PyPI](https://pypi.org/project/coordstometers/)) that converts GPS coordinates (lat/lon) to a local meter-based coordinate system centered at a reference point, and back.

If you just need the distance between two points, the [haversine](https://pypi.org/project/haversine/) package works great. But many spatial problems go beyond point-to-point distance — finding the area of a polygon, plotting a route with proper scaling, detecting when a point is within X meters of a line, or doing any kind of 2D geometry. These operations are much easier when you convert GPS coordinates to a flat 2D plane with familiar distance units like meters or feet, do your math there, and convert back.

## Installation

```bash
pip install coordstometers
```

## Usage

```python
import numpy as np
from coordstometers import coords_to_meters, meters_to_coords

# Define some GPS points (lat, lon)
points = np.array([
    [39.951382, -75.180691],
    [39.946669, -75.142810],
])

# Choose a center point (e.g. the mean)
center = np.mean(points, axis=0)

# Convert to meters
pts_meters = coords_to_meters(points, center)

# Do your spatial math (distances, areas, etc.)
distance = np.linalg.norm(pts_meters[0] - pts_meters[1])
print(f"Distance: {distance:.0f} meters")

# Convert back to GPS coordinates
pts_gps = meters_to_coords(pts_meters, center)
```

## API

### `coords_to_meters(pts_lat_lon, center_coord)`

Converts an `(n, 2)` array of lat/lon points to meters relative to `center_coord`.

Returns an `(n, 2)` array in `(x, y)` meter coordinates where x is east-west and y is north-south.

### `meters_to_coords(pts_meters, center_coord)`

Inverse of `coords_to_meters`. Converts `(n, 2)` meter coordinates back to lat/lon.

### `get_scale(center_coord)`

Returns the `(lat_scale, lon_scale)` factors (meters per degree) at the given coordinate.

## How it works

Approximates the Earth's surface as locally flat near the center point. Latitude degrees are converted at a constant rate (~111 km/degree), while longitude degrees are scaled by `cos(latitude)` to account for meridian convergence. Accurate within a few km of the center point.

## License

MIT
