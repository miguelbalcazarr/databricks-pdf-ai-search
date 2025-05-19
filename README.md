# databricks-pdf-ai-search

## Descripción general

`databricks-pdf-ai-search` es una solución de ejemplo que muestra cómo procesar documentos PDF almacenados en un volumen de Databricks (DBFS/Volumes) y transformarlos en fragmentos JSON, para luego guardarlos nuevamente en un volumen.

## Arquitectura

```
+------------------------+      +-------------------------+      +------------------------+
|                        |      |                         |      |                        |
|  Volumen de PDFs       | ---> | Azure Databricks        | ---> | Volumen de JSON        |
|  (DBFS/Volumes)        |      | (PySpark + Utils)       |      | (DBFS/Volumes)         |
|                        |      |                         |      |                        |
+------------------------+      +-------------------------+      +------------------------+
```

1. **Origen**: PDFs almacenados en un volumen de Databricks (Volumes/DBFS).
2. **Procesamiento**: notebooks de Databricks:

   * `utils.ipynb`: funciones auxiliares (conversión de rutas, bloqueo, carga de configuración).
   * `ntb_read_pdf_volumes.ipynb`: lectura de PDFs, creación de fragmentos y generación de JSON.
3. **Destino**: los JSON resultantes se guardan en un volumen de Databricks.

## Requisitos previos

* **Workspace de Azure Databricks** con permisos para ejecutar notebooks.
* **Databricks Runtime 14.3 LTS** con Python 3.9.
* **Librerías y versiones** (instaladas en los notebooks mediante `%pip install`):

  * `numpy==1.24.3`
  * `spacy==3.5.0`
  * `es-core-news-sm==3.1.0`
  * `PyPDF2==3.0.1`
  * `langdetect==1.0.9`

## Notebooks

### `utils.ipynb`

Este notebook centraliza la **buena práctica** de fragmentación de texto:

* Incluye la función `chunk_text(text: str, size: int) -> List[str]`, que divide un texto en fragmentos de longitud aproximada `size`, respetando límites de palabra.

Mantener esta utilidad en un notebook separado aporta:

1. **Reutilización**: varios notebooks pueden importar `chunk_text` sin duplicar lógica.
2. **Mantenibilidad**: ajustes en la fragmentación se realizan en un único lugar.

### `ntb_read_pdf_volumes.ipynb`

Este notebook transforma tus PDFs en JSON:

1. **Listado de PDFs**: obtiene la lista de archivos desde la ruta definida en `input_path`.
2. **Conversión a JSON**: procesa cada PDF y lo convierte en uno o varios documentos JSON.
3. **Guardado de salida**: escribe los archivos JSON generados en la carpeta especificada por `output_dir`.

## Uso

En el notebook `ntb_read_pdf_volumes.ipynb` encontrarás las siguientes celdas:

1. **Importaciones y configuración**: carga las librerías necesarias y establece las variables `input_path` y `output_dir`.

2. **Procesamiento de PDFs**: recorre la lista de archivos en `input_path`, lee cada PDF y extrae su contenido.

3. **Fragmentación y generación de JSON**: utiliza la función `chunk_text` para dividir el texto extraído en fragmentos y construye documentos JSON con metadatos.

4. **Escritura en volumen**: guarda los archivos JSON generados en la ruta definida por `output_dir`.

5. **Validación rápida**: muestra un ejemplo de JSON resultante para verificar la estructura.

## Autor
Miguel Balcazar

LinkedIn: miguel-balcazar-carmelo
