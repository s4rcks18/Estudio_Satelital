# Estudio_Satelital
Documentos y datos sobre imagenes satelitales previos para poder realizar predicciones con IA.

### Introducción a la Herramienta
Esta herramienta la armé en Python usando `geemap` y Google Earth Engine para facilitar el análisis de imágenes satelitales de Sentinel-2 de forma interactiva desde un Jupyter Notebook, permitiendo dibujar zonas en el mapa y procesarlas al instante con un panel de control flotante muy práctico.

### Especificación Técnica de Google Earth Engine (GEE) API
Por la parte técnica, la API de Google Earth Engine se encarga de manejar la colección multiespectral de Sentinel-2 (`COPERNICUS/S2_SR_HARMONIZED`) aplicando filtros temporales y de nubosidad, utilizando específicamente las bandas B2 (Azul), B3 (Verde), B4 (Rojo), B8 (Infrarrojo Cercano) y B11 (SWIR). Lo que obtenemos son rásters procesados con fórmulas de índices y matrices detalladas con coordenadas geográficas de latitud/longitud y los valores numéricos exactos de cada píxel, controlando un límite estricto de 4,999 elementos por lote (`numPixels`) para evitar bloqueos por parte de los servidores.

### Integración de la Overpass API
Por otro lado, esta API complementa el flujo recibiendo las coordenadas extremas o `bounding boxes` calculadas a partir de las geometrías dibujadas en el mapa, devolviendo elementos vectoriales del área de interés de manera rápida y configurada con un tiempo de espera estricto (`timeout`) para no congelar la interfaz gráfica de los widgets.

### Lógica Técnica y Funcionamiento del Algoritmo
El núcleo del script opera mediante un ciclo secuencial de validación espacial: primero, el algoritmo intercepta las figuras creadas en el mapa mediante `Map.draw_features` y evalúa su tipo geométrico (`Point`, `Polygon`, etc.). Si detecta un punto, aplica una operación de búfer simétrico (`g.buffer()`) expandiéndolo en metros según el parámetro ingresado, mientras que si es un polígono o rectángulo, respeta sus vértices originales sin alteraciones. Posteriormente, se realiza un recorte ráster sobre la imagen base de Sentinel-2 utilizando las geometrías procesadas, se evalúa la fórmula matemática del índice espectral seleccionado (como NDVI o MNDWI), se renderiza la capa visualmente con su paleta de colores y leyenda, y finalmente se extraen los datos tabulares mediante un muestreo espacial a escala nativa de 10 metros, generando un archivo CSV estructurado por zonas con las etiquetas de origen correspondientes.


### Librerias que se requieren instalar
pip install earthengine-api geemap ipywidgets pandas requests

tambien mencionar que encuentro ciertos detalles con ciertas herramientas del mismo GEE , y que en la extraccion de la infomacion por pixel teninendo ese limitante tendria que indagar y apoyarme más para permitir más pixeles ya que claramente son muchos pixeles por 1 y quisiera que pudiera con mas de 3 zonas.

tengo entendido que esto genera un diagrama en git entonces lo mandare de esta manera para saber si es asi, si no para desarrollarlo en dia o draw.io 
```mermaid
flowchart TD
    User([Usuario / Interfaz]) -->|1. Coordenadas, Tipo de Geometría y Radio (m)| Script[Script Principal geemap / ipywidgets]
    Script -->|2. Evaluación lógica de vértices y búferes| Memory[(Historial de Zonas en Memoria)]
    Memory -->|3. Geometrías procesadas y Bandas B2, B3, B4, B8, B11| GEE[(Google Earth Engine API)]
    GEE -->|4. Ráster recortado, Matriz Lat/Lon y Valores de Píxel| Script
    Script -->|5. Capa visual, paleta de colores y leyenda| User
    User -->|6. Solicitud de exportación| Script
    Script -->|7. Consulta segura por lotes numPixels=4999| GEE
    GEE -->|8. Dataset de píxeles filtrados| Script
    Script -->|9. Archivo CSV con Pixel_ID, Origen_Figura y Coordenadas| Storage[(Disco Local: CSV)]


