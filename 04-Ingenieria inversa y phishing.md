## Explorando Ingeniería Inversa

Después de adentrarnos en el mundo de los desbordamientos de buffer y comprender su funcionamiento, nuestro equipo decidió ampliar nuestros horizontes hacia la ingeniería inversa. La motivación detrás de este cambio de enfoque fue clara: queríamos comprender más profundamente las vulnerabilidades presentes en el código de la versión afectada de WhatsApp.

Con el tiempo, nos dimos cuenta de que lograr un ataque de tipo "0 clic" era una tarea considerablemente compleja y requería una combinación de tiempo y conocimientos que, sinceramente, no poseíamos en ese momento. Surgió entonces una pregunta crucial en nuestras discusiones internas: ¿realmente necesitábamos alcanzar un nivel tan elevado de complejidad para obtener datos sensibles de un usuario?

Tras un intenso debate, llegamos a la conclusión de que había otras vías para evaluar la seguridad y conciencia de los usuarios. Nos enfrentamos a la pregunta de si una campaña de phishing, una de las tácticas más conocidas en el ámbito de la ciberseguridad, podría ofrecernos una perspectiva más clara sobre este tema.

## Experimentando con Campañas de Phishing

Con el objetivo de evaluar el nivel de concienciación de los usuarios, decidimos llevar a cabo una campaña de phishing por nuestra cuenta. Esta decisión nos permitiría no solo comprender la susceptibilidad de los usuarios a los ataques de ingeniería social, sino también identificar áreas de mejora en cuanto a la concienciación sobre seguridad informática.

Para llevar a cabo esta campaña, desplegamos los siguientes recursos:

- **Servidor Ubuntu:** Configuramos un servidor Ubuntu donde implementamos todas las herramientas necesarias para ejecutar la campaña de phishing.
- **Instalación de GoPhish:** Utilizamos GoPhish, una herramienta de código abierto diseñada específicamente para ejecutar campañas de phishing y pruebas de concienciación sobre seguridad.
- **Creación de un Dominio:** Registramos un dominio para nuestro servidor, proporcionando una apariencia auténtica a los correos electrónicos enviados como parte de la campaña.
- **Generación de Certificados SSL:** Implementamos certificados SSL para garantizar que la comunicación entre los usuarios y nuestro servidor sea segura y esté cifrada.
- **Creación de Correos Electrónicos:** Diseñamos y generamos correos electrónicos persuasivos y convincentes que imitaban a los remitentes legítimos, utilizando técnicas de ingeniería social para inducir a los usuarios a realizar acciones específicas, como hacer clic en enlaces maliciosos o proporcionar información confidencial.

Este enfoque meticuloso nos permitió ejecutar la campaña de phishing de manera efectiva, garantizando que estuviera diseñada para ser lo más realista posible, mientras manteníamos un entorno controlado y ético para llevar a cabo nuestras pruebas.

