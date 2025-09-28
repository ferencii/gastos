# Documentación técnica del proyecto de gastos

## Visión general
La aplicación es una SPA basada únicamente en HTML, CSS utilitario provisto por Tailwind desde CDN y JavaScript vanilla. Permite gestionar proyectos de gastos y sus recibos directamente en el navegador, persistiendo los datos en `localStorage` y exportando los resultados a un archivo Excel basado en la plantilla `gastos.xlsx`.

## Estructura del repositorio
- `gastos.html`: contiene toda la lógica de la interfaz, la definición de estilos mediante utilidades de Tailwind y el código JavaScript que gobierna el flujo de la aplicación.
- `gastos.xlsx`: plantilla que se usa como base para generar el reporte descargable. Incluye las hojas **Corporate Credit Card** y **LOCAL TE support doc** que la aplicación rellena dinámicamente.
- `instrucciones.md`: este documento con la guía técnica.
- `resumen.md`: bitácora breve del estado actual del proyecto y próximos pasos.

No hay archivos JavaScript o CSS externos: todo el comportamiento y los estilos personalizados viven en `gastos.html`. Cualquier modificación debe realizarse ahí para mantener la coherencia del proyecto.

## Flujo funcional
### Gestión de proyectos
- El listado principal (`projectsView`) muestra los proyectos almacenados en `localStorage`.
- Al pulsar **Crear Nuevo Proyecto** se solicita el nombre del proyecto y se guarda con un identificador basado en `Date.now()`.
- Desde el detalle del proyecto se puede eliminar el proyecto seleccionado (previa confirmación) o volver al listado.

### Gestión de gastos
- Cada proyecto almacena un arreglo `expenses` con los gastos asociados.
- El formulario de alta se genera dinámicamente en función de la categoría seleccionada (`categoryFields`).
- Al crear un gasto nuevo la categoría **OTHERS** se carga por defecto y el formulario se despliega automáticamente para agilizar la captura.
- La función `showAddExpenseView` ignora eventos DOM y siempre fuerza la precarga de **OTHERS** cuando se abre desde el botón general, garantizando que el formulario esté listo sin pasos adicionales.
- Se soportan las categorías **OVERSEAS - TRAVEL EXPENSES**, **LOCAL - TRAVEL EXPENSES**, **ENTERTAINMENT** y **OTHERS**, cada una con sus campos particulares.
- Los gastos se pueden crear y editar; al editar se precargan los datos e imagen previamente guardados.
- Desde la vista de detalle es posible eliminar un gasto individual desde su tarjeta, previa confirmación, lo que actualiza inmediatamente la lista y el estado persistido.
- Las miniaturas de los recibos se pueden pulsar para abrir un modal con la imagen en alta resolución; se cierra con el botón dedicado, al hacer clic fuera o con la tecla Escape.

### Captura de recibos y auto-relleno
- Los botones **Tomar Foto del Recibo** y **Seleccionar Imagen del Dispositivo** utilizan el `<input type="file" capture="camera">` para obtener la imagen y mostrarla en la vista previa.
- La imagen se almacena en formato base64 asociada al gasto para reutilizarla y exportarla.
- El botón **Auto-Rellenar** envía la imagen al modelo Gemini definido en `CONFIG.MODEL_NAME`. La petición se lanza contra el endpoint `https://generativelanguage.googleapis.com/${CONFIG.API_VERSION}/models/...:generateContent`.
- Se espera un JSON con campos como `date`, `description`, `meals`, `accommodation`, `transport`, `airfares`, `total`, `place` e `isOverseas`. Los valores válidos se asignan a los campos del formulario respetando los mapeos descritos en el código (por ejemplo `description → details`).

### Persistencia local
- La clave usada en `localStorage` es `gastos_empresa_proyectos` (`CONFIG.STORAGE_KEY`).
- Cada proyecto se guarda con el siguiente esquema:
  ```json
  {
    "id": "string",
    "name": "string",
    "createdAt": "ISOString",
    "expenses": [
      {
        "category": "string",
        "date": "YYYY-MM-DD",
        "details": "string",
        "receiptImage": "data:image/...",
        "...campos específicos de la categoría"
      }
    ]
  }
  ```

### Exportación a Excel
- Al pulsar **Descargar Excel** se carga la plantilla `gastos.xlsx`, se localiza la hoja **Corporate Credit Card** y se identifica el bloque correspondiente a cada categoría.
- Antes de escribir se limpian completamente las filas de datos de la categoría (valores, combinaciones y estado de filas) para evitar que queden textos guía como "Insert line" o celdas fusionadas que oculten información.
- El script busca la primera fila vacía dentro del rango de la categoría; si no existe, inserta una nueva fila clonando estilos, formatos y fórmulas del template y la prepara antes de insertar los valores.
- Los campos se escriben en las columnas `B` a `L` según la categoría y se mantiene el formato de fecha cuando procede.
- Las imágenes de los recibos se insertan en la hoja **LOCAL TE support doc** usando `ExcelJS.Workbook.addImage`, respetando un espaciado mínimo para evitar solapamientos (`rowPadding = 2`, `minimumRowSpacing = 20`).
- Existe un botón de **Previsualizar Excel** que abre un modal con tablas por categoría (mismo orden y columnas que la exportación) para validar rápidamente que ningún gasto falte antes de descargar la plantilla.

## Configuración e inicio
1. Servir el proyecto con cualquier servidor de archivos estáticos (por ejemplo, la extensión **Live Server** de VS Code). También es posible abrir `gastos.html` directamente en el navegador si este permite acceder a `localStorage` y a `fetch` sobre archivos locales.
2. Editar el bloque `CONFIG` al inicio del `<script type="module">` en `gastos.html` y colocar tu propia API key de Google Gemini en `API_KEY`. También se puede ajustar `MODEL_NAME` y `API_VERSION` si se usa otro modelo compatible.
3. La plantilla `gastos.xlsx` debe permanecer en el mismo directorio que `gastos.html` para que la descarga funcione mediante `fetch` relativo.

## Dependencias externas
- **Tailwind CSS CDN**: proporciona utilidades de diseño (`https://cdn.tailwindcss.com`). Se complementa con un `tailwind.config` embebido al final del HTML para declarar componentes personalizados.
- **XLSX (SheetJS)** y **ExcelJS** desde CDN: lectura y manipulación del archivo Excel.
- **Google Gemini Generative Language API**: servicio de IA para extraer datos de los recibos.

## Guías de desarrollo
- **Añadir o modificar categorías**: actualizar el objeto `categoryFields`, el renderizado de tarjetas en `renderExpenses`, y la lógica del switch dentro de la exportación para escribir los nuevos campos en Excel. No olvidar adaptar la plantilla `gastos.xlsx`.
- **Modificar el auto-relleno**: ajustar el `prompt` y los mapeos de `autoFillExpenseFields` para reflejar los campos requeridos.
- **Control de versiones de la plantilla**: cualquier cambio en `gastos.xlsx` debe documentarse y probarse generando un archivo de ejemplo.

## Consideraciones y limitaciones
- La API key está expuesta en el cliente; para entornos productivos se recomienda mover la lógica de IA a un backend.
- El almacenamiento es local al navegador. Borrar el almacenamiento del sitio eliminará los proyectos.
- Las peticiones a Gemini requieren conexión a internet y habilitar el dominio en la consola de Google.

## Historial reciente
- Se eliminaron los archivos `js/config.js` y `styles.css` porque la configuración y los estilos viven dentro de `gastos.html` y los ficheros externos no eran utilizados.
