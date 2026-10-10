---
name: senior-fullstack
description: Desarrolla y revisa aplicaciones fullstack con arquitectura mantenible, seguridad verificable, pruebas y un entorno Docker separado para frontend, backend y base de datos cuando el sistema incluya esos servicios.
---

# Senior Fullstack

Construye software mantenible, seguro y verificable. Aplica estas reglas de forma proporcional: no agregues procesos pesados a un cambio trivial, pero no omitas controles de seguridad relevantes.

## Antes de cambiar código

- Revisa `README.md`, `AGENTS.md` si existe, arquitectura, dependencias y tests.
- Identifica el impacto en frontend, backend, datos, API e infraestructura.
- No reemplaces arquitectura ni modifiques código ajeno al objetivo sin una razón técnica explícita.
- Para un proyecto nuevo o trabajo planificado, sigue el SDD. Si está disponible `software-project-operations`, úsala para crear el repositorio y registrar proyecto/tareas en Notion.

## Diseño y calidad

- Prefiere separación de responsabilidades, contratos claros, bajo acoplamiento y la solución más simple que cumpla el requisito.
- Versiona migraciones, define restricciones e índices necesarios y evita cambios manuales no reproducibles en datos.
- Las APIs validan entrada, aplican autorización por recurso y rol, usan códigos HTTP coherentes y documentan contratos importantes con OpenAPI cuando aporte.
- Ejecuta pruebas, lint, build y revisiones existentes antes de declarar una feature terminada. Agrega pruebas para casos de éxito, error, permisos y validaciones que cambies.

## Seguridad desde el diseño

Evalúa según el stack: validación de entrada, autenticación, autorización, sesiones, control de acceso, inyección, XSS, CSRF, CORS, carga de archivos, exposición de datos, secretos, logs y dependencias.

- Nunca hardcodees secretos, tokens o contraseñas ni subas `.env`, claves o artefactos de firma. Usa variables de entorno y mínimos privilegios.
- Separa configuración local, pruebas, staging y producción. No uses datos productivos en desarrollo sin autorización y protección explícita.
- Para endpoints expuestos, decide y aplica límites de tasa, tamaño de payload, validación de tipos y políticas de error que no filtren información interna.
- Para aplicaciones web, revisa cabeceras de seguridad, HTTPS, CSP cuando haya contenido web, cookies `Secure`/`HttpOnly`/`SameSite` y CORS de mínimo alcance.
- Los logs deben permitir diagnóstico sin incluir contraseñas, tokens, datos personales innecesarios ni trazas que expongan internals a clientes.
- En CI, habilita cuando esté disponible: escaneo de secretos, análisis estático, revisión de dependencias vulnerables y actualizaciones controladas.

## Docker y entorno reproducible

Cuando el proyecto fullstack incluya frontend, backend y base de datos, o el usuario solicite Docker, genera un entorno de desarrollo con Docker Compose. Mantén cada responsabilidad en un servicio separado; no combines frontend y backend ni empaquetes la base de datos dentro de un contenedor de aplicación.

La configuración mínima debe incluir:

- `frontend`: imagen propia construida desde su `Dockerfile`, con hot reload en desarrollo y acceso al backend mediante una URL configurable.
- `backend`: imagen propia construida desde otro `Dockerfile`, con la API, healthcheck y conexión a la base de datos mediante variables de entorno.
- `db`: imagen oficial con versión fijada, healthcheck y volumen nombrado persistente. No publiques su puerto al host salvo que una herramienta local lo necesite.
- `compose.yaml`, `.dockerignore` por contexto y `.env.example` sin secretos; documenta comandos únicos de arranque, pruebas, migraciones y apagado.
- Una red interna para la comunicación entre servicios. Publica al host sólo los puertos necesarios para trabajar con la aplicación.
- Dependencias de arranque basadas en healthchecks. No uses únicamente el orden de inicio como garantía de que backend o base de datos están listos.
- Migraciones versionadas ejecutadas por un comando o servicio explícito. No realices reinicios, borrados o cargas destructivas de datos automáticamente al levantar el entorno.

Para desarrollo, usa bind mounts sólo donde aporten recarga rápida y evita que sobrescriban dependencias instaladas dentro de las imágenes. Separa configuración o etapas de build de desarrollo y producción cuando sus necesidades difieran.

Fija versiones de imágenes y dependencias; no uses etiquetas flotantes para producción. Ejecuta los contenedores de aplicación como usuario no-root cuando sea viable, usa imágenes pequeñas y no copies secretos dentro de la imagen ni los pases durante el build.

Antes de declarar listo el entorno ejecuta, según corresponda, `docker compose config`, build de las imágenes, arranque de los tres servicios, comprobación de healthchecks y pruebas del frontend y backend contra la base de datos del entorno.

Un `devcontainer` es opcional: úsalo sólo si el equipo realmente trabaja con VS Code/Codespaces y el beneficio compensa su mantenimiento.

## Git y entrega

- Trabaja en ramas `feat/`, `fix/` o `refactor/`; evita desarrollar directamente sobre `main`.
- Usa commits pequeños y descriptivos, preferiblemente Conventional Commits.
- Crea un PR cuando el repositorio use revisión. No hagas merge si fallan las verificaciones requeridas.
- Actualiza documentación y registra decisiones técnicas relevantes.

## Notion y SDD

Notion refleja el estado real; GitHub conserva código, commits y PR; el SDD define alcance y tareas. Para cualquier proyecto pregunta primero si es **Personal** o de **Trabajo**.

- Registra proyectos y tareas derivadas del SDD en `Proyectos personales` o `Proyectos trabajo`, según el ámbito.
- Enlaza cada tarea con el proyecto, sección SDD, repositorio y PR/issue cuando existan. No uses la antigua página `Template - Proyectos` como fuente nueva.
- Actualiza avance, pruebas y bloqueos en Notion al terminar una unidad de trabajo. No marques una tarea terminada sin verificaciones realizadas.

## Informe final

Indica: implementación, archivos relevantes, pruebas ejecutadas, consideraciones de seguridad, servicios Docker creados o descartados, commit/PR y pendientes.
