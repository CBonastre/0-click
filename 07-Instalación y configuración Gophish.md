# Instalación y Configuración de Gophish  

El siguiente paso es instalar Gophish en nuestro servidor y configurarlo correctamente para que funcione con los certificados previamente generados.

## Descarga de Gophish

El primer paso es descargar Gophish desde el repositorio oficial. Utiliza el siguiente comando para descargar la última versión disponible, asegurándote de obtener la versión más actualizada:

```bash
git clone https://github.com/gophish/gophish/releases/download/v0.12.1/gophish-v0.12.1-osx-64bit.zip
```

**Nota:** Asegúrate de verificar si hay versiones más actualizadas en el repositorio oficial.
## Creación carpeta

Una vez descargado, moveremos el archivo a un directorio nuevo dentro de `/opt`.

```
sudo mkdir /opt/gophish
```
```
sudo mv ./gophish-v0.12.1-osx-64bit.zip /opt/gophish
```
 
## Descompresión del Archivo

Ahora nos moveremos al directorio creado y descomprimiremos el archivo descargado. Utiliza los siguiente comandos:

```
cd /opt/gophish
```

```bash
unzip gophish-v0.12.1-osx-64bit.zip
```

## Modificación del Archivo de Configuración

Una vez en la ruta `/opt`, procederemos a modificar el archivo de configuración `config.json` según nuestras necesidades.

Tras su configuración el archivo debe verse tal que así:

![config json](https://github.com/CBonastre/0-click/assets/151465796/f7c4d0e4-a6eb-482e-a5f9-8e33e25371ea)

## Guardar Cambios y Ejecutar Gophish

Una vez modificado el archivo de configuración, guardaremos los cambios. Luego, daremos permisos de ejecución a Gophish con el comando `sudo chmod +x gophish` y lo ejecutaremos con el comando `sudo ./gophish`. **Es importante anotar las credenciales de acceso que nos aparecen al ejecutar Gophish**, ya que las necesitaremos para logearnos por primera vez en el panel de administración.

## Acceso al Panel de Administración

Con Gophish corriendo, podemos abrir nuestro navegador y acceder al panel de administrador buscando: `https://tudominio:3333`, donde `tudominio` es la dirección de tu servidor. Debería aparecer el siguiente panel de login:

![Captura de pantalla 2024-05-04 172616](https://github.com/CBonastre/0-click/assets/151465796/5df751c6-ca3d-4bc9-8cce-6d3cc26be5d9)


Aquí deberemos iniciar sesión con las credenciales que hemos apuntado previamente y nos pedirá que cambiemos la contraseña de acceso. Una vez hecho eso, ya tendremos instalado y configurado Gophish para su correcto funcionamiento.



