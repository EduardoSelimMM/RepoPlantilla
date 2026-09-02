# Instrucciones

Ir al botón de "Use this template" -> "Create a new repository"

Llenar los campos

Ir a Settings -> Pages -> Build and deployment -> Source -> GitHub Actions

Modificar el archivo "_quarto.yml"

```
website:
  title: "Sitio web"
```
Cambiar el `title` por el título que le quieres dar a tu sitio. Por ejemplo: "Sitio web de John Doe"

```
website:
  title: "Sitio web de John Doe"
```

Modificar el archivo ".index.qmd"

```
---
title: "Bienvenido al sitio web"
---

Este es el sitio web. Navega a **Lecciones** para ver todo el contenido disponible.
```

Acá se llena con un mensaje de bienvenida e info que quieras compartir

```
---
title: "Prepársense para aprender"
---

Este es el sitio web del curso. Da click al botón **Lecciones** en la barra superior para ver todo el contenido disponible.

```

En la carpeta "lecciones" se encuentra cada uno de los notebooks de Quarto con el contenido individual. Por el momento no es necesario modificarlo

Este debe ser el último paso del despliegue por primera vez:
Crear un documento que se llame
".github/workflows/publish.yml"

Con el siguiente contenido

```
name: Publicar sitio Quarto en GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Clonar repositorio
        uses: actions/checkout@v4

      - name: Instalar Quarto
        uses: quarto-dev/quarto-actions/setup@v2

      - name: Instalar R
        uses: r-lib/actions/setup-r@v2
        with:
          r-version: "release"

      - name: Instalar paquetes de R
        uses: r-lib/actions/setup-r-dependencies@v2
        with:
          packages: |
            any::ggplot2
            any::dplyr
            any::knitr
            any::rmarkdown
            any::readr

      - name: Renderizar sitio
        run: quarto render

      - name: Subir artefacto para Pages
        uses: actions/upload-pages-artifact@v3
        with:
          path: _site

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Desplegar en GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
