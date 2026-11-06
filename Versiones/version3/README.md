# Estudio_Satelital
Documentos y datos sobre imágenes satelitales previos para poder realizar predicciones con IA.

### Introducción a la Herramienta
Esta herramienta la armé en Python usando `geemap` e ipywidgets para facilitar el análisis interactivo de imágenes satelitales desde un Jupyter Notebook. Permite delimitar zonas de estudio mediante dibujos manuales o mediante la extracción automatizada de infraestructura urbana y natural, procesando los índices espectrales al instante con un panel de control flotante.

---

### Especificación Técnica de Google Earth Engine (GEE) API y Datos Utilizados
* **Colección base:** `COPERNICUS/S2_SR_HARMONIZED` (Sentinel-2 Level-2A surface reflectance, corregida atmosféricamente).
* **Filtros aplicados:** Rango temporal configurable y filtro estricto de nubosidad (`CLOUDY_PIXEL_PERCENTAGE < 15%`).
* **Bandas espectrales utilizadas:**
  * `B2` (Azul) y `B4` (Rojo)
  * `B3` (Verde)
  * `B8` (Infrarrojo Cercano - NIR)
  * `B11` (Infrarrojo de Ondas Cortas - SWIR)
* **Índices calculados:** NDVI, NDWI, MNDWI, EVI, NDSI, BSI, SAVI y NDMI mediante fórmulas matemáticas normalizadas y expresiones ráster.
* **Datos extraídos:** Muestreos espaciales a escala nativa de **10 metros por píxel** dentro de la geometría activa.

---

### Integración de la Overpass API (OpenStreetMap / OSMnx)
* **Función:** Se conecta a los servidores espejo de Overpass API para extraer elementos vectoriales reales del mundo físico.
* **Capas y datos que extrae:** Utiliza etiquetas detalladas de interés (`amenity`, `highway`, `waterway`, `water`, `natural`, `landuse`, `leisure`, `building`, `man_made`, `railway`).
* **Uso en el mapeo:** Traduce y clasifica los elementos en categorías visuales (`edificio`, `calle`, `agua`, `servicio`, `natural`, `estructura`) aplicando estilos personalizados mediante capas `GeoJSON` interactivas. Permite hacer clic en cualquier estructura del mapa para convertirla de inmediato en la zona activa de análisis.
* **Estabilidad:** Configurada con un tiempo de espera extendido (`timeout = 300s`) y rotación automática de espejos (`overpass-api.de`, servidores de respaldo) para soportar áreas de hasta **25 km²** y renderizar hasta 15,000 estructuras sin colapsar.

---

### Características del Mapeo y Estructura del Archivo CSV
* **Mapeo interactivo:** El mapa inicializa centrado en coordenadas configurables con una capa base limpia de OpenStreetMap. Las geometrías del usuario (puntos con búfer dinámico en metros, cuadrados, círculos o polígonos) se registran en un historial global indexado para evitar errores de sincronización en la interfaz.
* **Exportación a Google Drive:** Envía las tareas de procesamiento en segundo plano generando archivos **CSV limpios**. 
* **Qué contiene el CSV:** Cada fila representa un píxel muestreado de 10 metros, conteniendo exclusivamente el valor numérico del índice espectral analizado, junto con sus coordenadas geográficas exactas (`longitude`, `latitude`),aplicando limpieza por expresiones regulares para evitar errores por acentos o caracteres especiales en los nombres de las tareas.

---

### Limitaciones Actuales y Retos Técnicos con GEE
Aunque la herramienta es sumamente eficiente para múltiples zonas, me he encontrado con ciertos detalles técnicos propios de las políticas de GEE:
* **Límite de píxeles por exportación/muestreo:** Al extraer información masiva por píxel en áreas extensas o al intentar procesar de forma simultánea más de 3 zonas complejas, se alcanzan los umbrales de memoria y restricciones de nodos de procesamiento de Earth Engine (`maxPixels` / límites de la API síncrona). 
* **Próximos pasos:** Actualmente estoy indagando y requiero apoyarme en métodos avanzados de procesamiento por lotes en la nube (como colecciones `ImageCollection.map()` o exportaciones particionadas a Google Cloud Storage / BigQuery) para escalar la extracción más allá de 3 zonas sin restricciones de memoria local.

---

### Librerías que se requieren instalar
```bash
pip install earthengine-api geemap ipywidgets pandas requests osmnx shapely
