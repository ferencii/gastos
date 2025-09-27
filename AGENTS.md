# AGENTS.md - Documentación del Proyecto Gastos

## Descripción General

Este es un sistema de gestión de gastos empresariales que permite a los usuarios:

- Crear proyectos de gastos.
- Capturar recibos usando la cámara o subiendo imágenes.
- Procesar automáticamente recibos usando IA para extraer datos.
- Categorizar gastos en diferentes tipos.
- Exportar reportes a un archivo Excel que mantiene el formato y las fórmulas de una plantilla predefinida.

## Cómo Ejecutar el Proyecto

1.  **Servidor Local:** Asegúrate de tener un servidor web local para servir los archivos estáticos (HTML, CSS, JS). Una forma sencilla es usar la extensión **Live Server** en Visual Studio Code.
2.  **Iniciar la Aplicación:** Abre el archivo `gastos.html` en tu navegador utilizando el servidor local.
3.  **Configurar API Key:** Antes de usar la funcionalidad de IA, es crucial reemplazar el valor de `API_KEY` en el objeto `CONFIG` dentro del archivo `gastos.html` con tu propia API key de Google Gemini.

## Estructura del Proyecto

### Tecnologías Principales

- HTML/JavaScript (Frontend puro, sin framework)
- Tailwind CSS para estilos
- ExcelJS para generación de reportes
- Google Gemini AI para procesamiento de imágenes
- LocalStorage para persistencia de datos en el navegador

### Configuración

El objeto `CONFIG` en `gastos.html` centraliza las variables clave del proyecto:

```javascript
const CONFIG = {
  API_KEY: "TU_API_KEY_AQUI",
  MODEL_NAME: "gemini-2.5-flash-preview-05-20",
  API_VERSION: "v1beta",
  STORAGE_KEY: "gastos_empresa_proyectos",
  EXCEL_TEMPLATE_URL: "gastos.xlsx",
};
```

### Categorías de Gastos

1.  OVERSEAS - TRAVEL EXPENSES
2.  LOCAL - TRAVEL EXPENSES
3.  ENTERTAINMENT
4.  OTHERS

### Estructura de Datos

```javascript
// Estructura de un Proyecto
{
    id: String,
    name: String,
    createdAt: ISOString,
    expenses: Array<Expense>
}

// Estructura de un Gasto (Expense)
{
    category: String,
    date: String,
    details: String,
    receiptImage: String (base64),
    // ... y otros campos adicionales según la categoría
}
```

## Funcionalidades Principales

### 1. Gestión de Proyectos

- Crear nuevos proyectos de gastos.
- Listar los proyectos existentes.
- Eliminar proyectos (junto con todos sus gastos asociados).
- Seleccionar un proyecto para ver sus detalles.

### 2. Gestión de Gastos

- Añadir nuevos gastos a un proyecto.
- Editar los gastos existentes.
- Capturar imágenes de recibos con la cámara del dispositivo o seleccionarlas desde el almacenamiento.

### 3. Procesamiento de Imágenes con IA

El sistema utiliza el modelo `gemini-2.5-flash-preview-05-20` para analizar la imagen de un recibo y extraer la siguiente información en formato JSON:

- `date`: Fecha del gasto.
- `description`: Descripción o concepto del gasto.
- `meals`, `accommodation`, `transport`, `airfares`: Montos para diferentes sub-tipos de gasto.
- `total`: Monto total del recibo.
- `place`: Nombre del establecimiento.
- `isOverseas`: Un booleano para indicar si el gasto es internacional.

Posteriormente, el código de la aplicación utiliza esta información para:

- **Sugerir una categoría** (p. ej., si `airfares > 0`, sugiere "TRAVEL EXPENSES").
- **Auto-rellenar los campos** del formulario de nuevo gasto para agilizar la entrada de datos.

### 4. Exportación a Excel

- Genera un reporte en formato `.xlsx` basado en una plantilla (`gastos.xlsx`).
- Mantiene las fórmulas y el formato de la plantilla original.
- Busca la primera fila vacía dentro de la sección de cada categoría para añadir nuevos gastos.
- Si una sección está llena, inserta una nueva fila, replicando el estilo y las fórmulas de las filas existentes.

## Historial de Cambios (realizado por el agente de IA)

- **Corrección de API de Gemini:** Se actualizó el endpoint de la API a `v1beta` para asegurar la compatibilidad con los modelos de Gemini.
- **Ajuste del Modelo de IA:** Se configuró el sistema para usar el modelo `gemini-2.5-flash-preview-05-20` según la preferencia del usuario.
- **Corrección de Errores de Sintaxis:** Se solucionó un error de `try...catch` mal estructurado que impedía la ejecución del código de procesamiento de imágenes.
- **Mejora del Auto-rellenado del Formulario:** Se implementó una lógica de mapeo robusta para asegurar que los datos extraídos por la IA (ej. `description`, `total`) se asignen correctamente a los campos del formulario (ej. `details`, `totalPrice`).
- **Arreglo de la Exportación a Excel:** Se reescribió la funcionalidad de exportación para localizar correctamente la primera fila vacía en cada categoría y para insertar nuevas filas (preservando formato y fórmulas) cuando el espacio se agota.

