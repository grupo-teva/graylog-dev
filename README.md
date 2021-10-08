# graylog-dev-template
Plantilla para desplegar instancias de [Graylog](https://www.graylog.org/) totalmente dockerificado a su manera, esto es, usando los siguientes contenedores:
- [MongoDB](https://www.mongodb.com/)
- [ElasticSearch](https://www.elastic.co/)
- __Graylog__ _(as himself)_
- [Nginx](https://www.nginx.com/), como _proxy inverso_, si fuera necesario.

Ojo cuidao, que la gente de Graylog no quiere que usemos esto en entornos productivos, y, probablemente, tengan razón.

## Variables de entorno a considerar en el contenedoer de _Graylog_
Se encuentran en el fichero `.env.dist`, y es necesario hacer dos cosas dos:
* Crear a partir de él un fichero `.env`, y 
* Editarlo y establecer las variables convenientemente.

### _GRAYLOG_PASSWORD_SECRET_ y _GRAYLOG_ROOT_PASSWORD_SHA2_
El primero es la clave del usuario _admin_, y el segundo un _hash_ de dicha clave. Para crear este _hash_ de la clave que vayamos a poner, podemos usar estos comandos:

<pre>
❯ echo -n 'los-impuestos-son-un-robo' | sha256sum | awk '{ print $1 }'
9526855fe0040d7ba98971039accf0c68349f5932653e2cba94ee6c7f27a93fe
</pre>

### _GRAYLOG_HTTP_BIND_ADDRESS_, _GRAYLOG_HTTP_EXTERNAL_URI_ y _GRAYLOG_HTTP_PUBLISH_URI_
Las configuraciones por defecto funcionan en local, por lo que deben ser adecuadas al servidor donde se realiza el despliegue. En el siguiente enlace se detallan sus características → https://docs.graylog.org/en/4.0/pages/configuration/server.conf.html#web-rest-api-options


### Otras variables de entorno

En `./graylog/config` se encuentra un fichero `graylog.conf` completo; todas sus variables pueden importarse fácil e indoloramente en el apartado _environment_ del fichero `docker-compose.yml`, anteponiendo el prefijo «GRAYLOG_» al nombre de la variable del fichero, todo en mayúsculas, y asociándola con el valor deseado.

Por ejemplo, la hora del nodo está definida por defecto a **UTC**; en una instalación normal editaríamos la variable `root_timezone` del fichero `graylog.conf`, pero nosotros lo cambiaremos así:
<pre>
GRAYLOG_ROOT_TIMEZONE: "Europe/Madrid"
</pre>

## Cómo utilizar esta plantilla
1. Clonar el repositorio
2. Adecuarlo a los requerimientos del nuevo despliegue.
3. Desplegar el entorno, a través del _makefile_.

## Uso detallado del *Makefile*
#### _make help_ o _make_
Muestra, sucintamente, toda las funcionalidades:

<pre>
❯ make
usage: make [target]

targets:
help              Show this help message
run               Start the containers
stop              Stop the containers
terminate         Stop the containers (with --remove-orphans option set)
restart           Restart the containers
build             Rebuilds the custom Nginx container
ssh               SSH into the graylog container
ssh-root          SSH into the graylog container as root
logs-mongodb      Tails the MongoDB container log
logs-elastic      Tails the Elastic container log
logs-graylog      Tails the Graylog cointainer log

</pre> 

#### _make run_
Crea, si no existe, una red de _Docker_ compartida por todos los contenedores, y, posteriormente, arranca los contenedores en el orden establecido.

#### _make stop_
Detiene los contenedores.

#### _make terminate_
Detiene los contenedores y los elimina posteriormente.

#### _make restart_
Detiene y vuelve a arrancar los contenedores.

#### _make build_
(Re)Construye el contenedor de Nginx, si se precisa, pues el resto de contenedores vienen directos de sendas imágenes.

#### _make ssh_
Abre una sesión _SSH_ en el contenedor del _Graylog_.

#### _make ssh-root_
Abre una sesión _SSH_ en el contenedor del _Graylog_ con el usuario _root_.

#### _make logs-mongodb_
Muestra en modo de seguimiento los _logs_ del contenedor de _MongoDB_.

#### _make logs-elastic_
Muestra en modo de seguimiento los _logs_ del contenedor de _MongoDB_.

#### _make logs-graylog_
Muestra en modo de seguimiento los _logs_ del contenedor de _MongoDB_.
