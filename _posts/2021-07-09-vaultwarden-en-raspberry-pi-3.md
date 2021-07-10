---
layout: single
class: wide
title: Vaultwarden en Raspberry Pi 3
header:
  teaser: ../assets/posts/raspberrypi.jpg
tags:
  - seguridad
  - guia
  - raspberrypi
---

En el artículo previo que escribí hace ya un tiempo, menciono algunos gestores de contraseñas y los invito a hacer uso de ellos. La realidad es que desde que escribí el artículo en cuestión, hasta el día de hoy, he usado los tres gestores que menciono, todos con sus ventajas y sus desventajas. **Firefox Lockwise** era, en aquel entonces, una buena alternativa pero muy inmadura y con unas cuantas limitaciones (desconozco al día de hoy cómo progresó su desarrollo); **Bitwarden**, por su parte, me pareció una alternativa excelente, pero me terminé mudando a **KeePass** dado que me brindaba esa extraña seguridad absoluta el hecho de tener las contraseñas conmigo y no alojadas en servidores de terceros (más allá de que no hay motivos para desconfiar de Bitwarden más que por mera paranoia), y también porque creía que Bitwarden no admitía OTP (lo cuál es mentira).

Pero todo cambió hace unas semanas cuando me topé en GitHub con [Vaultwarden](https://github.com/dani-garcia/vaultwarden), un **servidor compatible con Bitwarden escrito en Rust**. Se destaca por incluir todas las ventajas que ofrece Bitwarden, sumadas algunas otras, como el **soporte para organizaciones, soporte para YubiKey, Duo, Authenticator y U2F, archivos adjuntos y más**. Además afirman que es una buena alternativa para "auto hostear" el servidor en dispositivos con pocos recursos (¿se referirán a la Raspberry Pi? Ya lo sabremos...).

## Requisitos previos

Si bien a continuación voy a indicar mi paso a paso para hostear Vaultwarden en mi RPi, es sólo eso, **la manera en la que YO lo hice**. Aclaro esto porque la [Wiki](https://github.com/dani-garcia/vaultwarden/wiki) está bien explicada y super detallada, y ofrece además un montón de opciones por cada paso que avancen. Les recomiendo encarecidamente que la lean detenidamente.

En mi caso cuento con una Raspberry Pi 3B con Arch Linux ARM como sistema operativo. Los paquetes que instalé fueron los siguientes:

* `docker` - Lo usaremos precisamente para levantar nuestra instancia de Vaultwarden junto con el proxy. En mi caso usaré Caddy como proxy, pero en la [Wiki](https://github.com/dani-garcia/vaultwarden/wiki/Proxy-examples) tienen ejemplos para implementar Nginx, Apache, Traefik y HAproxy. También es posible usar Podman en reemplazo de Docker.
* `docker-composoe` - Sólo para hacernos la vida más fácil. Lógicamente deberían usar `podman-compose` si usan Podman en vez de Docker.
* `fail2ban` - Nos permitirá bloquear las IPs que fallen consecutivamente al querer ingresar tanto al Web Vault como al panel de administración (si es que deciden usarlo). Lógicamente es posible correrlo con Docker, para lo cuál van a tener que leer la Wiki para ajustarse a las recomendaciones dadas.
* `rclone` - Es la reliquia que nos respaldará en la nube nuestros backups. Al igual que `fail2ban`, es posible levantarlo en un contenedor con Docker, junto con todo lo anterior.
* `certbot` - Nos generará nuestro certificado SSL para poder usar https y que nuestros amigos digan *WOOOO*. Y de paso previene ataques MITM.
* `s-nail` - Simplemente lo uso dentro del script que se encarga de hacer los backups para alertarme vía mail cuando se alcanza cierto uso de la capacidad de la nube y así poder ingresar y hacer limpieza. Posiblemente en un futuro lo automatice aún más, pero por lo pronto así funciona correctamente.
* Haré también uso de servicios y timers gracias a systemd.

Por otro lado, hay que tener en cuenta todo el apartado de networking. En mi caso tuve que meter mano en el router para fijar la IP privada de mi Raspberry Pi, crear los servidores virtuales para que las peticiones a la IP pública sean redirigidas por el router a mi RPi, y finalmente configurar el DNS dinámico con [No-IP](https://www.noip.com/) (el panel de administración de mi router admite hacer dicha configuración allí mismo). En criollo, lo que logramos es que al *pegarle* a `https://mivault.ddns.net` le estamos *pegando* a nuestra Raspberry Pi, que es en definitiva lo que queremos lograr. ¿Es obligatorio liberar al mundo nuestro Vault? Claro que no, pero yo lohice así por conveniencia personal.



## Paso 1: Armado de directorios y docker-compose

Conectado por USB a mi RPi tengo un disco externo que lo uso lo más que puedo para evitar hacer un uso excesivo de la tarjeta SD y afectar a su vida útil. El mismo lo tengo montado en la ruta `/mnt/disco`. Con lo cual me muevo dentro con `cd /mnt/disco` y creo algunos directorios que van a ser de ayuda:

```bash
mkdir -p vaultwarden/{caddy/{caddy-config,caddy-data},vaultwarden-data/logs}
```

Es decir, nos debería quedar la siguiente estructura:

```
.
|->	vaultwarden
    |-> caddy
    |    |-> caddy-config
    |    |-> caddy-data
    |-> vaultwarden-data
         |-> logs
```

Una vez hecho esto, procedemos a armar el archivo `docker-compose.yml`. En mi caso he tomado como referencia el que figura en la documentación del proyecto y le he añadido algunas cosas más. Mi editor favorito es `nvim`, así que ejecuto:

```bash
nvim vaultwarden/docker-compose.yml
```

Y una vez dentro pegamos lo siguiente:

```yaml
version: '3'

services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: always
    environment:
      - WEBSOCKET_ENABLED=true  # Enable WebSocket notifications.
      - WEB_VAULT_ENABLED=true
      - WEB_VAULT_FOLDER=web-vault/
      - EXTENDED_LOGGING=true
      - LOG_FILE=/data/logs/access.log
    volumes:
      - ./vaultwarden-data:/data
      - ./vaultwarden-data/logs:/data/logs

  caddy:
    image: caddy:2
    container_name: caddy
    restart: always
    ports:
      - 80:80  # Needed for the ACME HTTP-01 challenge.
      - 443:443
    volumes:
      - ./caddy/Caddyfile:/etc/caddy/Caddyfile:ro
      - ./caddy/caddy-config:/config
      - ./caddy/caddy-data:/data
    environment:
      - DOMAIN=<DOMAIN>					   # Your domain, prefixed with http or https.
      - EMAIL=<EMAIL>			           # The email address to use for ACME registration.
      - LOG_FILE=/data/access.log
```

Como ven, se levantan dos contenedores, uno con `vaultwarden` y otro con `caddy`. En éste último sólo resta añadir su email y el dominio obtenido en [No-IP](https://www.noip.com/). El comentario por defecto indica que el correo debe ser el usado en la registración ACME. Básicamente es el que le brindamos a `certbot` cuando generamos el certificado (si no lo hicieron, deben ejecutar `certbot certonly` con permisos de root y seguir los pasos que les indica el programa).

Por otro lado, se puede observar que indico algunas variables de entorno al contenedor de `vaultwarden`. Si se dirigen a la documentación, van a leer que hay dos maneras de configurar Vaultwarden: una es a través de variables de entorno, y la otra es usando el panel de administración web. En mi caso escogí la primera, con lo cual las variables de entorno se sobreescriben. Sin embargo, hay un puñado de ellas que no pueden definirse usando el panel de administración en cuestión, y es por eso que las defino en el archivo `docker-compose.yml`. Las opciones de logging son obligatorias en mi caso, porque las necesitará `fail2ban` para castigar a los atrevidos.

**IMPORTANTE:** Antes de la primera ejecución que efectuemos debemos añadir la variable de entorno `ADMIN_TOKEN` en el contenedor `vaultwarden` hasta que ingresemos por primera vez al panel de administración. Una vez que lo hagamos, dicho token quedará almacenado en el archivo `config.json` que genera el mencionado panel de administración, y ahí sí podremos eliminar la variable mencionada del `docker-compose.yml`. El valor de dicha variable será la contraseña de acceso al panel de administración, con lo cuál se recomienda que sea robusta y aleatoria. En mi caso la generé con el comando `openssl rand -base64 48`. **Este token es secreto y en situaciones normales no debe compartirse con nadie**.

## Paso 2: Creación de archivos de configuración e instalación del web vault

Las personas más atentas habrán notado que en los volúmenes del contenedor de `caddy` hay un `Caddyfie` que aún no creamos. Es eso lo que vamos a hacer:

```bash
nvim vaultwarden/caddy/Caddyfile
```

Y en este caso uso la configuración por defecto:

```nginx
{$DOMAIN}:443 {
  log {
    level INFO
    output file {$LOG_FILE} {
      roll_size 10MB
      roll_keep 10
    }
  }

  # Use the ACME HTTP-01 challenge to get a cert for the configured domain.
  tls {$EMAIL}

  # This setting may have compatibility issues with some browsers
  # (e.g., attachment downloading on Firefox). Try disabling this
  # if you encounter issues.
  encode gzip

  # Notifications redirected to the WebSocket server
  reverse_proxy /notifications/hub vaultwarden:3012

  # Proxy everything else to Rocket
  reverse_proxy vaultwarden:80 {
       # Send the true remote IP to Rocket, so that vaultwarden can put this in the
       # log, so that fail2ban can ban the correct IP.
       header_up X-Real-IP {remote_host}
  }
}
```

No hay nada más que añadir. Para más info, como siempre, dirigirse al repo oficial.

Continúo con la instalación del `web-vault`. La forma más simple es descargándola desde el [repo](https://github.com/dani-garcia/bw_web_builds/releases). En mi caso ejecuté:

``` bash
wget -O web-vault.tar.gz https://github.com/dani-garcia/bw_web_builds/releases/download/v2.20.4b/bw_web_v2.20.4b.tar.gz
```

Y posteriormente lo extraje con:

```bash
tar --extract --file web-vault.tar.gz --directory vaultwarden/vaultwarden-data
```

(Pueden eliminar el archivador luego con `rm web-vault.tar.gz`).

## Paso 3: Ejecución de los contenedores y automatización

Si todo va bien, ya sería momento de levantar los contenedores y ver si funciona como corresponde. Para esto, basta con ingresar al directorio donde se encuentra el `docker-compose.yml` y ejecutar:

`docker-compose up -d`

Se asume un conocimiento básico con `docker`, para poder revisar los logs y efectuar mantenimiento llegado al caso que sea necesario.

Para detenerlos es también bastante intuitivo:

`docker-compose down`

Llegados hasta acá, debemos tener los contenedores funcionando como corresponde. Si ingresamos a `<DOMINIO>` deberíamos poder ver el formulario de login a nuestro web vault. En `<DOMINIO>/admin` tendremos acceso al panel de administración (la contraseña la definimos en el `docker-compose.yml` anteriormente). Si nos logueamos, podemos ver una serie de menúes desplegables que nos ofrecen un montón de variables disponibles para configurar nuestra instancia. En *General settings* deshabilité la opción de nuevos signups pero dejando activa (al menos por ahora) la posibilidad de invitar nuevos usuarios. Son más que nada cuestiones de seguridad. El otro apartado interesante es *SMTP Email Settings*, donde podremos setear las opciones tendientes al envío de correos electrónicos (por ejemplo, con el link de invitación). En mi caso probé las dos opciones que cita la Wiki del proyecto, [SendGrid](https://sendgrid.com/) y [MailJet](https://www.mailjet.com/), pero para no complicarme la vida y dado que lo voy a usar sólo en mi entorno familiar, me decanté por usar mi cuenta de [Gmail](https://accounts.google.com/). 

En caso de que quieran implementar el servicio SMTP con Google, los campos deben llenar del siguiente modo:

| Campo              | Valor            | Descripción                                                  |
| ------------------ | ---------------- | ------------------------------------------------------------ |
| Enabled            | `true`           | Habilita o deshabilita la opción de envío de correos electrónicos |
| Host               | `smtp.gmail.com` | Dirección del host                                           |
| Enable Secure SMTP | `true`           | Habilita (o no) SMTP seguro                                  |
| Force TLS          | `false `         | Fuerza TLS (Transport Layer Security)                        |
| Port               | `587`            | Puerto a ser usado por el servidor SMTP                      |
| From Address       | `<MAIL>`         | La dirección de correo desde la cuál se van a enviar los correos electrónicos |
| From Name          | `<NAME>`         | Nombre que aparecerá junto al asunto del mensaje             |
| Username           | `<MAIL>`         | En el caso de Gmail, el username es también el correo electrónico |
| Password           | `<PASS>`         | **NO** es tu contraseña del correo electrónico que usas habitualmente sino que debes generar una específicamente para usar con apps de terceros: [clickeame](https://myaccount.google.com/apppasswords) |

Las opciones que quedan debajo las dejé por defecto ya que no son importantes para este caso puntual. Mi consejo es que revisen todas las opciones restantes y lean la documentación para ver si les sirve alguna más.

¿Qué pasa si de repente se nos corta la luz? Al regresar puede que nuestra Raspberry Pi se encienda nuevamente pero probablemente nuestros contenedores no. Con lo cual debemos encontrar una manera de automatizar el proceso. Hay quienes dirán de usar Kubernetes pero en mi caso me gusta hacer uso de las opciones nativas, así que implementé las automatizaciones, tanto de los contenedores como de los respaldos respectivos, con servicios y timers de systemd.

Entonces creamos el servicio:

```bash
nvim /etc/systemd/system/vaultwarden.service
```

E introducimos allí lo siguiente:

```toml
[Unit]
Description=Vaultwarden
After=docker.service network.target
Requires=docker.service network-online.target

[Service]
RemainAfterExit=true
WorkingDirectory=/mnt/disco/vaultwarden
ExecStartPre=/usr/bin/docker-compose pull --quiet
ExecStart=/usr/bin/docker-compose up -d

ExecReload=/usr/bin/docker-compose pull --quiet
ExecReload=/usrbin/docker-compose up -d

ExecStop=/usr/bin/docker-compose down

Restart=always
RestartSec=30s

[Install]
WantedBy=multi-user.target
```

Por último, guardamos, salimos y debemos habilitar el servicio:

```bash
systemctl enable vaultwarden.service
```

Esto hará que ante un reinicio de nuestra Raspberry Pi, luego de que se inicie correctamente el servicio de Docker y nuestra conexión de red se levantarían los contenedores que figuran en nuestro Docker Compose. Podríamos ejecutar el servicio instantáneamente reemplazando en el comando anterior el `enable` por `start`, pero no es necesario ya que los levantamos manualmente con `docker-compose`,

## Paso 4: Reforzar nuestro Vault

Nuevamente cito el repositorio oficial dado que tiene una sección exclusiva con estrategias para hacer nuestro Vault mucho más seguro y menos vulnerable a ataques (tengan en cuenta que se van a estar almacenando contraseñas dentro). En mi caso le añadí `fail2ban` para que ante tres intentos fallidos de ingreso, tanto en el panel de administración como en el web vault, se bloquée la IP en cuestión. Más adelante probablemente intente nuevas estrategias en conjunto para evitar malas noticias.

Existe la manera de implementar `fail2ban` usando un contenedor con Docker pero yo lo instalé desde los repositorios de Arch Linux ARM. Luego cree los siguientes cuatro archivos:

* Creamos el archivo con las expresiones regulares que filtrarán nuestro log en búsca de intentos fallidos de login en nuestro Web Vault:

```bash
nvim /etc/fail2ban/filter.d/vaultwarden.local
```

```toml
[INCLUDES]
before = common.conf

[Definition]
failregex = ^.*Username or password is incorrect\. Try again\. IP: <ADDR>\. Username:.*$
ignoreregex =
```

* Creamos el archivo de configuración de nuestro Web Vault:

```bash
nvim /etc/fail2ban/jail.d/vaultwarden.local
```

```toml
[vaultwarden]
enabled = true
port = 80,443,8081
filter = vaultwarden
action = iptables-allports[name=vaultwarden, chain=FORWARD]
logpath = /mnt/disco/vaultwarden/vaultwarden-data/logs/access.log
maxretry = 3
bantime = 14400
findtime = 14400
```
* Creamos el archivo con las expresiones regulares que filtrarán nuestro log en búsca de intentos fallidos de login en nuestro panel de administración:

```bash
nvim /etc/fail2ban/filter.d/vaultwarden-admin.local
```

```toml
[INCLUDES]
before = common.conf

[Definition]
failregex = ^.*Invalid admin token\. IP: <ADDR>.*$
ignoreregex =
```

* Creamos el archivo de configuración de nuestro panel de administración:

```bash
nvim /etc/fail2ban/jail.d/vaultwarden-admin.local
```

```toml
[vaultwarden-admin]
enabled = true
port = 80,443
filter = vaultwarden-admin
action = iptables-allports[name=vaultwarden, chain=FORWARD]
logpath = /mnt/disco/vaultwarden/vaultwarden-data/logs/access.log
maxretry = 3
bantime = 14400
findtime = 14400
```

Ahora sí, podemos arrancar el servicio:

```bash
systemctl start fail2ban.service
```

Y lógicamente también lo habilitamos:

```bash
systemctl enable fail2ban.service
```

Con eso tendrían funcionando correctamente el servicio. Una manera de probarlo es ingresando una contraseña incorrecta tres veces seguidas y ejecutando `fail2ban-client banned` para verificar que la IP desde donde hacemos el chequeo haya sido baneada. En caso de ser así, !felicidades, te has baneado! Eso significa que funciona como debe. Ya podés quitarte el ban ejecutando `fail2ban-client set vaultwarden unbanip <IP>`.

## Paso 5: Backups

Los respaldos suelen tener una sección dedicada dada su relevancia indiscutible. Mi hipótesis es que quienes nos preocupamos por el respaldo de los datos somos quienes en algún momento, y discúlpenme la expresión, la *re cagamos*. Sea por el motivo que sea, la documentación nos muestra explícitamente qué archivos debemos respaldar y cuáles sólo ocupan espacio.

Voy a ser completamente sincero: el script que armé para hacer backups no se destaca precisamente por su prolijidad, y voy a compartir una versión editada dado que tiene información sensible (que posiblemente no debería estar, pero como funcionó a la primera, así lo dejé). En fín, paso a lo relevante.

En primer lugar creo el script `backup.sh` en `/mnt/disco`, con el siguiente contenido:

```bash
#!/usr/bin/env sh

# Variables
MAIL="<MAIL>"
RCLONE_PATH="<REMOTE>:<PATH>"

# Creo un directorio para copiar los archivos y comprimirlos
echo "Creando backup..."
mkdir rclone-backups/tmp
sqlite3 vaultwarden/vaultwarden-data/db.sqlite3 ".backup 'rclone-backups/tmp/db-$(date '+%Y%m%d').sqlite3'"
rsync -a vaultwarden/vaultwarden-data/{config.json,rsa_key*} rclone-backups/tmp
[ -d vaultwarden/vaultwarden-data/attachments ] && rsync -a vaultwarden/vaultwarden-data/attachments rclone-backups/tmp
[ -f vaultwarden/vaultwarden-data/db.sqlite3-wal ] && rsync -a vaultwarden/vaultwarden-data/db.sqlite3-wal rclone-backups/tmp
tar --create --gzip --verbose --remove-files --file rclone-backups/vault-$(date '+%Y%m%d').tar.gz rclone-backups/tmp

# Copio el archivo más nuevo a la nube (Mega.nz)
echo "Copiando archivos a la nube..."
newest=$(ls --sort=time --format=single-column -r rclone-backups | tail -n1)
rclone copy rclone-backups/"${newest}" "${RCLONE_PATH}"

# Elimino backups viejos (mayores a 30 días)
echo "Buscando y eliminando backups antiguos..."
find rclone-backups -type f -mtime +30 -name 'vault-*' -delete

# Si llego a 25Gb, me mando un mail así meto mano...
storage=$(rclone size "${RCLONE_PATH}" --json | jq '.bytes')
if [ "${storage}" -ge 25000000000 ]; then
	echo "La nube pasó la mitad de su capacidad." | mail -v -s "Alerta!" "${MAIL}"
fi
```

Hay algunas cosas que notar en el script. En primer lugar, debemos crear la carpeta `rclone-backups` para que el script pueda crear los backups. En segundo lugar, se requieren algunos paquetes para que funcione correctamente: `s-nail` que nos provée el comando `mail`, `rclone` que nos enviará los archivos `.tar.gz` con nuestro respaldo a la nube que escojamos (en mi caso Mega.nz), `rsync` que decidí usar en reemplazo de `cp` porque me resulta más óptimo en casos puntuales, y `jq` que sirve para parsear el JSON que nos devuelve `rclone` al querer obtener el espacio de almacenamiento ocupado dentro de la nube. Comandos como `sqlite3` o `tar` se asumen ya instalados (como en la gran mayoría de distribuciones Linux). Claramente se puede pulir mucho más, o incluso automatizar la limpieza de los backups que almaceno en la nube, pero me temo que será trabajo para más adelante. Mi consejo es que se aseguren de tener algo que saben que funciona. Lo idóneo sería, pasado un tiempo, respaldar manualmente la configuración de Vaultwarden y simular la restauración del último backup, para asegurarnos que están bien hechos y no están dañados. Un backup mal hecho es prácticamente como no tenerlo.

Posteriormente lo marco como ejecutable:

`chmod +x backup.sh`

Y continúo con la creación del servicio que ejecutará el script, y el timer que actúa similar al archi conocido `cron`.

* Creamos el servicio:

```bash
nvim /etc/systemd/system/vaultwarden-backup.service
```

```bash
[Unit]
Description=Backup Vaultwarden database and files

[Service]
Type=oneshot
WorkingDirectory=/mnt/disco
ExecStart=/usr/bin/env sh backup.sh
```

* Creamos el timer:

```bash
/etc/systemd/system/vaultwarden-backup.timer
```

```bash
[Unit]
Description=Schedule Vaultwarden backups

[Timer]
OnCalendar=04:00
Persistent=true

[Install]
WantedBy=multi-user.target
```

Creo que por más que nunca hayan hecho algo similar, es bastante comprensible: todos los días a las 4 de la mañana se activa el timer que ejecuta el servicio que se encargará de ejecutar el script (sí, un trabalenguas). La opción `Persistent=true` me gustaría remarcarla como importante porque es la encargada de que si, por alguna razón, el timer no puede ejecutarse a la hora prevista (por ejemplo, si la RPi está apagada), lo hará ni bien pueda, sea la hora que sea. Eso evita que por un desperfecto el respaldo se saltée un día entero.

Finalmente habilitamos el timer:

```bash
systemctl enable vaultwarden-backup.timer
```

Y lo levantamos para que ya comience a funcionar:

```bash
systemctl start vaultwarden-backup.timer
```

El servicio en este caso se deja intacto porque es el timer quien se encargará de levantarlo cuando sea el momento de efectuar el respaldo.

## Conclusión

Para concluir me gustaría destacar nuevamente el repositorio original: https://github.com/dani-garcia/vaultwarden. Y es que este post es sólo una pequeñísima parte de las opciones que ofrece Vaultwarden. Es la utilidad que yo le dí, y que satisface mis necesidades. Sin lugar a dudas lo seguiré usando por bastante tiempo y probablemente hasta me anime a añadir algunos cambios, quizás en el apartado de seguridad o incluso mejorando el script de generación de backups. Tampoco dudo que es un buen proyecto para poner en marcha tu Raspberry Pi, si así lo quisieras. Desde mi humilde lugar, insto al lector a sumergirse en el repositorio en cuestión y aventurarse a levantar su propia instancia de Vaultwarden. Porque, además de estar implementando un software súper útil y recomendable, se afianzan conocimientos y se incorporan otros al querer implementar una funcionalidad nueva. En fin, sin lugar a dudas un proyecto que merece la pena llevar a cabo.
