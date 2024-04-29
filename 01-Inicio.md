# Proyecto de Investigación: Ataques 0-clic y Software Pegasus

## Introducción

Este proyecto de investigación nació de la curiosidad al descubrir el impactante poder del software Pegasus para infectar dispositivos sin la interacción consciente del usuario. Mi interés por comprender su funcionamiento fue el motor inicial de esta investigación.

## Comienzo de la Investigación

Al principio, mis conocimientos eran limitados, ya que estaba cursando el primer año del ASIX en ciberseguridad. Sin embargo, junto a compañeros igualmente intrigados, comenzamos a explorar este tema complejo.

### Descubrimiento del Manual de Pegasus

Nuestro progreso dio un giro cuando, por casualidad y con la ayuda de un profesor, obtuvimos una copia de un antiguo manual del software Pegasus. Este documento nos proporcionó información sobre los vectores de ataque, en especial el enfoque en la técnica OTA (Over The Air).

#### Explicación de OTA

OTA es un método que permite la actualización inalámbrica de software en dispositivos sin la necesidad de una conexión física. Esto facilita la instalación remota de software, como en el caso de Pegasus.

### Descubrimiento de la Vulnerabilidad CVE-2022-36934

Nuestra búsqueda nos llevó a revisar bases de datos de vulnerabilidades, donde finalmente encontramos información crucial. La vulnerabilidad CVE-2022-36934, reportada en WhatsApp, permitía a un atacante ejecutar código arbitrario debido a un desbordamiento de un entero en las videollamadas de la aplicación.

#### Desbordamiento de Entero

Ahora nos enfrentábamos a la pregunta de qué significaba realmente un desbordamiento de entero o buffer y cómo esto posibilitaba la ejecución remota de código.

## Exploración de las Vulnerabilidades y Código Remoto

La siguiente etapa de la investigación se centró en comprender en detalle cómo estos errores de software, como el desbordamiento de entero, podían permitir a los atacantes ejecutar código de manera remota.
