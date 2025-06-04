---
layout: post
title:  "Indie SOLO game developer"
date:   2025-06-03 17:34:21 +0200
categories: [métricas]
---


# Mundo ordinario
Despierto, reviso la web. La bbdd permanece inmutable, como una roca que se deshace con el tiempo, pero no con la mirada. Nadie entra. Nadie sale. El circuito real espera, los coches esperan, la página espera. Yo también espero.


# Llamado a la aventura
Dice ChatGPT que además de publicar 3 vídeos cortos diarios, debería hacer un blog. Seguro que mejora el SEO muchíiiiiiiiisimo. Sarcasmo nivel planeta. Me río yo solo, porque nadie más lo hará.

Instalo Jekyll y Bundler y lo configuro con cariño:

```shell
gem install jekyll bundler
```


# Rechazo de la llamada
El enemigo es la [erosión](https://erosion.es): cada día, menos ganas, menos visitas, menos fe.

¿Para qué escribir un blog? Nadie lo va a leer.


# Encuentro con el mentor

GoatCounter hace que solo 2 líneas te separen del contador de tu soledad. Suena fantástico.

Lo configuro con precisión suiza, para saber exactamente cuántos no vienen, metiendo esto en el _head_ del _layouts/default.html_:

```html
<script data-goatcounter="https://erosion.goatcounter.com/count"
        async src="//gc.zgo.at/count.js"></script>
```

Y retoco el _config.yml_ de mi blog:

```yaml
title: Erosión continua
email: cctpidmdlcdtc@gmail.com
description: >-
  Explora la frontera entre lo real y lo digital: 
  coches RC, videojuegos, automatización y aventuras tecnológicas con alma. 
baseurl: "/blog" # domain.com/blog es mejor para el seo que blog.domain.com
url: "https://erosion.es"
permalink: /:year/:month/:day/:title/
theme: minima
plugins:
  - jekyll-feed
goatcounter:
  domain: "erosion.goatcounter.com"
```


# Cruzando el umbral
Lanzo el blog:

```shell
# Servidor simulando producción (carga GoatCounter)
JEKYLL_ENV=production bundle exec jekyll serve
```

Lo reviso desde el móvil y el ordenador, como si esperara sorprenderme. El contador sigue en cero. La única notificación es la de mi propio acceso, que ignoro para no romper la estadística perfecta de la nada.


# Recompensa
La recompensa es saber, con precisión quirúrgica, que nadie viene. Y aun así, el circuito sigue ahí. El código sigue ahí. Yo sigo ahí.


# Regreso con elixir
Decido reemplazar la home con un haiku:

```html
<h1>Error 418:</h1>
<p>El servidor existe<br>Pero tú no</p>

<script>
let totalVisitors = 0; // Spoiler: siempre será 0
</script>
```
