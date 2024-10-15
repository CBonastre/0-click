# Creación de Dominio y Certificados SSL

Una vez configurado el servidor y realizado el correcto redireccionamiento de puertos en el enrutador, el siguiente paso es crear un dominio para poder generar los certificados SSL correspondientes. Para mayor comodidad y agilidad en la generación de dominios para distintas campañas de phishing, se opta por utilizar un servicio DNS en la nube.

## Creación del Dominio

1. **Elección del Servicio DNS en la Nube:** Seleccionar un proveedor de servicios DNS en la nube confiable y adecuado para las necesidades del proyecto.
   
2. **Configuración del Dominio:** Configurar el dominio deseado utilizando el servicio DNS en la nube elegido, siguiendo las instrucciones proporcionadas por el proveedor.

![Captura de pantalla 2024-05-04 165255](https://github.com/CBonastre/0-click/assets/151465796/e79174e1-6b0a-4b16-ab02-6d09a0e06c13)


## Generación de Certificados SSL con Certbot

Una vez creado el dominio, se procede a generar los certificados SSL utilizando Certbot, una herramienta ampliamente utilizada para la gestión de certificados SSL en servidores web.

### Comando de Certbot:

```bash
certbot certonly --standalone --cert-name (nombre del certificado) -d (dominio) -m (correo para recibir avisos de caducidad del certificado) --agree-tos --noninteractive
```
**--standalone:** Utiliza un servidor web independiente para la emisión del certificado.

**---cert-name:** Nombre personalizado para el certificado.

**-d:** Especifica el dominio para el cual se va a generar el certificado SSL.

**-m:** Dirección de correo electrónico donde se recibirán los avisos de caducidad del certificado.

**--agree-tos:** Acepta automáticamente los términos de servicio de Let's Encrypt.

**--noninteractive:** Ejecución en modo no interactivo, adecuado para scripts automatizados.

La salida del comando debería ser algo parecido a lo siguiente:

![Certificados](https://github.com/CBonastre/0-click/assets/151465796/6462b679-c940-4b6c-a634-d41a97c82c69)

> [!TIP]
> Acordarse de la ruta donde se guardan los certificados, necesitaremos ese path para realizar la configuración de los certificados en Gophish.

Con los certificados SSL y el dominio correctamente creados, se puede instalar y configurar Gophish.
