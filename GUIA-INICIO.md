# Inicio y colaboración

## 1. Docker en Linux Mint

En el equipo inspeccionado ya están Docker, Compose y Git. No reinstalarlos.
Desde una terminal normal:

```bash
docker --version
docker compose version
git --version
sudo systemctl start docker
docker info
```

Si `docker info` responde “permission denied”, comprobar con `sudo docker info`.
Para trabajar sin sudo, opcionalmente:

```bash
sudo usermod -aG docker "$USER"
```

Cerrar la sesión gráfica y volver a entrar. El grupo docker otorga privilegios
equivalentes a administrador. Si se prefiere no añadirlo, usar sudo delante de
los comandos Docker. Para iniciar el servicio en cada encendido, opcional:
`sudo systemctl enable docker`.

En otro equipo sin Docker: seguir la instalación oficial. Linux Mint basado
en Ubuntu usa UBUNTU_CODENAME (este equipo: noble), no el nombre de Mint (zena).
LMDE requiere las instrucciones Debian. Docker indica que Mint no tiene soporte
oficial directo: https://docs.docker.com/engine/install/ubuntu/ y
https://docs.docker.com/engine/install/debian/ .

## 2. Carpeta y primer arranque

Se puede usar directamente esta carpeta, o copiarla una sola vez a
`~/Documentos/proyectos/biblioteca` (crear antes la carpeta proyectos).
Ejecutar el resto de comandos desde la raíz de biblioteca.
No colocar el proyecto dentro de lab1 ni copiar bases de datos del laboratorio.

```bash
cp .env.example .env
docker compose config --quiet
docker compose up -d postgres pgadmin
docker compose ps
docker compose exec postgres psql -U curso -d curso -c 'SELECT version();'
```

Se espera postgres healthy y pgadmin en ejecución. Abrir http://localhost:5051.
Si un puerto está ocupado, cambiarlo en .env y repetir `up -d`.
Para diagnosticar: `docker compose logs --tail=50 postgres pgadmin`.
Para Hito 1 basta modelar en dbdiagram.io; tener PostgreSQL encendido no es
necesario para dibujar el modelo.

## 3. Un integrante crea el repositorio remoto

La carpeta entregada ya tiene Git inicializado en main, sin commits.
Si se parte de una copia sin .git, ejecutar `git init -b main`.
Configurar identidad local, sustituyendo los valores:

```bash
git config user.name "Tu nombre"
git config user.email "TU_CORREO_VERIFICADO_O_NOREPLY"
git status
git add .
git diff --cached --stat
git commit -m "chore: preparar entorno y estructura por hitos"
```

En GitHub crear un repositorio `biblioteca`, privado si el curso no exige público.
Crearlo vacío: no generar README, .gitignore ni licencia, pues ya hay archivos locales.
Invitar a los otros tres en Settings → Collaborators y esperar que acepten.
Sustituir TU_USUARIO por el propietario real:

```bash
git remote add origin https://github.com/TU_USUARIO/biblioteca.git
git push -u origin main
```

Para HTTPS usar el gestor de credenciales o un token; GitHub no acepta la contraseña
normal de la cuenta para Git. No escribir tokens en la URL ni en archivos del repo.
SSH es otra opción si ya tienen claves configuradas.

En GitHub crear seis Milestones, uno por hito, e Issues pequeñas con responsable,
hito y criterio de terminado. Configurar main para exigir PR y una aprobación
si el plan del repositorio lo permite; de lo contrario mantener esa regla como
acuerdo del equipo. Permitir merge commits para conservar los commits individuales.

## 4. Los otros tres clonan

```bash
mkdir -p ~/Documentos/proyectos
cd ~/Documentos/proyectos
git clone https://github.com/TU_USUARIO/biblioteca.git
cd biblioteca
git config user.name "Tu nombre"
git config user.email "TU_CORREO_VERIFICADO_O_NOREPLY"
cp .env.example .env
docker compose up -d postgres pgadmin
```

Cada quien configura su identidad y su .env. No compartir una misma cuenta GitHub.

## 5. Trabajo diario: rama por tarea y PR

main representa el trabajo integrado. Una feature branch contiene una tarea concreta;
un PR pide revisar e integrar sus cambios. No crear ramas permanentes por persona
ni una rama enorme por hito. Ejemplos: feat/h1-modelo-er, docs/h1-justificacion,
feat/h2-carga-csv, test/h2-csv-invalidos.

```bash
git switch main
git pull --ff-only origin main
git switch -c feat/h1-modelo-er
# Editar archivos y comprobar el resultado.
git add docs/hitos/hito-1
git commit -m "docs(h1): agregar modelo inicial de biblioteca"
git push -u origin feat/h1-modelo-er
```

En GitHub abrir PR hacia main. Explicar qué cambia, issue asociado y cómo se probó.
Otro integrante revisa; el autor corrige; tras aprobación integrar con
**Create a merge commit**. Se conservan los commits y la autoría de cada persona.

Actualizar una rama PERSONAL antes de integrarla:

```bash
git fetch origin
git rebase origin/main
# Si la rama ya estaba publicada y el rebase terminó bien:
git push --force-with-lease
```

Hacer esto con el árbol de trabajo limpio. Rebase vuelve a aplicar los commits
sobre main y cambia sus identificadores. Usarlo solo en una rama propia que nadie
más esté usando como base; nunca hacer rebase ni force push sobre main.
Si la rama todavía no está publicada, usar `git push -u origin NOMBRE_RAMA`.

Si hay conflicto: editar los archivos indicados, quitar los marcadores de conflicto,
conservar el resultado correcto, ejecutar `git add ARCHIVO` y `git rebase --continue`.
Para cancelar y volver al estado anterior: `git rebase --abort`.
No forzar el push si --force-with-lease rechaza: revisar primero los cambios remotos.

Si DOS personas comparten una rama, actualizar sin reescribir su historial:

```bash
git fetch origin
git merge origin/main
git push
```

Resolver conflictos con `git add ARCHIVO` y `git commit`; para cancelar,
`git merge --abort`. Rebase y merge son alternativas para actualizar la rama:
no hay que ejecutar los dos. La integración final sigue pasando por PR.
Referencia: https://docs.github.com/en/pull-requests/reference/pull-request-merges

Después de integrar el PR:

```bash
git switch main
git pull --ff-only origin main
git branch -d feat/h1-modelo-er
```

## 6. Cierre de hito

Revisar el checklist del README y guardar la evidencia en docs/hitos/hito-N.
El responsable de la entrega marca la versión integrada, una sola vez:

```bash
git switch main
git pull --ff-only origin main
git tag -a hito-1 -m "Entrega Hito 1: modelo E-R"
git push origin hito-1
```

Repetir con hito-2, etc. Un tag permite recuperar la entrega sin duplicar el proyecto.
En dbdiagram.io acordar quién aplica los cambios; exportar el DBML al repositorio
como fuente revisable, además del enlace y PDF/imagen pedidos. Git no coordina las
ediciones simultáneas hechas dentro de dbdiagram.io.
