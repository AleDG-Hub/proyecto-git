# Práctica de Git – Gestión de un proyecto web

**Alumno:** Alejandro Díaz Gómez
**Repositorio remoto:** https://github.com/TU_USUARIO/proyecto-git  *(cambiar por el enlace real)*

Objetivo: practicar los comandos básicos y algunos avanzados de Git (repositorio, commits, ramas, merge, conflictos y remotos).

---

## 1. Inicialización del repositorio

### 1.1 Crear la carpeta del proyecto y entrar en ella

```bash
mkdir proyecto-git
cd proyecto-git
```

- `mkdir` crea la carpeta del proyecto.
- `cd` nos sitúa dentro de ella desde la terminal.

### 1.2 Inicializar el repositorio

```bash
git init -b main
```

Salida:

```
Initialized empty Git repository in /ruta/proyecto-git/.git/
```

`git init` crea la carpeta oculta `.git`, donde Git guarda todo el historial. Con `-b main` la rama principal se llama `main` (en versiones antiguas de Git se llamaría `master` por defecto).

---

## 2. Configuración inicial

```bash
git config --global user.name "Alejandro Díaz Gómez"
git config --global user.email "soulsenor8@gmail.com"
```

Comprobación:

```bash
git config --global --list
```

- `user.name` y `user.email` son los datos que aparecen como autor de cada commit.
- `--global` guarda la configuración para todos los repositorios del equipo. Sin `--global` solo se aplicaría a este repositorio.
- El correo debe ser el mismo de la cuenta de GitHub para que los commits aparezcan asociados al perfil.

---

## 3. Primer commit

### 3.1 Crear `index.html`

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Proyecto Git</title>
</head>
<body>
    <h1>Mi proyecto con Git</h1>
    <p>Este es el primer párrafo de la página.</p>
</body>
</html>
```

### 3.2 Añadir al staging y hacer commit

```bash
git status
git add index.html
git commit -m "Primer commit: index.html básico"
```

Salida:

```
[main (root-commit) 4d3994d] Primer commit: index.html básico
 1 file changed, 11 insertions(+)
 create mode 100644 index.html
```

- `git status` muestra el estado: el archivo aparece como *untracked* (sin seguimiento).
- `git add` lo pasa al **área de preparación (staging area)**, es decir, lo marca para el próximo commit.
- `git commit -m` guarda una "foto" del proyecto en el historial con un mensaje que describe el cambio.

---

## 4. Creación de ramas

```bash
git branch desarrollo
git checkout desarrollo
```

O en un solo comando:

```bash
git checkout -b desarrollo
```

Salida:

```
Switched to a new branch 'desarrollo'
```

- `git branch desarrollo` crea la rama.
- `git checkout desarrollo` nos cambia a ella.
- `git checkout -b` hace las dos cosas a la vez.
- Una rama permite trabajar en cambios sin tocar `main` hasta que estén listos.

Para ver en qué rama estamos: `git branch` (la actual se marca con `*`).

---

## 5. Modificación y merge

### 5.1 Modificar `index.html` en la rama `desarrollo`

Se añade un párrafo debajo del primero:

```html
    <p>Este es el primer párrafo de la página.</p>
    <p>Párrafo añadido en la rama desarrollo.</p>
```

### 5.2 Commit de los cambios

```bash
git add index.html
git commit -m "Añadido párrafo en rama desarrollo"
```

Salida:

```
[desarrollo 5ded26c] Añadido párrafo en rama desarrollo
 1 file changed, 1 insertion(+)
```

### 5.3 Volver a `main` y fusionar

```bash
git checkout main
git merge desarrollo
```

Salida:

```
Updating 4d3994d..5ded26c
Fast-forward
 index.html | 1 +
 1 file changed, 1 insertion(+)
```

- `git merge desarrollo` trae a `main` los cambios de `desarrollo`.
- Ha sido un merge **Fast-forward**: como `main` no tenía commits nuevos desde que se creó `desarrollo`, Git simplemente avanza `main` hasta el último commit de `desarrollo`. No se crea commit de merge y no hay conflictos.

---

## 6. Resolución de conflictos

### 6.1 Crear la rama `nueva-funcionalidad`

```bash
git checkout -b nueva-funcionalidad
```

### 6.2 Modificar `index.html` en `nueva-funcionalidad`

```html
    <p>Párrafo añadido en la rama desarrollo.</p>
    <p>Párrafo de la nueva funcionalidad.</p>
