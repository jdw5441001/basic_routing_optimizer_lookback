# Technician Route Optimization - Proximity Problem Counter Factual

## Overview

This Databricks notebook solves a route optimization problem for field technician scheduling and routing. It assigns technicians to customer jobs and optimizes their daily routes to minimize total travel distance using a Traveling Salesman Problem (TSP) nearest neighbor algorithm.

## Problem Statement

The notebook addresses the following challenges:
- Assigning technicians to customer service jobs across a weekly schedule
- Ensuring balanced workload distribution (4 jobs per tech per day: 2 morning, 2 afternoon)
- Optimizing travel routes to minimize distance traveled
- Comparing original routes against optimized routes

## Features

### 1. **Job Assignment**
- Randomly assigns technicians to customer jobs
- Enforces constraints:
  - 4 jobs per technician per day
  - 2 jobs per time slot (morning/afternoon)
  - Balanced distribution across the technician pool

### 2. **Route Optimization**
- Implements TSP nearest neighbor algorithm
- Uses Haversine distance formula for geographic calculations
- Optimizes routes starting from a fixed location (Raleigh, NC coordinates: 35.780175, -78.633199)
- Provides optimized job sequences to minimize travel distance

### 3. **Distance Calculation**
- Haversine distance formula for accurate geographic distance calculations
- Distance matrix generation for route optimization
- Comparison of original vs. optimized total distances

## Data Requirements

The notebook expects three CSV files in `/Volumes/datasets/default/data_for_tech_opt/`:

1. **customer_table.csv** - Customer information including:
   - Customer_ID
   - Latitude
   - Longitude
   - Other customer details

2. **tech_table.csv** - Technician information including:
   - Technician (identifier)
   - Other technician details

3. **customer_schedule.csv** - Job schedule including:
   - Customer_ID
   - Day_of_Week
   - Arrival_Time_Slot (Morning/Afternoon)
   - Job_Type

## Dependencies

```python
# PySpark
from pyspark.sql import functions as F
from pyspark.sql.functions import col

# Python Standard Library
import numpy as np
import pandas as pd
from math import radians, cos, sin, asin, sqrt
from collections import defaultdict
```

## Key Functions

### `assign_technicians(schedule_df, technicians)`
Assigns technicians to jobs with workload constraints.

**Parameters:**
- `schedule_df`: DataFrame containing job schedule
- `technicians`: Array of technician identifiers

**Returns:**
- DataFrame with assigned technicians

**Constraints:**
- Each tech gets 4 jobs per day
- 2 jobs in morning slot, 2 in afternoon slot

### `haversine_distance(lat1, lon1, lat2, lon2)`
Calculates the distance between two geographic coordinates using the Haversine formula.

**Parameters:**
- `lat1, lon1`: Latitude and longitude of first point
- `lat2, lon2`: Latitude and longitude of second point

**Returns:**
- Distance in miles

### `tsp_nearest_neighbor(distance_matrix, start=0)`
Solves the Traveling Salesman Problem using the nearest neighbor heuristic.

**Parameters:**
- `distance_matrix`: 2D array of distances between locations
- `start`: Starting node index

**Returns:**
- Tuple of (route_indices, total_distance)

### `create_distance_matrix(coordinates)`
Creates a distance matrix from a list of coordinate pairs.

**Parameters:**
- `coordinates`: List of (latitude, longitude) tuples

**Returns:**
- 2D numpy array of distances

### `optimize_routes(df)`
Main optimization function that processes technician schedules and optimizes routes.

**Parameters:**
- `df`: DataFrame with columns: Assigned_Tech, Day_of_Week, Latitude, Longitude, cumulative_job_number

**Returns:**
- Dictionary containing optimized routes for each technician-day combination

## Workflow

1. **Data Loading**: Import customer, technician, and schedule data
2. **Job Assignment**: Randomly assign technicians to jobs with constraints
3. **Data Preparation**: Join datasets and sort by technician, day, and job order
4. **Route Optimization**: Apply TSP algorithm to each technician's daily schedule
5. **Results Comparison**: Compare original vs. optimized distances

## Output

The notebook produces:
- `optimized_route_df`: Final DataFrame containing:
  - Day_of_Week
  - Assigned_Tech
  - total_expected_rev (if available)
  - total_miles (original distance)
  - total_optimized_distance_mi (optimized distance)

## Configuration

Starting location (depot):
```python
STARTING_LAT = 35.780175  # Raleigh, NC
STARTING_LON = -78.633199
```

Random seed for reproducibility:
```python
np.random.seed(42)
```

## Usage

1. Ensure all required CSV files are in the specified Volumes path
2. Run cells sequentially in Databricks
3. Review optimization results in `optimized_route_df`
4. Compare original vs. optimized distances to measure efficiency gains

## Algorithm Notes

The TSP nearest neighbor algorithm is a greedy heuristic that:
- Starts at a specified location
- Repeatedly visits the nearest unvisited location
- Returns to start after visiting all locations
- Provides a good (but not guaranteed optimal) solution efficiently

For larger problem sizes, consider more sophisticated algorithms like:
- 2-opt or 3-opt improvements
- Genetic algorithms
- Simulated annealing
- Exact solvers (for small instances)

## Performance Considerations

- The nearest neighbor algorithm has O(n²) time complexity
- Suitable for daily technician schedules (typically 4-10 jobs)
- For larger route sets, consider parallelization or more efficient algorithms

## Future Enhancements

- Time window constraints
- Technician skill-based assignment
- Traffic-aware routing
- Multi-depot optimization
- Real-time route adjustments

## License

[Specify your license here]

## Contact

[Specify contact information or team]
