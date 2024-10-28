# SCRIPTS

## Architecture

- uname -a                  >>>             muestra la alquitectura del SO(Kernel).

Linux kegonzal42 6.1.0-26-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.112-1 (2024-09-30) x86_64 GNU_Linux

## Physical Nuclei

- grep "physical id" /proc/cpuinfo | wc -l                  >>>             muestra el número núcleos físicos.

0       >>>         sólo tiene un procesador.
1       >>>         tiene más de un procesador. (nuestro caso)

## Virtual Nuclei

- grep processor /proc/cpuinfo | wc -l              >>>             muestra el número núcleos físicos.

0       >>>         sólo tiene un procesador.
1       >>>         tiene más de un procesador. (nuestro caso)

##  RAM

- free --mega           >>>             muestra la memoria utilizada.
- free --help           >>>             más info del comando.

        -total- -used-   -free-  -shared-   -buff/cache-    -available-
Mem      1007    205       830       0           90              801
Swap     1023     0       1023      

- free --mega | awk '$1 == "Mem:" {print $3}'           >>>         Muestra sólo la memoria utilizada.

205 (nuestro caso).

- free --mega | awk '$1 == "Mem:" {print $2}'           >>>         Muestra sólo la memoria TOTAL.

1007 (nuestro caso).

- free --mega | awk '$1 == "Mem:" {printf("(%.2f%%)\n", $3/$2*100)}'            >>>         muestra el porcentaje utilizado.
DESGLOCE:
    free --mega         >>  busca el fichero
    awk '$1 == "Mem:"   >>  selecciona la fila cuya columna "1" sea "Mem:"
    printf()            >>  Imprime.
    %.2f                >>  Imprime un número con dos decimales.
    %%                  >>  Imprime el símbolo %.
    $3/$2*100           >>  Calcula el porcentaje entre las columnas 3 y 2.

23.14 % (nuestro caso).

## Memory Disk

- df            >>>         muestra la gestión de memoria.

Incialmente el comando muestra la información en bloques, que es distinto a MB o KB u otro formato legible para humanos. Considerar que 1Kb equivale a 1'024'000 bloques.

- df -h            >>>         muestra la info de la memoria en formato de bytes.

- df -m | grep "/dev/" | grep -v "/boot" | awk '{memory_use += $3} END {print memory_use}'

DESGLOCE:
    df -m               >>> Muestra la info de bytes.
    grep "/dev/"        >>> obtiene sólo los continene lo indicado.
    grep -v "/boot"     >>> omite lo que contiene lo indicado.
    {memory_use += $3}  >>> suma, al memory_use el valor de la tercera columna.
    {print memory_use}  >>> imprime el nuevo valor de memory_use.

- df -m | grep "/dev/" | grep -v "/boot" | awk '{memory_result += $2} END {printt("%.0fGb\n"), memory_result/1024}'

DESGLOCE:
    {memory_result += $2}   >>> Suma el resultado de la segunda columna
    ("%.0fGb\n")            >>> No muestra decimales.
    memory_result/1024      >>> divide en 1024 bloques (para obtener los bytes)

- df -m | grep "/dev/" | grep -v "/boot" | awk '{use += $3} {total += $2} END {printf("(%d%%)\n"), use/total*100}           >>>         obtenermos el porcentaje de la memoria usada.

## CPU's Use

- vmstat            >>>         muestra información de la CPU

-procs- -------------memory-----------  ----swap-----  -----io-----   ---system---    ---------------cpu-----------------
-r- -b- -swpd-  -free-  -buff-  -cache- -si-    -so-    -bi-    -bo-  -in-    -cs-    -us-    -sy-    -id-    -wa-    -st-
2    0     0     82784   8972    78240    0      0      232      12    307     62      0       0       99       1       0

PROCESOS: 
    · r         >>>         número de procesos en ejecución (listos para usar la CPU)
    · b         >>>         núero de procesos en espera.

