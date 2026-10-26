# Práctica Git

Alejandro Díaz Gómez
Repositorio: https://github.com/AleDG-Hub/proyecto-git

## 1. Inicialización del repositorio

```bash
mkdir proyecto-git
cd proyecto-git
git init
```

## 2. Configuración inicial

```bash
git config --global user.name "Alejandro Díaz Gómez"
git config --global user.email "soulsenor8@gmail.com"
```

## 3. Primer commit

Se crea `index.html` con un HTML básico.

```bash
git add index.html
git commit -m "Primer commit: index.html básico"
```

## 4. Creación de ramas

```bash
git checkout -b desarrollo
```

## 5. Modificación y merge

Se añade un párrafo a `index.html` en la rama desarrollo.

```bash
git add index.html
git commit -m "Añadido párrafo en rama desarrollo"
git checkout main
git merge desarrollo
```

## 6. Resolución de conflictos

```bash
git checkout -b nueva-funcionalidad
# se añade un párrafo en index.html
git add index.html
git commit -m "Añadido párrafo en nueva-funcionalidad"

git checkout main
# se añade otro párrafo distinto en el mismo sitio
git add index.html
git commit -m "Añadido párrafo diferente en main"

git merge nueva-funcionalidad
```

Sale un conflicto porque las dos ramas cambian la misma línea. Se abre `index.html`, se dejan los dos párrafos y se borran las marcas `<<<<<<<`, `=======` y `>>>>>>>`. Después:

```bash
git add index.html
git commit -m "Merge nueva-funcionalidad: conflicto resuelto"
```

## 7. Repositorio remoto

Se crea el repositorio `proyecto-git` en GitHub y se sube:

```bash
git remote add origin https://github.com/AleDG-Hub/proyecto-git.git
git push -u origin main
git push origin desarrollo nueva-funcionalidad
```
