# Instrucciones

## Despliegue por primera vez

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
OPCIONAL: En el archivo "_quarto.yml" encontrarás una parte que se ve así

```
format:
  html:
    theme: minty
    toc: true
    lang: es
```

Pues cambiar el `theme`. Ahora está en minty. Puedes seleccionar de entre las opciones

https://quarto.org/docs/output-formats/html-themes.html

Supongamos que me gustó el tema "superhero". Entonces lo cambio

```
format:
  html:
    theme: superhero
    toc: true
    lang: es
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

Una vez que exista el documento "publish.yml" se empezará a crear el sitio. Esto tomará unos minutos

Ir a "Actions". Dar click al único workflow y esperar a que termine

Una vez que termine el workflow te mostrará el link de tu sitio web.

Para que lo tengas a la mano todo el tiempo, haz lo siguiente:

Ve a la página principal de tu repositorio (aquí en GitHub). Del lado derecho encontrarás la palabra "About" con una tuerquita de lado derecho.

Marca la casilla de "Use your GitHub Pages website" y "Save changes". Ahora verás la dirección de tu sitio justo debajo de la palabra "About"

## Agregar lecciones propias

Probablemente tengas algunas lecciones propias en Quarto, además de las que ya están disponibles y también quieras que los estudiantes las revisen

```
---
title: "Bienvenido"
---

Este es mi sitio creado con Quarto, R y GitHub Actions.

Acá usamos nuestra sintaxis clásica de RMarkdown/Quarto

## Un vistazo a los datos

```{r}
#| echo: false
#| message: false
library(dplyr)
library(ggplot2)
```

```{r}
head(mtcars)
```

## Un gráfico

```{r}
#| echo: false
#| warning: false
ggplot(mtcars, aes(x = wt, y = mpg, color = as.factor(cyl))) +
  geom_point(size = 3) +
  labs(
    title = "Peso del auto vs. rendimiento",
    x = "Peso (miles de lb)",
    y = "Millas por galón",
    color = "Cilindros"
  ) +
  theme_minimal()
```

## Una tabla resumen

```{r}
#| echo: false
mtcars |>
  group_by(cyl) |>
  summarise(mpg_promedio = mean(mpg)) |>
  knitr::kable()
```

Un resumen del famoso dataset "iris"

```{r}
summary(iris)
```


```
**IMPORTANTE:** Se me hace que acá tendremos que poner una foto porque los acentos inversos se hacen bolas

Ve a la carpeta Lecciones

+ Allí encontrarás el archivo "_plantilla.qmd"

+ Copia su contenido y pégalo en un nuevo archivo que se llame "mi-leccion-01.qmd"

+ Modifica el contenido, copiando y pegando el contenido de tu archivo .qmd