MEMORIA:
    · swpd      >>>         memoria en intercambio (en KB)
    · free      >>>         memoria libre (en KB)
    · buff      >>>         memoria usado los buffers.
    · cache     >>>         memoria usado el cache.
    
Diferencia entre buffer y cache en el kernel:
Buffer: almacena datos temporalmente en tránsito entre la memoria y los dispositivos de E/S.
Cache: Almacena datos que han sido leídos o escritos recientemente en memoria, facilita el accesos a ellos rápidamente sin operaciones adicionales.

SWAP (Intercambio):
    · si        >>>         memoria transferida desde el área de intercambio a la memoria principal.
    · so        >>>         memoria que se transfiere de mem princ al área de intercambio.

IO (In/Out):
    · bi        >>>         bloques recibidos (en KB/s).
    · bo        >>>         bloques enviados (en KB/s).

SYSTEM (Interruptions):
    · in        >>>        número de interrupciones.
    · cs        >>>        cambio de contextos por segunndo. 

    Una interrupción de sistema es una señal enviada al procesador que indica que un evento externo o interno necesita atención inmediata. Esta señal pausa temporalmente la ejecución del programa actual para que el sistema operativo pueda responder a ese evento.

        >> Interrupciones de hardware: Provienen de dispositivos físicos. Por ejemplo, cuando se presiona una tecla, el teclado envía una señal de interrupción para que el procesador registre esa entrada.

        >> Interrupciones de software: Generadas por programas en ejecución o por el sistema operativo. Un ejemplo común es una llamada al sistema (syscall) que genera una interrupción para solicitar recursos o servicios específicos del sistema operativo.

        >> Interrupciones de temporizador: Configuradas por el sistema operativo para realizar ciertas tareas periódicas, como programar procesos o medir el tiempo transcurrido.

        >> Interrupciones de excepciones: Se producen cuando hay condiciones anormales en la ejecución de un programa, como una división por cero, errores de segmentación, o desbordamiento de pila.

CPU (% del tiempo real)
    · us        >>>         tiempo de procesos de usuarios.
    · sy        >>>         tiempo de procesos de sistema.
    · id        >>>         tiempo inactivo.
    · wa        >>>         tiempo de operaciones de E/S.
    · st        >>>         tiempo "robado" por máquinas virtuales.

- vmstat 1 3 | tail -1 | awk '{cpu_use += $15} END {printf("%.1f%%\n"), 100-cpu_use}'

DESGLOCE:
    cpu_use += $15      >>> capturamos el valor de la columna 15
    printf("%.1f%%\n")  >>> imprimimos un porcentaje con un decimal.
    100-cpu_use         >>> el porcentaje a imprimir.

## Last Reset

- who           >>>         muestra los usuarios conectados

-usuario-   -terminal-      -fecha y hora-            -ip-
kegonzal       tty1         2024-10-23  15:11
kegonzal       pts/0        2024-10-23  15:12       (10.0.2.2)

- who -b            >>>         muestra el último reinicio.

system boot     2024-10-23      15:11

## LVM (Logical Volume Manager)

Tecnología que ayuda a la gestión de almacenamiento.

Volumen Físico (Physical Volume - PV): Un disco duro o parte de uno, que es parte de un grupo de volúmenes.
Grupo de Volúmenes (Volume Group -VG): conjunto de Volúmenes Físicos, crea un flexible espacio disponible entre los VG.
Volumen Lógico (Logical Volume - LV): creado dentro de los VG, flexible a la redimensión y movilidad. Albergan sistemas de archivos(/, /home. /var o swap) y es lo que utiliza el SO.

Además de la redimensión dinámica y la agrupación de discos, LVM permite "snapshots" de LV para copias de seguridad, facilitanto la mejora del rendimiento.

- lsblk         >>>         muestra info de los "DISPOSITIVOS DE BLOQUE"