## Áreas de Mejora Potencial

1.  **Backend y Persistencia de Datos:**
    - Implementar un backend (ej. Node.js, Python) para persistencia de datos en una base de datos real.
    - Añadir autenticación de usuarios.
    - Almacenamiento en la nube para las imágenes de los recibos (ej. S3, Firebase Storage).
2.  **Mejoras de UI/UX:**
    - Añadir un modo oscuro.
    - Mejorar el diseño responsive para una mejor experiencia en tablets.
    - Implementar drag & drop para subir imágenes de recibos en escritorio.
3.  **Funcionalidades Adicionales:**
    - Soporte para múltiples divisas con conversión automática.
    - Dashboard con reportes analíticos y gráficos.
    - Exportación de reportes a formato PDF.
    - Funcionalidad para compartir proyectos entre usuarios.
4.  **Optimizaciones:**
    - Compresión de imágenes en el cliente antes de subirlas para ahorrar ancho de banda y almacenamiento.
    - Implementar un sistema de caché para los resultados de la IA y evitar procesar la misma imagen varias veces.
    - Mejorar la aplicación para que tenga funcionalidades offline usando Service Workers.

## Guía de Desarrollo

### Añadir Nueva Categoría de Gasto

1.  Agregar la nueva categoría a la definición del objeto `categoryFields` en `gastos.html`.
2.  Asegurarse de que la plantilla de Excel (`gastos.xlsx`) contenga la nueva sección de categoría con el formato adecuado.
3.  Actualizar la lógica de exportación a Excel si la nueva categoría tiene una estructura de columnas única.

### Modificar Procesamiento IA

1.  Actualizar el `prompt` en el `event listener` del input `receiptImageInput` para pedir a la IA los nuevos campos.
2.  Modificar el mapeo de la respuesta de la IA a los campos del formulario en el `setTimeout` dentro del mismo `event listener`.

## Consideraciones de Seguridad

- **Validación de Archivos:** Validar siempre el tipo y tamaño de los archivos subidos.
- **Sanitización de Datos:** Aunque no hay un backend, es una buena práctica sanitizar los datos que se muestran para prevenir ataques XSS si la aplicación evoluciona.
- **Gestión de API Keys:** La API key no debe estar hardcodeada en el código en un entorno de producción. Debería ser gestionada a través de un backend o variables de entorno.

## Dependencias Externas

- **TailwindCSS:** Framework de CSS para un diseño rápido y moderno.
- **ExcelJS:** Librería para leer, manipular y escribir archivos de Excel.
- **XLSX (SheetJS):** Utilizada para funcionalidades básicas de Excel (aunque ExcelJS es la principal para la escritura).
- **Google Gemini AI API:** Servicio de IA para el procesamiento de imágenes.

### Categorías del excel

voy a mostrate las columnas que tiene cada categoría desde la columna A hasta la L, recueda que el nombre de la categoría está en la columna B:
OVERSEAS - TRAVEL EXPENSES:
ITEM, Date, Details, Purchase Order, Client Training? Y/N, Meals, Accomodation, Airfares, Transport, Other, Marked "X" if invoice missing, Cost in Foreign curreny
LOCAL - TRAVEL EXPENSES
ITEM, Date, Details, Purchase Order, Client Training? Y/N, Meals, Accomodation, Airfare, Transport, Other, Marked "X" if invoice missing, Cost in Foreign curreny

ENTERTAINMENT
ITEM, Date, Details (Please provide clients & company name for our tax purpose), Quantity, Traveling? Y/N, Place, Total Price, Staff Meal, Client Meal, ,Marked "X" if invoice missing, Cost in Foreign curreny
OTHERS
ITEM, Date, Details, Client Training? Y/N, Purchase Order, Project, Total Price, Marked "X" if invoice missing, Cost in Foreign curreny

las categorías están dentro de la hoja "Corporate Credit Card" en la clumna B y son las siguientes:
OVERSEAS - TRAVEL EXPENSES
LOCAL - TRAVEL EXPENSES
ENTERTAINMENT
OTHERS

justo debajo de cada categoría en la misma columa B está DATE y debajo ya empiezan las filas a rellenar hasta que se encuentra con una fila con las clumnas B C D combinadas

los datos tienen que copiarse en la primera fila vacía de la categoría seleccionada, y si ya no quedan filas libres, se debe de crear una nueva pero ojo, estas filas tienen formatos y formulas, con lo que tiene que ser una copia exacta de una de las filas a rellenar

y debe de insertar la imagen del recibo en la hoja LOCAL TE support doc teniendo en cuenta que no pise o se sobre ponga con las imágenes que ya haya
