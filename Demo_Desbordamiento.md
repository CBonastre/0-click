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
### Enumeración de Puertos Abiertos en la Máquina Víctima

Una vez identificada la dirección IP de la máquina víctima (en este caso, la IP es `10.40.2.13`), utilizaremos la herramienta NMAP para realizar la enumeración de los puertos abiertos en nuestro objetivo.

Utilizaremos el siguiente comando NMAP:

```bash
nmap -p- --open -T5 -v -n <IP objetivo>
```

### Verificación de los Servicios en los Puertos Identificados

Después de identificar los puertos abiertos, en este caso, los puertos `9999` y `10000`, procederemos a verificar qué servicios están en ejecución detrás de cada uno de ellos.

Para ello, abriremos un navegador web y en la barra de direcciones ingresaremos lo siguiente:

- **Para el Puerto 10000:**
  http://IP_Brainpan:10000
  Este paso nos permitirá verificar si algún servicio específico responde en el puerto 10000. Y al acceder podemos que es una pagina web.

- **Para el Puerto 9999:**
  http://IP_Brainpan:9999
### Interacción con el Portal de Acceso en el Puerto 9999

Al acceder al puerto 9999, nos encontramos con un portal de acceso que requiere una contraseña para continuar. Este portal podría estar relacionado con un binario o servicio específico.

**Conexión al Puerto 9999 mediante Netcat (nc)**

Para intentar establecer una conexión directa al puerto 9999, utilizaremos la herramienta Netcat (nc) desde nuestra máquina Kali Linux. El objetivo es explorar más a fondo el comportamiento de este servicio y realizar pruebas de conexión directa.

Usaremos el siguiente comando:

```bash
nc IP_Brainpan 9999
```
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
Tras introducir la cadena generada en el campo de contraseña del portal de acceso en el puerto 9999, observamos que el servicio se vuelve inoperativo. Al intentar una nueva conexión, se nos deniega el acceso, lo cual sugiere claramente que existe una vulnerabilidad de desbordamiento de buffer en el sistema.
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
Después de ejecutar el comando Gobuster, se identificó la presencia de un subdirectorio relevante: "/bin". Al acceder a este subdirectorio, confirmamos que es posible descargar el ejecutable.

#### Descarga del Ejecutable para Análisis Local

Procederemos a descargar este ejecutable en nuestra máquina con sistema operativo Windows. El propósito es trabajar con este archivo en un entorno controlado para llevar a cabo un análisis minucioso. Esta acción nos permitirá comprender mejor el funcionamiento interno del código y su comportamiento, particularmente cuando se le aplica un desbordamiento de buffer.

### Análisis Inicial del Ejecutable con Immunity Debugger

Una vez que tenemos el ejecutable en nuestra máquina Windows, procedemos a ejecutarlo. Para analizar su comportamiento y realizar un seguimiento de la ejecución del programa, abriremos la herramienta Immunity Debugger.

**Uso de Immunity Debugger para Analizar el Ejecutable**

Al abrir Immunity Debugger, seleccionamos el ejecutable Brainpan navegando a "File > Attach". Esto nos permitirá analizar el programa en ejecución y observar detalles cruciales, como direcciones de memoria y otros datos relevantes.

**Objetivo: Sobreescribir el Registro EIP**

Nuestro objetivo es conseguir sobreescribir el registro EIP en la memoria. Este registro, el Puntero de Instrucción Extendido, indica la próxima instrucción a ejecutar en el programa. Al lograr sobreescribir este registro, podríamos tomar control sobre la secuencia de ejecución del programa y manipular su comportamiento.

Este paso es esencial para explorar vulnerabilidades como el desbordamiento de buffer y buscar cómo podemos influir en el flujo de ejecución del programa para obtener un control más significativo sobre su funcionamiento.







