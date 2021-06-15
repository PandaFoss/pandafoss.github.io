---
layout: single
class: wide
title: Seguridad informática para simples mortales
last_modified_at: 2020-11-07
header:
  teaser: ../assets/posts/hacker.jpg
tags:
  - seguridad
  - guia
  - privacidad
---

*Edición del 7 nov 2020: En vista de que mucha gente, por uno u otro motivo, sigue siendo víctima de "hackeos" (fundamentalmente en redes sociales), he creado el siguiente listado con recursos útiles para las víctimas (por lo menos para saber a quién recurrir en primer término, antes de una  consulta "formal" o "legal" si es el caso). Está abierta a colaboración: [Link](https://gist.github.com/PandaFoss/76ddc9c9efcc764cf2ca6d10bc06bbc3)*

## Breve introducción

Un video en una popular red social despertó mi interés por escribir sobre seguridad informática. En él una chica relataba, sollozando, un par de situaciones de (in)seguridad informática que tuvo que vivir, las consecuencias del robo de datos y otros detalles no menores. Por otro lado, varios medios de comunicación publicaron al respecto haciendo alusión al **aumento en la cantidad de ciberataques durante la cuarentena**, tanto en mi país (Argentina) como en muchos otros, latinoamericanos y del mundo.

Por eso, si llegaste hasta acá, te aconsejo que sigas leyendo. Prometo ser lo más conciso y claro posible.

*Nota: El objetivo es dar reglas básicas y herramientas casi elementales de protección para personas que no se relacionan con el rubro de la tecnología o de la informática. Si entraste para leer un tutorial de Kali Linux, es el lugar incorrecto.*

___

## Reglas para navegar por la web frente a un mundo hostil y despiadado

Si bien mi rubro es la informática, no soy experto en seguridad. Ésto lo aclaro porque las reglas que voy a presentar tienen en parte algo de subjetividad, ya que son normas que con el tiempo fui moldeando para uso personal. Ahora deseo compartirlas. Y en forma de ítems, porque a todos nos gustan los ítems. Allá vamos...

* **Desconfía de todo y de todos**. Sí, puede sonar un poco extremista, pero así es. La realidad es que uno muy pocas veces sabe a ciencia cierta quién está **realmente** del otro lado de la pantalla. Desconfía de los correos electrónicos de redes sociales o de plataformas bancarias, de ofertas de trabajo que impliquen fotografías o videos de uno mismo (más aún si dicho material es en "paños menores" (con poca ropa)). Ten presente que basta sólo un par de clicks para que un tercero nos complique la vida. (No se asusten, les daré algunos tips sobre el final).

* **Somos el eslabón más débil de la cadena**. Un cibercriminal suele enfocarse en atacar el eslabón más debil de la cadena. ¿Pero qué pasa cuando el eslabón más débil somos nosotros, los usuarios? He escuchado multitud de veces cosas como "me hackearon la cuenta de Instagram", o "de Facebook". Y por dentro pienso lo dificil que puede ser *romper* la seguridad de empresas como la mencionada. ¿No habrá sido un error humano (del usuario mismo)? La respuesta, sin saberla, es sí. Y es que con un ataque tan histórico como actual como lo es el phishing, basta un correo electrónico y un usuario confiado para que, ese usuario, pierda la cuenta (nuevamente, ¿ven por qué hay que ser un poco desconfiado?). La buena noticia es que, con los consejos que les voy a dar a continuación, podremos ser un eslabón un poco más "durito".

* **Ten amor por la privacidad**. Infinidad de veces he escuchado que desprestigian la protección de nuestra privacidad con el argumento "no tengo nada que ocultar". Y es aquí cuando cito una frase que se le atribuye a Eduard Snowden:
> *"Decir que no te preocupa la privacidad porque no tienes nada que ocultar es como decir que no te preocupa la libertad de expresión porque no tienes nada que decir." ~ E. Snowden*

Creo que cualquier cosa que añada está de más. Pero es importante remarcar lo siguiente: **lo que subimos a internet, deja de ser privado y de estar bajo nuestro control**. ¿Eso implica que no debería subir fotos sin remera a Instagram?. Claro que no, yo no soy quién para decir qué deben y que no deben subir a las redes. Simplemente les informo lo que usualmente pasa (y que gran parte de las veces queda constancia en los llamados *Términos y Condiciones* que nadie nunca lee).

* **Nadie nace sabiendo**. Por último pero no por eso menos importante, es aclarar que nadie nace sabiendo. Sí, es algo lógico, pero no tanto. La idea de estos ítems es que se despierte de tu interior un mínimo interés la seguridad informática personal, por comprender la importancia de la pivacidad y de estar siempre atentos, de cuestionar y sospechar cualquier situación que nos genere cierta desconfiaza o que se aleje de lo lógico. No hace falta llegar a la paranoia absoluta; basta con ser menos confiados.

Llegados a este punto seguramente te estarás preguntando, ¿hay algo que pueda hacer para prevenirme? Y si no te lo estás preguntando, preguntátelo porque es lo próximo que voy a responder.

___

## Regla útil de protección

Popularmente se suele relacionar la seguridad con una contraseña de esas imposibles de recordar. Pero (sí, siempre hay un "pero") no es suficiente. Podemos pensar en el mítico caso en el que alguien nos apunta con el dedo y nos dice "a tí te hackearé", y de tantas veces que lo intenta lo termina logrando. O también podemos pensar en algo más cercano a la realidad como puede ser la filtración de una base de datos en donde realmente no importa si tu password es *hola1234* o *2wZywSF3DLxac7*.

Desde ya que la robustez de la contraseña es muy importante, pero deberíamos aprender también el concepto de *segundo factor de autenticación* (o Two-Factor Authentication o 2FA, en inglés, como suele utilizarse). Si bien es bastante intuitivo, el segundo factor de autenticación es un paso extra que solemos tener al ingresar a una plataforma web (por ejemplo en las redes sociales o en los servicios de mensajería como Telegram o Whatsapp). Desde ya, la **tenemos que habilitar** nosotros mismos. 

Pero, ¿por qué es importante? Dicen los que saben que para que un sistema sea realmente seguro, debe estar compuesto por **algo que sabes, algo que tienes y algo que eres**. Esto hace referencia precisamente al segundo (o incluso tercer) factor de autenticación. Leamos nuevamente. "Algo que sabemos" puede ser, por ejemplo, la contraseña. "Algo que tenemos" se refiere al teléfono móvil, por ejemplo. Y con "algo que somos" apunta a la autenticación biométrica, es decir, la forma de la mano o las huellas dactilares.

En general, para nosotros, los simples mortales, pasar de tener como contraseña *hola1234* a tener una contraseña robusta y un segundo factor de autenticación es todo un logro. No por eso hay que bajar la guardia, pero sí les podría asegurar mayor seguridad (si tienen el debido cuidado, desde ya). Así que, para finalizar, voy a mencionar algunas herramientas útiles que uso o he usado (o me han recomendado) para que puedan aplicar todo lo que vienen leyendo hasta este momento sin dolores de cabeza. Incluiré también algún que otro ejemplo para habilitar el 2FA en algunas plataformas conocidas.

## Herramientas útiles

* **Generar y almacenar contraseñas**. 

No es cosa de unos pocos el hecho de recordar un par de contraseñas y usarlas en todas y cada una de las plataformas o sitios donde nos registramos. Pero, como es de esperarse, no es la opción más recomendada.  Sin embargo, también es cierto que recordar un sinfín de contraseñas complejas y diferentes entre sí no es tarea simple. Por suerte existen los gestores de contraseñas. Éstas aplicaciones deberían convertirse en tu "mano derecha". ¿Qué es y para qué sirven esos gestores de contraseñas? Son, ni más ni menos, aplicaciones o servicios que nos permiten almacenar y administrar de forma segura nuestras contraseñas. En algunos casos suelen tener funciones extra, como puede ser un generador de contraseñas (muy útil y recomendado) u opciones de autocompletado de formularios (es decir, cuando detecta que hay un casillero para iniciar sesión, ingresa la contraseña por nosotros, así de simple).

Algunas opciones:

– **Firefox Lockwise** *([https://lockwise.firefox.com/](https://lockwise.firefox.com/))*: Si sos usuario del navegador Mozilla Firefox, quizás ésta sea una buena opción, ya que *viene integrada con él*. El gestor de contraseñas de Firefox cuenta con aplicación propia para Android y iOS. Para que las contraseñas se sincronicen a través de los diferentes dispositivos, deben crearse una cuenta e [iniciar sesión en Firefox](https://accounts.firefox.com/). Las principales ventajas son la integración con el propio navegador Firefox (por ejemplo, te permite recordar las contraseñas facilmente cuando las ingresas o autocompleta los casilleros de *password* cuando los detecta), la sincronización entre dispositivos (si tenés Firefox en dos computadoras diferentes y le decís al navegador que recuerde una contraseña, automáticamente la otra computadora también tendrá acceso a dicha contraseña) y la generación de contraseñas robustas (cuando "ve" que nos queremos registrar en algún sitio, nos ofrece generar una contraseña "segura").

_**Nota:** Usuarios de Chrome tienen su propio gestor de contraseñas ([https://passwords.google.com](https://passwords.google.com)) con funciones similares (excepto, por ejemplo, la app). Sin embargo, no es un navegador que, en lo personal, recomiende._

– **Bitwarden** *([https://bitwarden.com](https://bitwarden.com))*: Éste es otro gestor que recomiendo por varias razones. En primer lugar porque se puede instalar en un montón de plataformas, desde el celular, hasta la PC (incluso tiene plugins para el navegador). Como opciones "extra" tiene unas cuantas: todas las que mencioné anteriormente y alguna que otra más (que no viene al caso detallar).

– **Otros gestores**: Hay multitud de otros gestores de contraseñas dando vueltas por allí. Podría recomendar también **KeePass** *([https://keepass.info](https://keepass.info/))* para Windows que, al ser de código abierto (ya explicaré esto) permite que surjan otros proyectos relacionados para otras plataformas, como **KeePassXC** *([https://keepassxc.org](https://keepassxc.org))* para Windows, Linux y macOS o **KeePassDX** *([https://www.keepassdx.com](https://www.keepassdx.com/))* para Android. Todos ellos son proyectos independientes pero compatibles entre sí.

**Importante**: En cualquier caso hay que tener en cuenta que dichos gestores suelen usar una "clave maestra" para desbloquearse, y perder u olvidar dicha clave implica no tener acceso al depósito de claves. Sí, pierden todas las contraseñas (a menos en los casos que no hay sincronización y/o algún sistema de recuperación). Otro punto de suma importancia es que algunas aplicaciones almacenan las claves en un fichero (archivo) en nuestro dispositivo (celular, computadora, etc), por lo que es de vital importancia realizar un respaldo cada tanto (hacer una copia y guardarla en un lugar seguro); perder dicho archivo implica, también, perder las contraseñas que guardamos.

* **Segundo factor de autenticación**.

Seguramente la gran mayoría de ustedes tengan cuentas en redes sociales (como Instagram, Twitter, Facebook, etc), servicios de mensajería instantánea (como Whatsapp o Telegram), y otras plataformas en la cual manejan información personal o, incluso, dinero. En todos esos casos existe la posibilidad de habilitar el *segundo factor de autenticación*, el cual será diferente en cada uno de los nombrados. Es más, algunas plataformas como Google nos ofrecen diversas opciones a la hora de brindarle mayor seguridad a la cuenta: puede ser desde un SMS con un código de acceso, códigos de seguridad implimibles e incluso usando la aplicación Google Authenticator. Y acá hay algo importante: las aplicaciones. Si bien Google Authenticator es una de ellas, la realidad es que, como ocurría con los gestores de contraseñas, existen unos cuantos disponibles para instalar.

Pero primero lo primero, ¿en qué consisten éstas aplicaciones? En pocas palabras, nos brindan pines numéricos que se renuevan cada cierto tiempo (por ejemplo, 30 segundos). Entonces, supongamos que configuramos Instagram (por poner un ejemplo). Nos dirigimos a nuestro *feed* y vemos en la esquina superior derecha las tres líneas horizontales (conocidas como *hamburguesa* entre los diseñadores). Se despliega un menú en cuyo margen inferior aparece *Configuración*. Ingresamos allí, luego en el apartado *Seguridad* y, finalmente, en *Autenticación en dos pasos* (que, por cierto, figura como *recomendado*). Si activamos dicha opción, Instagram buscará una app para configurar por nosotros el segundo factor bendito. Si no la encuentra, nos muestra un código relativamente largo. Ese código es el que usaremos en nuestra aplicación (o *app*) de autenticación en dos pasos (que aún no instalamos). *(Es importante anotar los códigos que Instagram (o la aplicacion o plataforma que sea) nos proporciona para ingresar a la cuenta en caso de no tener acceso a nuestro teléfono.)*

Una vez configurado como corresponde ya no bastará ingresar con usuario, mail o número de teléfono y contraseña, sino que les solicitará el pin generado por nuestra aplicación autenticadora.

Queda responder una pregunta, ¿qué aplicación podemos usar?

– Algunas aplicaciones que mencionamos anteriormente en la sección de gestores de contraseñas **también admiten el algoritmo TOTP** (que es el sistema éste que les cuento en el que la aplicacion nos brinda un pin que se renueva cada cierto período de tiempo). Ejemplos de ellos son **Bitwarden** y **KeePassDX** (o **KeeWeb** para navegadores web).

– Existen también aplicaciones cuyo único propósito es servir de 2FA, por ejemplo, **Aegis** *([https://getaegis.app](https://getaegis.app/))* y **Authy** *([https://authy.com](https://authy.com))*. El primero es software libre y de código abierto, pero no tiene ningún tipo de sincronización y es compatible sólo con Android. El segundo, en cambio, tiene sincronización en "la nube" (*la nube* vendrían a ser en este caso servidores de la empresa que mantiene la app, o sea, Twilio) y está disponible para varias plataformas además de Android.

En definitiva, depende de los gustos e intereses de cada cual, y de las necesidades que quiera o no cubrir.

_**Nota**: Actualmente la autenticación biométrica se está viendo cada vez más seguido en teléfonos móviles que poseen acceso a través de la huella dactilar. No hago un apartado al respecto porque es un nivel de seguridad que no todo el mundo tiene al alcance, pero si tienen la opción, también pueden considerarlo._

___

## Consideraciones finales

No es el propósito de éste artículo decirles *"deben usar tal o cual aplicación"* porque no soy quién para hacerlo. Simplemente pretendo que tengan en consideración la seguridad y la privacidad en la red y que sepan de la existencia de éstas aplicaciones (y las usen si consideran apropiado).

Sé que son muchas las aplicaciones disponibles (yo nombre un puñado muy pequeño), pero además de cuestiones de gustos personales también pueden tener en cuenta lo siguiente:

– **Es beneficioso si la aplicación dice ser de código abierto**. Esto implica que **el código está a la vista de todos**, con lo cual cualquier persona con los conocimientos en el tema puede auditar que la aplicación no tenga *cosas* extrañas (más aún si se trata de aplicaciones que velan por la seguridad). En ocasiones nos brindan, posiblemente junto a otras plataformas y redes sociales, un enlace a GitHub o GitLab (las cuales son plataformas donde los desarrolladores publican el código fuente de sus programas y aplicaciones para que otros desarrolladores puedan contribuir también).

– **Punto a favor para organizaciones que bregan por la privacidad**. Ejemplo de ésto es Mozilla. No está mal usar servicios como los provistos por Google para almacenar nuestras contraseñas e información personal en general, pero debemos ser conscientes de lo que ello conlleva. En lo personal, recomiendo evitarlos.

– **Evita el registro a mansalva**. Muchas veces optamos por registrarnos en sitios que no sabemos si volveremos a usar en algún momento. En éstos casos hay dos opciones: podemos usar el inicio de sesión a través de otra plataforma o red social (si existe la posibilidad), o registrarnos con un correo temporal (hay sitios para ello). En el caso de que no volvamos a usar ese sitio nunca más y quede en el olvido, no debemos preocuparnos porque un tercero no autorizado pueda, de algún modo, obtener nuestros datos y vincular la contraseña con nuestro usuario o correo electrónico (más aún si usamos la misma contraseña en más de un sitio, cosa que no recomiendo pero que probablemente seguirán haciendo (y los entiendo)).

– **Identifica servicios esenciales**. Hay servicios que están conformados por un conjunto de ellos. Es el caso de la cuenta de Google (sobre todo en usuarios de Android) o de Apple (con servicios cono iCloud, para usuarios de dicha marca). Éstos servicios deben tenerse con mayor cuidado ya que la vulneración de los mismos implicaría que el agresor tenga acceso a, bueno, toda nuestra vida. Entonces, debería ser el primer punto a considerar.

Y eso es todo (por ahora). Si te gustó, compartelo. Si no te gustó, dime qué le cambiarías. Si tienes dudas sobre uno o más puntos, puedes escribirme en Telegram (`@PandaFoss`). En cualquier caso, espero que te haya servido tanto como a mi redactarlo. ¡Nos vemos en el próximo post! (Se aceptan sugerencias de temáticas a tratar).

___

_**Descargo de responsabilidad**: La imágen de portada fue extraída de Unsplash. Créditos al autor.
No tengo ningún vínculo con ninguna de las aplicaciones y servicios mencionados en el artículo. El mismo es pura y exclusívamente con propósito informativo/educativo._
