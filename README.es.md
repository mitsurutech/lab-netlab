# Laboratorios de redes en una sola VM: enrutamiento estático, OSPF y BGP con FRRouting

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md)

[![check](https://github.com/mitsurutech/lab-netlab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-netlab/actions/workflows/check.yml)

Tres **laboratorios de redes informáticas** listos para usar en clase, de la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning), donde escuelas y centros dan a cada alumno sus propias máquinas y su terminal en el navegador. Cada alumno recibe su propia VM Ubuntu con una pequeña red construida dentro, con **routers que ejecutan FRRouting** y hosts desde los que hacer ping, y aprende **enrutamiento estático, OSPF y BGP** en la misma consola `vtysh` que se encontrará en equipos reales. No se instala nada en el ordenador del alumno y no hace falta ninguna licencia de Cisco.

Un router necesita una máquina propia y un alumno tiene una sola VM, así que cada router y cada host es un **espacio de nombres de red** (network namespace), cada enlace un par de cables virtuales, y cada router ejecuta sus propios demonios de FRRouting. Los comandos reales funcionan, que es lo importante: `ip route`, `traceroute -n`, `tcpdump`, `vtysh`.

## Los tres laboratorios

| Laboratorio | Archivo | Qué hace el alumno | Página de ayuda |
| --- | --- | --- | --- |
| Enrutamiento estático | [`static-routing.lab.yaml`](static-routing.lab.yaml) | Encontrar dónde se detiene un paquete, añadir rutas estáticas en los routers que las necesitan, mantener inalcanzable una red de gestión | [Laboratorio de enrutamiento estático](https://mitsurutech.co.uk/es/help/lab-netlab-static) |
| OSPF | [`ospf.lab.yaml`](ospf.lab.yaml) | Activar OSPF en el área 0 en tres routers, observar los hello, esperar a que cada adyacencia esté en Full, dejar una red fuera de los anuncios | [Laboratorio de OSPF](https://mitsurutech.co.uk/es/help/lab-netlab-ospf) |
| BGP | [`bgp.lab.yaml`](bgp.lab.yaml) | Hacer vecinos a dos sistemas autónomos, anunciar la red de cada uno y no anunciar nada de la red privada que hay detrás de uno de ellos | [Laboratorio de BGP](https://mitsurutech.co.uk/es/help/lab-netlab-bgp) |

Cada laboratorio deja en la carpeta personal del alumno un README que dice qué hacer y no da ninguna de las respuestas. Cada uno tiene una **comprobación**, un script que califica una VM entregada sin que el profesor la lea: se ejecuta sobre una copia de la máquina guardada después de un inicio limpio, así que solo ve lo que el alumno guardó con `write memory`, y solo pasa si el estado real se cumple (las rutas están en las tablas de enrutamiento, los vecinos OSPF están en Full o la sesión BGP está en Established, `h1` llega a `h2` por el camino a través de los routers, y la red privada sigue siendo inalcanzable).

## Qué hay en un archivo

Cada `.lab.yaml` es una **definición de laboratorio de VM** completa, exactamente lo que ejecuta la plataforma:

| Clave | Qué es |
| --- | --- |
| `cpus`, `memory`, `disk` | el tamaño de la VM de cada alumno: 2 CPU, 2048 MB y 10 GB |
| `open` | los puertos que se abren en el navegador: ninguno, el laboratorio es el terminal |
| `setup` | un script que se ejecuta una vez, como root, la primera vez que arranca la VM de cada alumno: instala FRRouting y `tcpdump` desde el archivo de Ubuntu en unos diez segundos y escribe el comando `netlab`, la topología y el README del laboratorio |
| `check` | el script de calificación, que se ejecuta como root sobre una copia del disco guardado después de un inicio limpio; su código de salida es el resultado y `0` es aprobado |

El comando `netlab` que instala: `netlab status` lista las máquinas, `netlab shell r1` abre la consola de un router y `netlab shell h1` la de un host, y `netlab reset` empieza el laboratorio de nuevo.

## Usarlo en la plataforma

**Para ponerlo como trabajo a una clase:**

1. En tu módulo, añade una tarea y elige la tarjeta **Laboratorio de VM Ubuntu**.
2. Pulsa el ejemplo del que quieras partir, o pega el contenido de uno de los archivos de aquí. Usar un ejemplo reemplaza el texto, y Deshacer recupera el tuyo.
3. Publica y prueba primero tu propia copia. Cada alumno tiene una VM propia, que se conserva entre sesiones.
4. Asígnalo como cualquier tarea. Cuando se entregue, ejecuta la comprobación en la fila de un alumno de la lista de la clase: inicia una copia de su máquina guardada que no se vuelve a guardar y muestra si pasó y qué imprimió.

La [guía de los laboratorios de VM Ubuntu](https://mitsurutech.co.uk/es/help/labs) del manual y su [página de laboratorios de ejemplo](https://mitsurutech.co.uk/es/help/sample-labs) explican el resto. Para crear tu propia topología, copia un archivo y cambia la topología en su `setup`: una línea por cada router, host y enlace, y `daemons ospfd` o `daemons bgpd` para iniciar ese protocolo en todos los routers.

¿Todavía no tienes una cuenta de centro? [El primer año es gratis](https://mitsurutech.co.uk/es/pricing) para hasta 50 alumnos, y [registrarse](https://mitsurutech.co.uk/es/signup) solo requiere una dirección de correo electrónico.

## Comprobar los archivos

En cada push, `.github/workflows/check.yml` comprueba que cada archivo sea YAML válido con las cinco claves de arriba y que `setup` y `check` quepan cada uno en el límite de 16 KB de la plataforma. No arranca ninguna VM: eso se hace en las propias máquinas de la plataforma.

## Lo que no funciona en un laboratorio

La VM llega al archivo de paquetes de Ubuntu, a PyPI, a npm y a GitHub, y a nada más, así que un laboratorio no se puede apuntar a un host de Internet. Es una sola VM, así que no hay una segunda máquina desde la que atacar ni a la que atacar; dentro se construye una topología de varios routers.

## Licencia

Esta obra está bajo licencia [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Puedes compartirla y adaptarla para aprender y enseñar, si citas a Mitsuru Technologies Ltd, no la usas comercialmente y compartes lo que hagas con ella bajo la misma licencia. El texto completo está en el archivo `LICENSE`. Cubre las definiciones de laboratorio, la herramienta `netlab` y esta documentación. [FRRouting](https://frrouting.org) tiene licencia GPL-2.0 y se instala desde el archivo de Ubuntu cuando arranca una VM; nada de él se distribuye aquí.

## Acerca de

Creado por [Mitsuru Technologies](https://mitsurutech.co.uk) para la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning): Python, Java, Go, Node, SQL y Linux en el navegador para escuelas y centros, con laboratorios de contenedores, laboratorios de VM, aplicaciones web desplegadas desde GitHub y cuestionarios que se califican solos. Prueba código sin cuenta en el [entorno de pruebas](https://mitsurutech.co.uk/es/playground).

Más laboratorios del mismo sitio: [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) y [lab-sample](https://github.com/mitsurutech/lab-sample).
