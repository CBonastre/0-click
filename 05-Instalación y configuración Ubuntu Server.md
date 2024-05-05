# Instalación de Ubuntu Server utilizando Ventoy y Configuración de IP estática

Este documento proporciona instrucciones paso a paso sobre cómo instalar Ubuntu Server en un servidor utilizando Ventoy y cómo configurar una dirección IP estática para el servidor.

## Instalación de Ubuntu Server con Ventoy

1. **Descarga de Ubuntu Server:**
   - Visita el sitio web oficial de Ubuntu (https://ubuntu.com/download/server) y descarga la imagen ISO de Ubuntu Server.

2. **Descarga e Instalación de Ventoy:**
   - Visita el sitio web de Ventoy (https://www.ventoy.net/en/index.html) y descarga la versión correspondiente para tu sistema operativo.
   - Sigue las instrucciones de instalación proporcionadas en el sitio web de Ventoy.

3. **Creación de una unidad USB de arranque con Ventoy:**
   - Conecta una unidad USB a tu computadora.
   - Ejecuta Ventoy y selecciona la unidad USB como destino para la instalación.
   - Copia la imagen ISO de Ubuntu Server descargada en el directorio raíz de la unidad USB.

4. **Arranque desde la unidad USB:**
   - Conecta la unidad USB al servidor.
   - Reinicia el servidor y accede al menú de arranque (generalmente presionando una tecla específica como F12 durante el inicio).
   - Selecciona la unidad USB como dispositivo de arranque.

5. **Instalación de Ubuntu Server:**
   - Sigue las instrucciones en pantalla para instalar Ubuntu Server en el servidor.

## Configuración de IP estática en Ubuntu Server

Una vez que Ubuntu Server esté instalado, sigue estos pasos para configurar una dirección IP estática:

1. **Acceso al servidor:**
   - Inicia sesión en Ubuntu Server utilizando las credenciales de administrador.

2. **Edición del archivo de configuración de red:**
   - Edita el archivo de configuración de red utilizando un editor de texto como `nano` o `vim`:
     
     ```
     sudo nano /etc/netplan/00-installer-config.yaml
     ```

4. **Configuración de la dirección IP estática:**
   - Modifica el archivo para que se vea similar a esto:
     
     ```yaml
     network:
       version: 2
       ethernets:
         enp0s3:   # Reemplaza "ensXXX" con el nombre de tu interfaz de red (puedes encontrarlo ejecutando el comando "ip a").
           dhcp4: no
           addresses: [192.168.1.33/24]   # Cambia la dirección IP según tus necesidades.
           gateway4: 192.168.1.1   # Cambia la puerta de enlace predeterminada si es diferente.
           nameservers:
             addresses: [8.8.8.8, 8.8.4.4]   # Opcional: cambia los servidores DNS si es necesario.
     ```

5. **Aplicación de los cambios de configuración:**
   - Guarda y cierra el archivo de configuración.
   - Aplica los cambios ejecutando el siguiente comando:
     
     ```
     sudo netplan apply
     ```

6. **Verificación de la configuración:**
   - Verifica que la configuración se haya aplicado correctamente ejecutando el comando:
     
     ```
     ip a
     ```

   - Comprueba que la interfaz de red tenga la dirección IP estática configurada.

Ahora el servidor Ubuntu Server ya está instalado y configurado correctamente para la instalación de Gophish.

**NOTA**: Es importante realizar un correcto redirecionamiento de puertos en el enrutador para un correcto funcionamiento.