```

### 6.3 Commit

```bash
git add index.html
git commit -m "Añadido párrafo en nueva-funcionalidad"
```

### 6.4 Volver a `main` y modificar el mismo sitio del archivo

```bash
git checkout main
```

En `main` se añade un párrafo distinto **en la misma línea**:

```html
    <p>Párrafo añadido en la rama desarrollo.</p>
    <p>Párrafo diferente añadido en main.</p>
```

### 6.5 Commit e intento de merge

```bash
git add index.html
git commit -m "Añadido párrafo diferente en main"
git merge nueva-funcionalidad
```

Salida:

```
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

**¿Por qué hay conflicto?** Las dos ramas han cambiado la misma parte del mismo archivo de forma diferente, así que Git no sabe cuál de las dos versiones quedarse y nos pide que lo decidamos nosotros.

`git status` lo muestra así:

```
Unmerged paths:
  (use "git add <file>..." to mark resolution)
	both modified:   index.html
```

Git marca el conflicto dentro de `index.html`:

```html
<<<<<<< HEAD
    <p>Párrafo diferente añadido en main.</p>
=======
    <p>Párrafo de la nueva funcionalidad.</p>
>>>>>>> nueva-funcionalidad
```

- Entre `<<<<<<< HEAD` y `=======` está la versión de la rama actual (`main`).
- Entre `=======` y `>>>>>>> nueva-funcionalidad` está la versión de la rama que fusionamos.

**Resolución:** se edita el archivo a mano, se decide qué queda (en este caso se conservan los dos párrafos) y se borran las marcas `<<<<<<<`, `=======` y `>>>>>>>`:

```html
<body>
    <h1>Mi proyecto con Git</h1>
    <p>Este es el primer párrafo de la página.</p>
    <p>Párrafo añadido en la rama desarrollo.</p>
    <p>Párrafo diferente añadido en main.</p>
    <p>Párrafo de la nueva funcionalidad.</p>
</body>
```

Después se marca como resuelto y se termina el merge con un commit:

```bash
git add index.html
git commit -m "Merge nueva-funcionalidad: conflicto resuelto"
```

Salida:

```
[main 5cffd3e] Merge nueva-funcionalidad: conflicto resuelto
```

Si en algún momento se quiere cancelar el merge en lugar de resolverlo: `git merge --abort`.

### Historial final

```bash
git log --oneline --graph --all
```

```
*   5cffd3e Merge nueva-funcionalidad: conflicto resuelto
|\
| * 02b2cbf Añadido párrafo en nueva-funcionalidad
* | 4c13ed6 Añadido párrafo diferente en main
|/
* 5ded26c Añadido párrafo en rama desarrollo
* 4d3994d Primer commit: index.html básico
```

Se ve cómo las dos ramas se separan y vuelven a unirse en el commit de merge.

---

## 7. Trabajo con repositorios remotos

### 7.1 Crear el repositorio en GitHub

1. Entrar en https://github.com y pulsar **New repository**.
2. Nombre: `proyecto-git`, visibilidad **Public**.
3. **No** marcar "Add a README" (el proyecto local ya tiene historial y daría conflicto al subir).
4. Pulsar **Create repository** y copiar la URL: `https://github.com/TU_USUARIO/proyecto-git.git`

### 7.2 Añadir el remoto y subir los cambios

```bash
git remote add origin https://github.com/TU_USUARIO/proyecto-git.git
git remote -v
git push -u origin main
git push origin desarrollo nueva-funcionalidad
```

- `git remote add origin <url>` enlaza el repositorio local con el de GitHub. `origin` es el nombre que se le da al remoto por convención.
- `git remote -v` comprueba que el remoto está bien añadido.
- `git push -u origin main` sube la rama `main`. El `-u` deja enlazada la rama local con la remota, así las siguientes veces basta con `git push`.
- La última línea sube también las otras dos ramas para que se vean en GitHub.

Al hacer `push`, GitHub pide autenticación: el usuario de GitHub y, como contraseña, un **Personal Access Token** (GitHub ya no acepta la contraseña de la cuenta desde la terminal).

---

## Resumen de comandos utilizados

| Comando | Para qué sirve |
|---|---|
| `git init` | Crear un repositorio nuevo |
| `git config` | Configurar nombre y correo del autor |
| `git status` | Ver el estado de los archivos |
| `git add` | Pasar cambios al área de preparación |
| `git commit -m` | Guardar los cambios en el historial |
| `git branch` | Crear o listar ramas |
| `git checkout` / `checkout -b` | Cambiar de rama / crearla y cambiar |
| `git merge` | Fusionar una rama en la actual |
| `git log --oneline --graph --all` | Ver el historial con las ramas |
| `git remote add` | Enlazar con un repositorio remoto |
| `git push` | Subir los commits al remoto |
