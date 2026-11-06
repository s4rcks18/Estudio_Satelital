# Gestor Geoespacial con OpenStreetMap (Versión 2)

Esta versión introduce la capacidad de consultar y renderizar infraestructura real del mundo físico (edificios, calles, cuerpos de agua y servicios) directamente desde la API de OpenStreetMap utilizando `osmnx`. aunque al tener tanta estructura no marca bien las estructuras, mas que las calles y cuerpos de agua.

## Mejoras y Características
* **Conexión con OpenStreetMap:** Descarga elementos vectoriales reales dentro del área dibujada o del área visible del mapa mediante `ox.features_from_polygon`.
* **Interactividad por Clic:** Permite hacer clic directamente sobre cualquier edificio o estructura dibujada en el mapa para seleccionarla automáticamente como la "Zona Activa".
* **Filtrado Dinámico:** Incluye un menú desplegable de categorías (`filtro_osm`) para mostrar únicamente edificios, calles, agua o servicios según la necesidad del análisis.
* **Control de Estado:** Historial basado en índices numéricos para evitar errores de sincronización de widgets en Jupyter.