Un dispositivo de bloque es un tipo de dispositivo que gestiona datos en bloque. Normalmente, son unidades de más de 512 bytes y con acceso aleatorio, es decir que leen y escriben datos en cualquier bloque; contrario a los dispositivos de carácter (teclado, ratón,...).

El dispositivo de bloque interactúa con el SO a través de un SISTEMA DE ARCHIVOS, siendo ideal para almacenar y acceder a grandes cantidades de información.

-NAME-                  -MAJ:MIN-   -RM-    -SIZE-  -RO-    -TYPE-  -MOUNTPOINTS-
sda                         8:0      0         12G     0      disk
_sda1                       8:1      0        487M     0      part       /boot
_sda2                       8:2      0         1K      0      part
_sda5                       8:5      0        11.5G    0      part
-_sda5_crypt               254:0     0        11.5G    0      crypt
-__kegonzal42--vg-root     254:1     0         4.1G    0      lvm          /
-__kegonzal42-vg-swap_1    254:2     0         976M    0      lvm       [SWAP]
-__kegonzal42-vg-home      254:2     0         6.4G    0      lvm        /home
sr0                        11:0      1        1024M    0      rom

sda                     >>> disco principal (DISK).
sda1                    >>> 487M montada en /boot con archivos de arranques.
sda2                    >>> asignada, posiblemente, para la estructura de participaciones.
sda5                    >>> 11.5G ENCRIPTADO (¿born2beroot?)
kegonzal42--vg-root     >>> LVM montada como raíz (sistema de raíz principal)
kegonzal42-vg-swap_1    >>> área de intercambio (memoria virtual del sistema).
kegonzal-vg-home        >>> MVL montada en /home (almacenamiento para usuarios).
sr0                     >>> dispositivo de lectura, unidad CD/DV o virtual (ROM).


- if [ $(lsblk | grep "lvm" | wc -l) -gt 0 ]; then echo yes; else echo no; fi           >>>         comando que verifica si LVM está activo.

DESGLOSE:
    grep "lvm"          >>> busca líneas que contengan lo indicado.
    wc -l               >>> cuenta las líneas que encontró el grep.
    -gt 0               >>> "greater than (mayor que)" 0.

## TCP (Transmission Control Protocol)

- ss            >>>         muestra "sockets" de red (punto de comunicación con apps).

TCP da una conexión garantizando la entrega y orden de datos. Ideal para navegación web y correos electrónicos.

- ss -ta | grep ESTAB | wc -l           >>>         muestra las conexiones TCP.

## Users

- users         >>>         muestra usuarios existentes.

- users | wc -w         >>>         enumera los usuarios

## IP & MAC

- hostname          >>>         muestra el nombre del host

 kegonzal42

- hostname -i       >>>         muestra la IP del host

127.0.1.1

- ip link           >>>         muestra las interfaces de red.

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mut 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:75:b4:c6 brd ff:ff:ff:ff:ff:ff

- ip link | grep "link/ether" | awk '{print $2}'            >>>         muestra la MAC seleccionada.

08:00:27:75:b4:c6

## SUDO Comands

- journalctl            >>>         muestra los registros(logs) del sistema almacenados por el servicio de loggins "system-journald".

-r                                                           :   logs ordenados por recientes
-b                                                           :   logs del arranque actual
-u <nombre_servicio>                                         :   logs de un servicio.
-k                                                           :   logs de kerkel.
-f                                                           :   logs a tiempo real.
--since="YYYY-MM-DD HH:MM:SS" --until="YYYY-MM-DD HH:MM:SS"  :   filtra logs desde x hasta y.

- journalctl _COMM=sudo | grep COMMAND | wc -l          >>>         muestra el número de comandos que son ejecutados con SUDO.

DESGLOCE:
    _COMM=sudo      >>>         muestra logs generados con SUDO.
    grep COMMAND    >>>         filtra las líneas con COMMAND
    wc -l           >>>         cuenta el total

