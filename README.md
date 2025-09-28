# Gastos - Gestor de gastos corporativos

## Descripción general
Aplicación web de una sola página para registrar gastos corporativos por proyecto, almacenar la información en el navegador y generar reportes Excel compatibles con una plantilla corporativa. La app está desarrollada únicamente con HTML, JavaScript y Tailwind CSS cargado desde CDN, por lo que puede ejecutarse como sitio estático.

### Características principales
- **Gestión de proyectos**: crear, listar, abrir y eliminar proyectos guardados en `localStorage`.
- **Registro de gastos**: formulario dinámico que cambia según la categoría seleccionada (Overseas/Local Travel, Entertainment u Others). Permite adjuntar una fotografía del recibo.
- **Auto-relleno opcional con IA**: integra la API de Google Gemini para leer la imagen del recibo y proponer valores para los campos del formulario.
- **Exportación a Excel**: genera un reporte a partir de la plantilla `gastos.xlsx`, conservando estilos y fórmulas, e inserta la imagen del recibo en la hoja de soporte `LOCAL TE support doc`.

## Estructura del repositorio
```
.
├── gastos.html        # Aplicación completa (HTML, lógica JS y estilos utilitarios)
├── gastos.xlsx        # Plantilla Excel utilizada para el reporte corporativo
└── README.md          # Este documento
```
Los archivos `js/config.js` y `styles.css` se eliminaron porque la configuración y los estilos viven dentro de `gastos.html` y no existía ninguna referencia a dichos ficheros.

## Configuración y variables clave
La configuración se define en la parte superior del script principal dentro de `gastos.html`.
```javascript
const CONFIG = {
  API_KEY: "TU_API_KEY_AQUI",      // Sustituye por tu clave de Google Gemini
  STORAGE_KEY: "gastos_empresa_proyectos",
  EXCEL_TEMPLATE_URL: "gastos.xlsx",
  API_VERSION: "v1beta",
  MODEL_NAME: "gemini-2.5-flash-preview-05-20"
};
```
1. Crea una API key válida de Google Gemini y reemplaza el valor de `API_KEY`.
2. Si mueves la plantilla Excel a otra ruta, actualiza `EXCEL_TEMPLATE_URL`.

## Ejecución local
1. Sirve el directorio con cualquier servidor de archivos estáticos (por ejemplo, la extensión **Live Server** de VS Code o `python -m http.server`).
2. Abre `http://localhost:<puerto>/gastos.html` en tu navegador.
3. Los datos de proyectos y gastos se almacenan localmente en el navegador; no se requiere backend.

## Flujo de uso
1. **Crear proyecto:** desde la vista principal, pulsa "Crear Nuevo Proyecto" y asigna un nombre único.
2. **Agregar gasto:** dentro del proyecto pulsa "Añadir Gasto". Selecciona la categoría adecuada y rellena los campos. Puedes:
   - Capturar el recibo con la cámara (`Tomar Foto del Recibo`).
   - Subir una imagen existente (`Seleccionar Imagen del Dispositivo`).
   - Usar "Auto-Rellenar" para solicitar a Gemini que extraiga datos desde la imagen (requiere haber configurado la API key).
3. **Editar gasto:** cada tarjeta de gasto tiene un botón "Editar Gasto" para reabrir el formulario con la información existente.
4. **Exportar a Excel:** pulsa "Descargar Excel" para obtener un archivo `<nombre_proyecto>_reporte.xlsx`. El script:
   - Localiza la sección correspondiente a cada categoría en la hoja "Corporate Credit Card".
   - Inserta los datos en la primera fila vacía o crea una nueva fila clonando el formato.
   - Inserta las imágenes en la hoja "LOCAL TE support doc" en la primera posición libre disponible, evitando superposición.

## Datos almacenados
Cada proyecto se guarda con la siguiente estructura y se persiste en `localStorage` bajo `CONFIG.STORAGE_KEY`:
```json
{
  "id": "<timestamp>",
  "name": "Nombre del proyecto",
  "createdAt": "2024-05-27T12:34:56.789Z",
  "expenses": [
    {
      "category": "LOCAL - TRAVEL EXPENSES",
      "date": "2024-05-25",
      "details": "Taxi aeropuerto",
      "purchaseOrder": "PO-123",
      "clientTraining": "N",
      "transport": 35,
      "invoiceMissing": false,
      "receiptImage": "data:image/png;base64,...",
      "foreignCurrency": 0,
      "...otros campos dependiendo de la categoría"
    }
  ]
}
```

## Dependencias externas
- **Tailwind CSS**: cargado vía CDN para utilidades de estilo.
- **ExcelJS**: manipulación avanzada de la plantilla Excel.
- **SheetJS (XLSX)**: lectura básica del archivo (compatibilidad heredada).
- **Google Gemini API**: procesamiento de imágenes para auto-relleno.

## Mantenimiento y mejoras sugeridas
- Añadir autenticación y almacenamiento en servidor si se requiere compartir proyectos entre usuarios.
- Implementar validaciones adicionales para montos y fechas.
- Agregar tests automáticos para las funciones de exportación y parsing de IA.
- Ampliar el soporte multilenguaje desde la interfaz.

