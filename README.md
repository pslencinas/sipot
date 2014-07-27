SIPOT: SIP Open Tester
========
**Herramienta para auditar sistemas VoIP basados en SIP**

### Implementa 3 módulos: ###
*   **Flooder:** Realizar [flooding](http://es.wikipedia.org/wiki/Flood) sobre un objetivo.
*   **Fuzzer:** Realizar [fuzz testing](http://en.wikipedia.org/wiki/Fuzz_testing) sobre un objetivo.
*   **Spoofer:** Utlizar técnicas de [spoofing](http://es.wikipedia.org/wiki/Spoofing) sobre un objetivo


## Dependencias: ##
Todas las dependencias han sido embebidas dentro del paquete. Pueden encontrarse las librerías utilizadas dentro de la carpeta /lib.

### 39 Peers ###
> "_The 39 Peers project aims at implementing an open-source peer-to-peer Internet telephony software using the Session Initiation Protocol (P2P-SIP) in the Python programming language._"

*   **Link:** [39 Peers](https://pypi.python.org/pypi/multitask/) .
*   **Licencia:** GNU/GPL.
*   **Uso:** Librería básica del protocolo SIP.

### Multitask 0.2.0 ###
> "_Cooperative multitasking and asynchronous I/O using generators_"

*   **Link:** [Multitask 0.2.0](https://pypi.python.org/pypi/multitask/).
*   **Licencia:** MIT License.
*   **Uso:** Para generar tareas simultaneas.

### Sulley ###
> "_Pure Python fully automated and unattended fuzzing framework_"

*   **Link:** [Sulley](https://code.google.com/p/sulley/).
*   **Licencia:** GNU GPL v2.
*   **Uso:** Para generar los mensajes y mutarlos en el modo fuzzing.


## Descripción general: ##
La presente herramienta propone el uso de la librerìa de 39Peers desarrollada por Kundan Singh para el desarrollo de una herramienta interactiva que permita auditar sistemas VoIP basados en SIP.

1.   **Flooding**
2.   **Fuzzing**
3.   **Spoofing**

### Opciones básicas ###
#### Generales ####
    --version             show program's version number and exit
    -h, --help            show this help message and exit
    -v, --verbose         enable verbose mode for this module
    -V, --verbose-all     enable verbose mode for all modules

#### Opciones de red ####
    --transport=TRANSPORT
                        the transport type is one of "udp", "tcp" or "tls".
                        Default is "udp"
    --int-ip=INT_IP     listening IP address for SIP and RTP. Use this option
                        only if you wish to select one out of multiple IP
                        interfaces. Default "0.0.0.0"
    --port=PORT         listening port number for SIP UDP/TCP. TLS is one more
                        than this. Default is 5092
    --fix-nat           enable fixing NAT IP address in Contact
    --max-size=MAX_SIZE
                        size of received socket data. Default is 4096
    --interval=INTERVAL
                        The interval argument specifies how often should the
                        sock be checked for close, default is 180 s
#### Opciones básicas del protocolo SIP ####

    --username=USERNAME
                        username to use in my SIP URI and contacts. Default is
                        "nitsuga"
    --pwd=PASSWORD      set this if REGISTER requires pasword authentication.
                        Default is empty "" to not set.  A list of passwords
                        can be provided in the form of pwd1,pwd1,...,etc.
    --domain=DOMAIN     domain portion of my SIP URI. Default is to use local
                        hostname, which is "nitsuga-CX61-laptop"
    --proxy=PROXY       IP address of the SIP proxy to use. Default is empty
                        "" to mean disable outbound proxy
    --to=TO             the target SIP address, e.g., '"Henry Sinnreich"
                        <sip:henry@iptel.org>'. This is mandatory
    --from=FROMADDR     the user SIP address, e.g., '"Henry Sinnreich"
                        <sip:henry@iptel.org>'.
    --uri=URI           the target request-URI, e.g., "sip:henry@iptel.org".
                        Default is to derive from the --to option
    --register          enable user register befor sending messages
    --reg-username=REG_USERNAME
                        username used to for register. If not porvided
                        --username will be used.
    --reg-ip=REGISTRAR_IP
                        Registrar IP. If not provided is extracted from to
                        address: A registrar is a server that accepts REGISTER
                        requests and places the information it receives in
                        those requests into the location service for the
                        domain it handles.
    --register-interval=REGISTER_INTERVAL
                        registration refresh interval in seconds. Default is
                        3600
    --reg-refresh       Auto refresh registration. The refresh argument can be
                        supplied to automatically perform registration refresh
                        before the registration expires. Do not perform
                        refresh by default.
#### Selección del modulo a utilizar ####
    -M SIPOT_MODE, --sipot-mode=SIPOT_MODE
                        flooding / fuzzing / spoofing. set the mode of attack
                        for SIPOT. Default is flooding.
### Ejemplos de prueba ###
Registrar una extensión SIP:

`python sipot.py --register --username 109 --pwd abc123 --reg-ip 192.168.56.77`

## Módulos: ##
La presente herramienta consta de (3) módulos principales, con sus diferentes opciones, para explotar tres de los ataques más comunes realizados a servidores SIP:

### Flooding ###
_**Estado:** Completo (en verificación)._

#### Descripción ####

> [Flood](http://es.wikipedia.org/wiki/Flood)  es un término en inglés que significa literalmente inundación.Se usa en la jerga informática para designar un comportamiento abusivo de la red de comunicaciones, normalmente por la repetición desmesurada de algún mensaje en un corto espacio de tiempo.

Este módulo se encarga del envío de mensajes SIP a la dirección destino en un flujo continuo e ininterrumpido. El objetivo del módulo es sobrecargar el servidor y generar denegación de servicios por uso de la red, sobrecarga de memoria o carga de CPU.
El uso del módulo es muy sencillo pero efectivo. Los mensajes son generados a partir del método seleccionado (`--flood-method`) o de un archivo que lo contenga (`--flood-msg-file`). La aplicación enviará la cantidad deseada de mensajes  (`--flood-number`) en forma constante al objetivo seleccionado (`--to`). En este modo de uso, la aplicación no escuchará las respuestas proporcionadas por el objetivo.

#### Opciones ####
    --flood-number=FLOOD_NUM
                        Sets the number of messages to be sent by flooding
                        mode. Default is 500.
    --flood-method=FLOOD_METHOD
                        Set the method to flood. Default is REGISTER.
    --flood-msg-file=FLOOD_MSG_FILE
                        Provide a message from file to flood.
    --no-modify-ext     If not specified, extentions will be modified in each
                        message flooded. To generate extentions options --ext-
                        dictionary &--ext-range  will be used.

#### Ejemplos ####
Flood 500 Msg to 192.168.56.77:

`python sipot.py --sipot-mode flooding --to sip:109@192.168.56.77:5060 --flood-number 500`

Flood 500 Msg from File to 192.168.56.77:

`python sipot.py --sipot-mode flooding --to sip:109@192.168.56.77:5060 --flood-number 500 --flood-msg-file example_sipot_flood_this.txt`

Flood 500 Msg to 192.168.56.77 changing extentions with dictionary:

`python sipot.py --sipot-mode flooding --to sip:109@192.168.56.77:5060 --flood-number 500 --ext-dictionary example_sipot_ext_dict.txt` 


### Fuzzing ###
_**Estado:** Completo (en verificación)._

#### Descripción ####

> La técnica del [Fuzz testing](http://en.wikipedia.org/wiki/Fuzz_testing) consiste en realizar diferentes test de software capaces de generar y enviar datos secuenciales o aleatorios a una aplicación, con el objeto de detectar defectos o vulnerabilidades. El fuzzing sirve para encontrar vulnerabilidades del tipo: format string, integer overflow, buffer overflow, format string, etc.

Este módulo se encarga del envío de mensajes SIP a la dirección destino en un flujo continuo e ininterrumpido. Con cada envío, el mensaje es _mutado_ con leves variaciones en los campos enviados. El objetivo del módulo es el de generar denegación de servicio en el servidor a través de explotar una vulnerabilidad de _parseo_, _buffer overlow_, etc. El módulo posee opciones expuestas para cambiar el módulo generador de mensajes (`--fuzz-fuzzer`), limitar la cantidad máxima de mensajes enviados (`--fuzz-max=FUZZ_MAX_MSGS`), en incluse realizar una verificación automática de la disponibilidad del servidor (`--fuzz-crash`). Para la generación de mensajes y sus mutaciones se utiliza como base el framework con licencia _GNU GPL v2_ llamado [Sulley](https://code.google.com/p/sulley/).


#### Opciones ####
    -l, --fuzz-fuzzer-list
                        Display a list of available fuzzers or display help on
                        a specific fuzzer if the -f option is also provided
    --fuzz-fuzzer=FUZZER
                        Set fuzzer. Default is InviteCommonFuzzer. Use -l to
                        see a list of all available fuzzers
    --fuzz-crash        Enables crash detection
    --fuzz-crash-method=CRASH_METHOD
                        Set crash method. By default uses OPTIONS message and
                        stores response.
    --fuzz-crash-no-stop
                        If selected prevents the app to be stoped when a crash
                        is detected.
    --fuzz-max=FUZZ_MAX_MSGS
                        Sets the maximum number of messages to be sent by
                        fuzzing mode. Default is max available in fuzzer.
    --fuzz-to-file=FILE_NAME
                        Print the output to a file with the given name.

#### Ejemplos ####
Fuzzes the headers commonly found in a SIP INVITE request to 192.168.56.77:

`python sipot.py --sipot-mode fuzzing --to sip:109@192.168.56.77:5060`

Fuzzes the headers commonly found in a SIP REGISTER request to 192.168.56.77:

`python sipot.py --sipot-mode fuzzing --fuzz-fuzzer REGISTERFuzzer --to sip:109@192.168.56.77:5060`

Uses all available fuzzers to 192.168.56.77:

`python sipot.py --sipot-mode fuzzing --fuzz-fuzzer --fuzz-max 10 All --to sip:109@192.168.56.77:5060`

Fuzz and print results to a file:

`python sipot.py --sipot-mode fuzzing --fuzz-crash --fuzz-to-file example_fuzz_results.txt --to sip:109@192.168.56.77:5060`



### Spoofing ###
_**Estado:** No desarrollado aún._






[Markup Preview](http://github-markup.dfilimonov.com/)
[MultiMarkdown](https://github.com/fletcher/MultiMarkdown/blob/master/Documentation/Markdown%20Syntax.md)







