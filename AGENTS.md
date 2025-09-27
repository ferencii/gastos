# AGENTS.md - Documentación del Proyecto Gastos

## Descripción General

Este es un sistema de gestión de gastos empresariales que permite a los usuarios:

- Crear proyectos de gastos
- Capturar recibos usando la cámara o subiendo imágenes
- Procesar automáticamente recibos usando IA (gemini-2.5-flash-preview-05-20)
- Categorizar gastos en diferentes tipos
- Exportar reportes a Excel

## Estructura del Proyecto

### Tecnologías Principales

- HTML/JavaScript (Frontend puro, sin framework)
- Tailwind CSS para estilos
- ExcelJS para generación de reportes
- Google Gemini AI para procesamiento de imágenes
- LocalStorage para persistencia de datos

### Configuración

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

1. OVERSEAS - TRAVEL EXPENSES
2. LOCAL - TRAVEL EXPENSES
3. ENTERTAINMENT
4. OTHERS

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
    // Campos adicionales según categoría
    // ...
}
```

## Funcionalidades Principales

### 1. Gestión de Proyectos

- Crear nuevos proyectos
- Listar proyectos existentes
- Eliminar proyectos
- Ver detalles de proyecto

### 2. Gestión de Gastos

- Añadir gastos a proyectos
- Editar gastos existentes
- Capturar imágenes de recibos
- Procesamiento automático de recibos con IA

### 3. Procesamiento de Imágenes con IA

El sistema utiliza gemini-2.5-flash-preview-05-20 para:

- Extraer fecha
- Extraer monto total
- Identificar tipo de gasto
- Extraer detalles específicos

### 4. Exportación a Excel

- Genera reportes Excel basados en template
- Mantiene fórmulas y formato original
- Organiza gastos por categorías
- Incluye todos los detalles de gastos

## Áreas de Mejora Potencial

1. Backend Integration

- Implementar un backend para persistencia de datos
- Añadir autenticación de usuarios
- Almacenamiento en la nube para imágenes

2. UI/UX Enhancements

- Añadir modo oscuro
- Mejorar responsive design
- Implementar drag & drop para imágenes

3. Funcionalidades Adicionales

- Múltiples divisas
- Reportes analíticos
- Exportación a PDF
- Compartir proyectos entre usuarios

4. Optimizaciones

- Compresión de imágenes
- Caché de procesamiento IA
- Modo offline

## Guía de Desarrollo

### Añadir Nueva Categoría de Gasto

1. Agregar definición en `categoryFields`
2. Actualizar lógica de exportación Excel
3. Modificar UI para mostrar nuevos campos

### Modificar Procesamiento IA

1. Actualizar prompt en `receiptImageInput` event listener
2. Modificar mapeo de respuesta IA a campos
3. Actualizar validaciones de datos

### Añadir Nuevo Formato de Reporte

1. Crear nuevo template Excel
2. Implementar nueva lógica de exportación
3. Actualizar UI para nuevo formato

## Consideraciones de Seguridad

- Validar tipos de archivo
- Sanitizar datos de entrada
- Limitar tamaño de imágenes
- Implementar rate limiting

## Dependencias Externas

- TailwindCSS
- ExcelJS
- XLSX
- Google Gemini AI API
