# Resumen del proyecto de gestión de gastos

## Estado actual
- Aplicación SPA en `gastos.html` con Tailwind desde CDN y lógica en JavaScript vanilla.
- Persistencia en `localStorage` bajo la clave `gastos_empresa_proyectos`.
- Procesamiento opcional de recibos mediante la API de Google Gemini para auto-rellenar campos.
- Exportación de datos a la plantilla `gastos.xlsx`, incluyendo inserción de imágenes en la hoja **LOCAL TE support doc**.

## Cambios recientes
- Documentación técnica actualizada para reflejar la arquitectura real y el flujo completo de la aplicación.
- Eliminación de `js/config.js` y `styles.css` al confirmarse que no eran utilizados.
- Cada tarjeta de gasto permite eliminar el registro tras confirmación, mejorando el control de los proyectos.
- Se añadió un modal de previsualización del Excel para validar los datos por categoría antes de descargar la plantilla.
- El formulario de nuevo gasto preselecciona la categoría **OTHERS** y despliega sus campos para acelerar la captura móvil.
- La exportación a Excel limpia y prepara los rangos antes de escribir, evitando textos residuales y celdas fusionadas que ocultaban información.
- Las imágenes de los recibos dentro de cada gasto ahora se amplían en un modal al hacer clic, facilitando su consulta.
- Se corrigió el formulario de alta para que al abrirse desde el botón general precargue la categoría **OTHERS** y despliegue sus campos automáticamente.

## Próximos pasos sugeridos
- Implementar un backend que oculte la API key y permita sincronizar los proyectos entre dispositivos.
- Añadir validaciones adicionales en el formulario de gastos (por ejemplo, límites numéricos y formatos monetarios).
- Revisar la experiencia móvil para optimizar la captura de recibos y la navegación entre vistas.
