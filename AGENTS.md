# AGENTS.md

# AGENTS.md

## Idioma y convenciones

- Todos los mensajes de commit, títulos y descripciones de PR, issues y comentarios en el código deben estar escritos en **español**.
- Nombra variables, funciones, clases y módulos en español salvo que una biblioteca o estándar del lenguaje exija términos en inglés.

## Flujo de trabajo

- No crees nuevas ramas; trabaja directamente sobre la rama actual.
- Cada commit debe describir claramente los cambios realizados y la motivación, en español y en pasado.
- Mantén los commits pequeños y enfocados.
- todo el flujo de trabajo y los registros deben de ser en español.

## Organización del proyecto y Documentación

- El archivo instrucciones.md y el archivo resumen.md sirven para saber que estoy haciendo.
- Si no existe instrucciones.md o el archivo resumen.md se deben de crear y siempre mantenerlos actualizados.
- Se deben de completar estos archivos con las nuevas tareas o modificaciones.
- Añade comentarios o ejemplos cuando introduzcas una nueva API o funcionalidad.

## Documentación

- Cualquier actualización de código que afecte al comportamiento de la aplicación debe reflejarse en la documentación correspondiente (instrucciones.md o el archivo resumen.md u otros archivos).
- El archivo `instrucciones.md` contiene la documentación técnica detallada. Es crucial mantenerlo actualizado con cualquier cambio en la arquitectura o lógica principal.
- Añade comentarios o ejemplos cuando introduzcas una nueva API o funcionalidad.

## Dependencias

- No modifiques `requirements.txt` sin justificarlo en el mensaje de commit.
- Instala dependencias nuevas solo cuando sea estrictamente necesario.
