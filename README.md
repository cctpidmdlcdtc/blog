# Configurar GoatCounter con Jekyll en Debian
## Preparar el entorno en Debian
Instalar dependencias básicas

```shell
# Actualizar el sistema
sudo apt update && sudo apt upgrade -y

# Instalar Ruby, Git y dependencias de Jekyll
sudo apt install ruby-full build-essential zlib1g-dev git curl -y

# Configurar Ruby gems para el usuario actual
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Instalar Jekyll y Bundler
gem install jekyll bundler
```

## Crear o configurar tu blog Jekyll
Si es un blog nuevo:

```shell
# Crear nuevo sitio Jekyll
jekyll new mi-blog
cd mi-blog

# Inicializar Git
git init
```

Si ya tienes un blog Jekyll:

```shell
cd tu-blog-existente
```

## Configurar GoatCounter
1. Crear cuenta en GoatCounter

    Ve a https://www.goatcounter.com/
    Haz clic en "Sign up"
    Elige un subdominio (ej: mi-blog.goatcounter.com)
    Completa el registro

1. Obtener el código de tracking

    Después del registro, obtendrás un código similar a:

    ```html
    <script data-goatcounter="https://mi-blog.goatcounter.com/count"
            async src="//gc.zgo.at/count.js"></script>
    ```

## Integrar GoatCounter en Jekyll
1. En el layout principal

    Edita el archivo _layouts/default.html (o créalo si no existe):

    ```html
    <!DOCTYPE html>
    <html lang="{{ page.lang | default: site.lang | default: "en" }}">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>{% if page.title %}{{ page.title | escape }}{% else %}{{ site.title | escape }}{% endif %}</title>
        <meta name="description" content="{{ page.excerpt | default: site.description | strip_html | normalize_whitespace | truncate: 160 | escape }}">
        <link rel="stylesheet" href="{{ "/assets/main.css" | relative_url }}">
        
        <!-- GoatCounter Analytics -->
        {% if jekyll.environment == "production" %}
        <script data-goatcounter="https://erosion.goatcounter.com/count"
                async src="//gc.zgo.at/count.js"></script>
        {% endif %}
    </head>

    <body>
        <main class="page-content" aria-label="Content">
        <div class="wrapper">
            {{ content }}
        </div>
        </main>
    </body>
    </html>
    ```

1. Como include (más flexible)

    Crea el archivo _includes/goatcounter.html:

    ```html
    <!-- GoatCounter Analytics -->
    {% if jekyll.environment == "production" %}
    <script data-goatcounter="https://erosion.goatcounter.com/count"
            async src="//gc.zgo.at/count.js"></script>
    {% endif %}
    ```

    Luego inclúyelo en tu layout:

    ```html
    <head>
    <!-- otras meta tags -->
    {% include goatcounter.html %}
    </head>
    ```

## Configurar Jekyll para producción
1. Editar _config.yml

    ```yaml
    title: Mi Blog
    description: Descripción de mi blog
    url: "https://tu-dominio.com" # Solo para producción
    baseurl: "" # Si usas subdirectorio, ej: "/blog"

    # Configuración de construcción
    markdown: kramdown
    highlighter: rouge
    plugins:
    - jekyll-feed
    - jekyll-sitemap

    # Configuración de GoatCounter (opcional)
    goatcounter:
    domain: "mi-blog.goatcounter.com"
    ```

1. Usar la configuración en el include

    Si usas el método include, puedes hacer el código más dinámico:

    ```html
    <!-- _includes/goatcounter.html -->
    {% if jekyll.environment == "production" and site.goatcounter.domain %}
    <script data-goatcounter="https://{{ site.goatcounter.domain }}/count"
            async src="//gc.zgo.at/count.js"></script>
    {% endif %}
    ```

## Probar localmente

```shell
# Servidor de desarrollo (no carga GoatCounter)
bundle exec jekyll serve

# Servidor simulando producción (carga GoatCounter)
JEKYLL_ENV=production bundle exec jekyll serve
```

## Despliegue
1. GitHub Pages

    ```shell
    # Hacer push a GitHub
    git add .
    git commit -m "Añadir GoatCounter analytics"
    git branch -M main
    git remote add origin https://github.com/tu-usuario/tu-repo.git
    git push -u origin main

    # Activar GitHub Pages en la configuración del repositorio
    ```

1. Servidor propio

    ```shell
    # Construir el sitio para producción
    JEKYLL_ENV=production bundle exec jekyll build

    # Los archivos estarán en _site/
    # Cópialos a tu servidor web
    rsync -avz _site/ servidor:/var/www/html/
    ```

1. Netlify

    Conecta tu repositorio Git
    Comando de build: jekyll build
    Directorio de publicación: _site
    Variable de entorno: JEKYLL_ENV=production

## Verificar que funciona

    Visita tu sitio web
    Ve a tu panel de GoatCounter: https://mi-blog.goatcounter.com/
    Deberías ver las visitas aparecer en tiempo real

# Consejos adicionales
## Excluir tu propia IP

En GoatCounter, ve a Settings > Ignore IPs y añade tu IP.

## Personalizar el tracking

```html
<!-- Tracking personalizado para páginas específicas -->
<script data-goatcounter="https://mi-blog.goatcounter.com/count"
        data-goatcounter-settings='{"no_onload": true}'
        async src="//gc.zgo.at/count.js"></script>

<script>
// Enviar evento personalizado
window.goatcounter = {no_onload: true}
goatcounter.count({
    path: location.pathname + location.search + location.hash,
})
</script>
```

# Problemas comunes

## GoatCounter no aparece en desarrollo:

    Normal, solo se carga con JEKYLL_ENV=production

## No se ven estadísticas:

    Verifica que el subdominio sea correcto
    Comprueba que el script se carga (F12 > Network)
    Puede tardar unos minutos en aparecer

## Errores de construcción:

```shell
# Si tienes problemas con bundler
bundle install
bundle update
```
