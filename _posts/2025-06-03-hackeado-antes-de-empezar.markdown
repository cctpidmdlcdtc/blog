---
layout: post
title:  "Hackeado antes de empezar"
date:   2025-06-03 01:14:21 +0200
categories: [hacking, coturn, nftables, hetzner]
---

# Mundo ordinario

Lanzo [mi web](https://erosion.es) al vacío digital.

Subo vídeos cuidadosamente editados. Los pixelo, los recorto, les inyecto fragmentos de mi alma. Son buenos. O al menos, eso quiero creer. Cada frame pulido, cada transición calculada. Horas invertidas en la ilusión de que alguien, en algún lugar, esperaba esto que he creado.

Así que espero.


# Llamada a la aventura
Nadie entra.

Nadie parece saber que existo.

La red es vasta, pero ignora.


# Rechazo de la llamada
Quizás es normal. Es la fase inicial. Silencio como parte del proceso, dicen.

Mentiras reconfortantes. Me las trago, las saboreo.

Cada visita que no ocurre es una confirmación de que el universo permanece indiferente.


# Encuentro con el mentor (o el enemigo que lo reemplaza)
Entonces llegan ellos. No los visitantes. No los fans.

Primero los bots. Pero no los de los buscadores. Estos no son arañas, son hienas. Después llegan sus dueños, los hackers.

No les interesa lo que hago. No miran los vídeos. Solo el agujero. Para ellos, mi web no es arte: es infraestructura. Y la infraestructura existe para ser explotada.


# Cruce del umbral
Mi servidor _coturn_ abierto. Vulnerable.

Coturn tiene una "[funcionalidad](https://github.com/coturn/coturn/pull/1588)" documentada desde hace 3 años. Sigue abierta: won't fix (by design).

Los hackers lo ven. Lo usan. Amplificación DDoS mediante STUN/TURN reflection. Mi servidor se convierte en su arma, mi ancho de banda en su munición. Soy cómplice involuntario de batallas que no elegí.


# Pruebas, aliados y enemigos
Un día, Hetzner me envía el veredicto:

```
Unfortunately we have had to lock the IP address(es) below due to network issues.
```

Supe del email tras comprobar que la web no funcionaba, entrar en el panel de usuario de Hetzner y ver un amenazante banner. Eso me llevó a pensar si se habrían intentado ponerse en contacto conmigo antes de desconectarme.

Habían pasado semanas. Yo sin embargo había decidido que ese era el momento de empezar a emitir en directo todos los días, que la plataforma era ya lo suficientemente estable.

Murphy sabe cómo y cuándo golpearte, eso es innegable.


# Acercamiento a la caverna más profunda
Investigo. Dormir se vuelve un lujo que no puedo permitirme. Tengo que recuperar el control de mi web, tengo que volver a poner en marcha el juego.

Encuentro el problema. Yo soy el problema.

Los desarrolladores conocían el riesgo y decidieron que no era su problema. La responsabilidad, como siempre, recae en el usuario final, en el ingenuo que confía en los defaults.


# Prueba suprema
Decido construir el muro. No por heroísmo, sino por terquedad. Por el absurdo placer de resolver un problema que yo mismo creé.

El firewall no lo activo por defecto: lo invoco dinámicamente cuando lo necesito. Una solución elegante para un problema estúpido.

1. Defino una lista de ips que puedo manipular dinámicamente:

    ```shell
    table inet filter {
        # Set dinámico para IPs permitidas por Python
        set coturn_dynamic_ips {
            type ipv4_addr
            flags dynamic,timeout
            timeout 1h
            # Tamaño máximo del set (ajustar según necesidades)
            size 1000
        }

        chain input {
            type filter hook input priority 0; policy drop;
            
            # Permitir tráfico de loopback
            iif lo accept
            
            # Permitir tráfico establecido y relacionado
            ct state established,related accept
                        
            # Puerto estándar STUN/TURN TCP
            ip saddr @coturn_dynamic_ips tcp dport 3478 accept
            
            # Puerto estándar STUN/TURN UDP  
            ip saddr @coturn_dynamic_ips udp dport 3478 accept
            
            # Rango de puertos RTP para COTURN
            ip saddr @coturn_dynamic_ips udp dport 49152-65535 accept
    ```

1. De este modo, puedo capturar la ip en nginx y meterla en una cabecera:

    ```shell
    proxy_set_header X-Real-IP $remote_addr;
    ```

1. Y esa cabecera puedo capturarla desde el backend en python para usarla para configurar el cortafuegos:

    ```python
    ip_value = request.headers.get('X-REAL-IP')
    ```

Una PoC. Frágil como porcelana china. Grotesca en su simplicidad. Funciona.


# Recompensa (el elixir amargo)
Lo integro en la web. Todo parece sellado ahora, fortificado contra la ingenuidad propia y la malicia ajena.

Pruebo. Atacan. Lo bloquea.

Yo los oigo. Persisten. Ya no me afectan. Sus ataques rebotan inútilmente contra mi paranoia convertida en código.

La web resurge. Blindada. Los ataques continúan, pero ahora son mi espectáculo, mis bufones.

La paradoja: ahora que soy "seguro", el silencio es más denso. He construido la fortaleza perfecta para defender un contenido que nadie quiere consumir.


# El camino de regreso
No hay regreso.

No hay gloria.

La web sigue ahí. Abierta al público. Segura contra atacantes. Desierta de visitantes. Un monumento a la futilidad del esfuerzo solitario.


# Resurrección
Al final, tal vez esa era la lección que necesitaba aprender. No sobre seguridad web o firewalls o protocolos de red. Sino sobre la naturaleza fundamental de crear en el vacío: que el acto de creación tiene valor independiente de la recepción, que la defensa tiene mérito independiente del valor de lo defendido.


# Regreso con el elixir
Mi trofeo: métricas de una guerra invisible. Paranoia como feature, no como bug.

[Mi web](https://erosion.es) está sola, pero inexpugnable.

Y yo también estoy solo. Pero al menos ahora entiendo que esa soledad no es un bug: es una feature.
