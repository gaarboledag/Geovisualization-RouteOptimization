# Geovisualization and Route Optimization

This repository provides two Jupyter Notebooks for geovisualization and route optimization in any city (default: Bogotá, Colombia). The projects use open-source tools to map administrative localities, points of interest (POIs), and optimized routes for POIs grouped by category, starting and ending at base locations. The route optimization is powered by the **Nearest Neighbor algorithm** for solving the **Traveling Salesman Problem (TSP)**, ensuring efficient and practical route planning.

## Projects

1. **Geovisualization** (`notebooks/geovisualization.ipynb`):

   - Visualizes administrative localities and points of interest on an interactive map.
   - Uses GeoPandas for shapefile handling and Folium for mapping.
   - Output: `outputs/mapa_interactivo_puntos_interes.html`

2. **Route Optimization** (`notebooks/route_optimization.ipynb`):

   - Extends the geovisualization to include optimized routes for POIs, grouped by category, using the Nearest Neighbor algorithm for the TSP.
   - Uses OSMnx for street network data and NetworkX for routing.
   - Output: `outputs/mapa_interactivo_rutas_optimizadas.html`

## Repository Structure

```
geovisualization/
├── data/
│   ├── localities/
│   │   ├── localities.shp
│   │   ├── localities.shx
│   │   ├── localities.dbf
│   │   └── localities.prj
│   └── puntos_interes.csv
├── notebooks/
│   ├── geovisualization.ipynb
│   └── route_optimization.ipynb
├── outputs/
│   ├── mapa_interactivo_puntos_interes.html
│   └── mapa_interactivo_rutas_optimizadas.html
├── requirements.txt
├── README.md
└── LICENSE
```

## Setup

### Prerequisites

- Python 3.8+
- pip for installing dependencies
- Jupyter Notebook or JupyterLab

### Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/your-username/geovisualization.git
   cd geovisualization
   ```

2. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

3. Ensure the `data/` directory contains:

   - `puntos_interes.csv`: A CSV file with POI data (columns: `Latitud`, `Longitud`, `Nombre`, `Categoría`). A sample file is provided for testing.
   - `localities/localities.shp`: A shapefile with administrative boundaries (optional; the notebooks handle missing shapefiles gracefully).

### Sample Dataset

A sample `puntos_interes.csv` is included in `data/` for testing, with 12 POIs across three categories (`Turismo`, `Comercio`, `Servicios`), each with a base location marked by "BASE" in the `Nombre` column. Example:

```csv
Latitud,Longitud,Nombre,Categoría
4.6951,-74.0733,Parque Simón Bolívar,Turismo
4.6762,-74.0845,Museo Nacional,Turismo
4.6890,-74.0698,Jardín Botánico,Turismo
4.6900,-74.0750,Base Turismo,Turismo
...
```

To use your own data, replace `puntos_interes.csv` with a CSV file following the same structure.

## Usage

1. Open a notebook in Jupyter:

   ```bash
   jupyter notebook notebooks/geovisualization.ipynb
   ```

   or

   ```bash
   jupyter notebook notebooks/route_optimization.ipynb
   ```

2. Run all cells to generate the interactive map.

3. View the output HTML file in the `outputs/` directory (e.g., `mapa_interactivo_puntos_interes.html` or `mapa_interactivo_rutas_optimizadas.html`) in a web browser.

## Optimization Method: Nearest Neighbor Algorithm for TSP

### Overview

The `route_optimization.ipynb` notebook uses the **Nearest Neighbor algorithm** to solve the **Traveling Salesman Problem (TSP)**, optimizing routes for each category of POIs. The TSP aims to find the shortest route that visits each point exactly once and returns to the starting point (the base location).

### How It Works

The Nearest Neighbor algorithm is a greedy heuristic that:

1. Starts at the base location (e.g., "Base Turismo").
2. Repeatedly selects the closest unvisited point to the current point, based on real-world street distances.
3. Continues until all points are visited.
4. Returns to the base location to complete the route.

### Implementation

- **Distance Matrix**: The `generar_matriz_distancias` function computes the shortest path distances between all pairs of points using OSMnx's street network and NetworkX's `shortest_path_length`. Distances reflect actual road lengths, ensuring realistic routes.
- **TSP Solver**: The `tsp_vecino_mas_cercano` function implements the Nearest Neighbor algorithm, selecting the closest unvisited point at each step and calculating the total route distance.
- **Route Visualization**: The `add_optimized_routes` function draws the route as a polyline on the Folium map, following the street network's geometry for each segment.
- **Error Handling**: The code handles cases like missing paths or insufficient points, logging warnings and skipping invalid segments.

### Why Nearest Neighbor?

The Nearest Neighbor algorithm is chosen for its:

- **Efficiency**: With a time complexity of O(n²), it is fast for small to medium datasets (e.g., the sample dataset with 12 POIs).
- **Practical Results**: It produces near-optimal routes, typically within 25-50% of the optimal solution, suitable for visualization and planning.
- **Simplicity**: Easy to implement and understand, making the project accessible for educational and prototyping purposes.
- **Real-World Integration**: Uses OSMnx's street network for accurate, road-based distances, ensuring routes are feasible in urban environments like Bogotá.

### Relevance

The algorithm is critical to the project because:

- **Urban Navigation**: It generates realistic routes that follow city streets, making it applicable for scenarios like tourism, delivery, or service planning.
- **Category-Based Routing**: Optimizes routes for each category (e.g., visiting all tourist attractions), starting and ending at a base location, which mirrors real-world use cases.
- **Scalability for Prototyping**: Efficiently handles small datasets, ideal for demonstrating route optimization without complex dependencies.
- **Open-Source Friendly**: Avoids proprietary solvers, ensuring the project remains accessible and modifiable.

### Limitations and Future Improvements

- **Suboptimality**: The algorithm may not find the shortest possible route, especially for complex point distributions.
- **Scalability**: Less effective for very large datasets (e.g., hundreds of POIs).
- **Improvements**: Consider integrating advanced solvers like Google OR-Tools, simulated annealing, or genetic algorithms for better performance on larger datasets. These can be added as optional dependencies in future versions.

## Configuration

Customize the notebooks by updating the `CONFIG` dictionary. Key parameters include:

- `city_name`: Name of the city for OSMnx street network loading (e.g., "Bogotá, Colombia").
- `city_coords`: \[latitude, longitude\] for the map center (e.g., \[4.60971, -74.08175\] for Bogotá).
- `shapefile_path`: Path to the localities shapefile (e.g., `data/localities/localities.shp`).
- `csv_path`: Path to the POI CSV file (e.g., `data/puntos_interes.csv`).
- `output_file`: Path for the output HTML map (e.g., `outputs/mapa_interactivo_rutas_optimizadas.html`).

Example for a different city:

```python
CONFIG = {
    'city_name': 'New York, NY, USA',
    'city_coords': [40.7128, -74.0060],
    'shapefile_path': 'data/nyc/localities.shp',
    'csv_path': 'data/nyc_pois.csv',
    'output_file': 'outputs/nyc_map.html'
}
```

## Data Requirements

- **Points of Interest** (`puntos_interes.csv`):

  - Columns: `Latitud`, `Longitud`, `Nombre`, `Categoría`.
  - Base locations should have "BASE" in the `Nombre` column (e.g., "Base Turismo").
  - Example: `4.6951,-74.0733,Parque Simón Bolívar,Turismo`.

- **Localities** (`localities/localities.shp`):

  - A shapefile with administrative boundaries (optional).
  - Expected column: `Nombre_de_l` for locality names (update the code if your shapefile uses a different column).
  - Source for Bogotá: Datos Abiertos Bogotá.

## Notes

- **Performance**: The `route_optimization.ipynb` notebook caches the OSMnx street network to `data/city_graph.graphml` to avoid repeated downloads. Ensure write permissions for the `data/` directory.
- **Shapefile Handling**: If no shapefile is available, the notebooks skip the localities layer and focus on POIs and routes.
- **Extensibility**: The project is designed for easy modification. Add new features like clustering, time-based routing, or advanced TSP solvers as needed.

## Contributing

Contributions are welcome! Please open an issue or submit a pull request with improvements, bug fixes, or new features. Suggestions for enhancing the optimization algorithm or adding support for other data formats are particularly appreciated.

## License

This project is licensed under the MIT License. See the LICENSE file for details.