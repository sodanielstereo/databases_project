# Biblioteca — proyecto integrador

Universidad del Valle · Bases de Datos · Equipo de cuatro integrantes.
Estado: estructura inicial; modelo, SQL, API y GUI pendientes.

## Arquitectura

HTML/CSS/JS → API REST Node.js/Express → PostgreSQL 16.
Se conserva el entorno Docker entregado por el curso (incluido Node 20).
La API irá en `node/proyecto/` y el esquema en `postgres/init/`.

## Arranque local

Requisitos: Docker Engine, Docker Compose y Git.
Desde esta carpeta:

```bash
cp .env.example .env
# Editar .env si se desean otras credenciales o puertos.
docker compose up -d postgres pgadmin
docker compose ps
docker compose exec postgres psql -U curso -d curso -c 'SELECT version();'
```

pgAdmin: http://localhost:5051. Servidor precargado: `postgres:5432`.
Contraseña de desarrollo por defecto: `curso`; usuario y base: `curso`.
Desde el equipo anfitrión, PostgreSQL está en `localhost:5433`.
Si cambias usuario/base en .env, adapta también los comandos y servers.json.
Cambiar la contraseña en .env después de inicializar la base no modifica
la contraseña ya almacenada en PostgreSQL.

```bash
docker compose logs --tail=50 postgres
docker compose stop                 # detener conservando datos
docker compose up -d postgres pgadmin # volver a arrancar
docker compose down                 # retirar contenedores, conservar datos
```

No borrar `postgres/data/`: contiene la base local.
Cada integrante tiene su propia base; Git comparte scripts y CSV, no los datos vivos.
Los scripts de `postgres/init/` solo se ejecutan durante la primera inicialización.
Para aplicar un cambio posterior, crear un script de migración revisado en
`postgres/scripts/` y ejecutarlo explícitamente con `psql -v ON_ERROR_STOP=1 -f`.
No reinicializar la base como procedimiento habitual de actualización.

## API (cuando corresponda)

```bash
docker compose up -d node
```

Sin package.json el servicio queda esperando; todavía no hay API en el puerto 3001.
La API deberá escuchar en 0.0.0.0 y usar el PORT del entorno.
Desde Node el host de PostgreSQL es `postgres`, no `localhost`.
El entorno original pasa credenciales administrativas a Node: antes de conectar
la API habrá que sustituirlas por roles de privilegios mínimos. El usuario
`curso` sirve para administración, no como usuario de toda la aplicación.
Versionar package.json y package-lock.json cuando se creen.

## Hitos oficiales

| Hito | Entrega | Evidencia |
|---|---|---|
| 1 · S8 | Modelo E-R, claves, cardinalidad/participación y cuatro perfiles | DBML, enlace, PDF/imagen, DDL y justificación de máximo una página |
| 2 · S16 | Esquema, carga CSV y cuatro consultas de reportes | SQL, semilla del curso, CSV propio y pruebas de errores |
| 3 · S20 | Normalización al menos 3FN | Dependencias funcionales y esquema revisado |
| 4 · semana 12 | Procedimiento y trigger de negocio | SQL y demo de 2–3 minutos o capturas |
| 5 · S28 | Roles mínimos, API, login con hash y OpenAPI | Código, documentación y colección de pruebas |
| 6 · S29–S32 | GUI integrada y sustentación | README reproducible, demo y preparación individual |

Guardar evidencia en docs/hitos/hito-N. Mantener una única versión del código;
no duplicar la API o el esquema en cada carpeta de hito. Marcar entregas con tags.
La definición íntegra está en docs/referencia/definicion-proyecto-final.md;
sus enlaces relativos originales apuntan a materiales del curso.

## Equipo

Completar nombres. Las responsabilidades son focos de trabajo, no compartimentos.
Todos deben explicar todo el sistema.

| Integrante | Foco | Revisión cruzada |
|---|---|---|
| 1 | Modelo, esquema y normalización | API |
| 2 | API, login y roles | BD |
| 3 | GUI e integración | Pruebas |
| 4 | Carga CSV, pruebas y documentación | GUI |

Para Hito 1: repartir entidades/claves, relaciones/reglas, perfiles y
justificación/exportación; revisar juntos el modelo final.
Ver GUIA-INICIO.md para Git y colaboración.

## Ajustes al entorno original

Proyecto Compose `biblioteca`, sin nombres fijos de contenedor; puertos locales
5433/5051/3001 para convivir con el laboratorio. Publicación solo en 127.0.0.1.
No se copiaron sesiones ni datos de pgAdmin del ZIP. Se conservaron las imágenes
del curso por compatibilidad; esta base aún no implementa funcionalidades.
