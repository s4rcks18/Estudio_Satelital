# Estudio_Satelital
Documentos y datos sobre imágenes satelitales previos para poder realizar predicciones con IA.

### Introducción a la Herramienta
Esta herramienta la armé en Python usando `geemap` e `ipywidgets` para facilitar el análisis interactivo de imágenes satelitales desde un Jupyter Notebook. Permite delimitar zonas de estudio mediante múltiples métodos de selección, procesando los índices espectrales al instante con un panel de control flotante.

---

### Fuentes y Métodos de Zonas de Estudio
Para delimitar y procesar las áreas geográficas, el sistema soporta tres fuentes principales de geometrías:
1. **Dibujos manuales e interactivos:** Trazado de puntos (con búfer configurable en metros para generar áreas circulares o cuadradas), polígonos, rectángulos o líneas directamente sobre el mapa interactivo mediante `ipyleaflet`.
2. **Límites Administrativos Oficiales (FAO GAUL 2015):** Capas vectoriales de Google Earth Engine que permiten seleccionar países enteros (Nivel 0), estados (Nivel 1) o municipios individuales/masivos (Nivel 2).
3. **Infraestructura y Elementos de OpenStreetMap (OSM / Overpass API):** Extracción automatizada de elementos físicos del mundo real (edificios, calles, cuerpos de agua, zonas naturales) en áreas de hasta 25 km², permitiendo hacer clic en cualquier estructura para convertirla en zona activa de análisis.

---

### Especificación Técnica de Google Earth Engine (GEE) API y Datos Utilizados
La herramienta interactúa directamente con los servidores en la nube de GEE utilizando los siguientes datasets y tipos de datos:
* **Colección base satelital:** `COPERNICUS/S2_SR_HARMONIZED` (`ee.ImageCollection`). Contiene reflectancia de superficie de Sentinel-2 Level-2A corregida atmosféricamente. Los valores de reflectancia se escalan dividiéndolos entre `10000` (`ee.Image.divide(10000)`).
* **Filtros aplicados:** Rango temporal (`ee.Filter.date` con cadenas de texto `YYYY-MM-DD`) y filtro estricto de nubosidad (`ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 15)`).
* **Topografía:** `USGS/SRTMGL1_003` (`ee.Image`). Modelo digital de elevación (DEM) de 30 metros de resolución espacial.
* **Bandas espectrales y tipos procesados:**
  * `B2` (Azul, float), `B3` (Verde, float), `B4` (Rojo, float), `B8` (Infrarcojo Cercano - NIR, float), `B11` (Infrarcojo de Ondas Cortas - SWIR, float).
* **Índices y variables calculadas:** NDVI, NDWI, MNDWI, EVI, NDSI, BSI, SAVI, NDMI, ELEVACION y PENDIENTE mediante expresiones matemáticas normalizadas de tipo `ee.Image`.

---

### Integración de la Overpass API (OpenStreetMap / OSMnx)
* **Función:** Se conecta a los servidores espejo de Overpass API para extraer elementos vectoriales reales del mundo físico a partir de un polígono de interés (`shapely.geometry.Polygon`).
* **Datos y Tipos extraídos:** Retorna un `geopandas.GeoDataFrame` con geometrías vectoriales (`Point`, `LineString`, `Polygon`) y metadatos en formato de diccionario de etiquetas (`dict`).
* **Categorización y Filtrado:** Traduce y clasifica los atributos (`amenity`, `highway`, `water`, `building`, etc.) en categorías normalizadas (`edificio`, `calle`, `agua`, `servicio`, `natural`, `estructura`).
* **Estabilidad:** Configurada con `timeout = 300s` y rotación automática de espejos (`overpass-api.de`, servidores de respaldo) para soportar áreas de hasta **25 km²** y renderizar hasta 15,000 estructuras en capas interactivas `ipyleaflet.GeoJSON`.

---

### Valores a Utilizar en el Mapa e Interfaz Visual
Los datos ráster procesados se renderizan dinámicamente en el mapa mediante diccionarios de visualización (`vis`) que configuran los umbrales numéricos y las paletas de colores hexadecimales:
* **Índices Espectrales (ej. NDVI, MNDWI):** Valores continuos de tipo float que oscilan típicamente entre `-1.0` y `1.0`. Se representan con paletas de degradado continuo (ej. tonos marrones a verdes para vegetación, o rojos a azules para agua).
* **Elevación (SRTM):** Valores enteros/float en metros (`0` a `3500+` m s.n.m.), representados con rampas altitudinales.
* **Pendiente:** Valores en grados (`0` a `45+`°).
* **Elementos Vectoriales (OSM):** Estilos aplicados mediante atributos visuales de GeoJSON (`color`, `fillColor`, `weight`, `fillOpacity`) optimizados por categoría.

---

### Características del Mapeo y Datos Contenidos en los Archivos CSV
* **Exportación a Google Drive:** Envía tareas de procesamiento por lotes en segundo plano generando archivos **CSV limpios**.
* **Estructura y Tipos de Datos en el CSV:**
  * **Columnas de Control (Metadatos de la Zona):** `id_zona` (int, identificador único de la figura), `escala_m` (int, resolución espacial en metros, ej. 10, 20, 30), `error_posicion_max_m` (float, semidiagonal de la celda).
  * **Variables Espectrales / Topográficas:** Valores numéricos (float) correspondientes a las bandas solicitadas (`NDVI`, `NDWI`, etc.).
  * **Coordenadas (Modo Píxeles):** `longitude` (float) y `latitude` (float) de cada píxel muestreado a escala nativa.
  * **Modo Estadísticas (Alternativo):** Agrega sufijos métricos por zona (`_mean`, `_stdDev`, `_min`, `_max`, `_count`, `_error_est`).

---

### Limitaciones Actuales y Retos Técnicos con GEE
* **Límite de píxeles por exportación/muestreo:** Al extraer información masiva por píxel en áreas extensas o al intentar procesar de forma simultánea múltiples zonas complejas, se alcanzan los umbrales de memoria y restricciones de nodos de procesamiento de Earth Engine (`maxPixels` / límites de la API síncrona).
* **Próximos pasos:** Indagar en métodos avanzados de procesamiento por lotes en la nube (`ImageCollection.map()` o exportaciones particionadas a Google Cloud Storage / BigQuery) para escalar la extracción más allá de los límites locales.

---

### Librerías Requeridas
```bash
pip install earthengine-api geemap ipywidgets pandas requests osmnx shapely matplotlib