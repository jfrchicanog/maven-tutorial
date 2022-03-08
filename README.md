# Tutorial de Apache Maven

En este documento se presenta un breve tutorial pensado para introducir Maven en la asignatura de Sistemas de Información para Internet, de tercero del Grado en Ingeniería Informática de la Universidad de Málaga (España). Para obtener más información sobre Maven y recursos adicionales se puede consultar la [página oficial de Apache Maven](https://maven.apache.org/index.html).

## ¿Qué es Maven?

Es una herramienta y un sistema de distribución de paquetes que facilita la construcción de aplicaciones y bibliotecas en Java. Se parece a herramientas como `make` o el paquete `autoconf`. En el mundo Java es similar a herramientas como `ant` (que es anterior) y `gradle` (que es posterior).

## ¿Qué ventajas tiene Maven?

Algunas de las ventajas de Maven para la construcción de código Java son:
* __Identificación de paquetes__: todos los paquetes tienen unas coordenadas que permiten de forma unívoca, lo que facilita la identificación de paquetes y versiones.
* __Distribución de paquetes__: los repositorios de Maven permiten almacenar paquetes de forma que no sea necesario descargar de una URL los paquetes de forma manual, sino que Maven detecta las dependencias y los descarga de forma automática.
* __Gestión de dependencias__: permite la declaración de dependencias de los proyectos y se encarga de resolver dichas dependencias descargando los paquetes necesarios.
* __Automatización de procesos de construcción complejos__: es posible ejecutar un complejo flujo de construcción usando un simple comando.
* **Independencia de los IDEs**: permite una construcción del software completo con independencia del entorno de desarrolo (IDE) que usemos. Los principales IDEs tienen soporte para Maven y es posible realizar la construcción sin ellos. Esto es especialmente útil en un equipo de desarrollo donde cada persona tiene un IDE diferente.
* __Ejecución de pruebas unitarias y de integración y reporte de resultados__: distingue entre pruebas unitarias, que se ejecutan sobre clases particulares tras la compilación y antes del empaquetar el software, y pruebas de integración, que se ejecutan una vez que el software está empaquetado. Permite reportar el resultado tras la ejecución de las pruebas y puede incluirse en herramientas de control de calidad como SonarQube.
* __Construcción de proyectos con múltiples módulos__: se pueden definir módulos dentro de un proyecto para software más complejo e idenfica las dependencias entre módulos para realizar al construcción en el orden correcto.

## Conceptos básicos

En esta sección explicaremos algunos conceptos básicos de Maven, necesarios para comenzar a trabajar con él.

### Coordenadas y dependencias

Una de las grandes ventajas de Maven es que se pueden especificar de forma declarativa las dependencias entre distintas bibliotecas (paquetes). Antes de la existencia de este tipo de herramientas, las bibliotecas debían descargarse manualmente (normalmente un fichero JAR) y colocarlas en una carpeta asociada al proyecto para incluirlas en el _classpath_ durante la construcción y la ejecución. Si la biblioteca descargadda tenía otras dependencias, estas también debían descargarse, y así sucesivamente hasta resolver todas las dependencias. Con Maven basta indicar de qué biblioteca depende nuestro proyecto y la herramienta se encargará de buscar la dependencia (descargarla si es necesario) y todas sus dependencias y ubicarla en el _classpath_ para la construcción del software.





### Project Object Model (pom)

### Ciclo de vida de la construcción: objetivos y fases

## Ejecución de la contrucción

## Propiedades en Maven

## Proyectos con múltiples módulos

## Arquetipos

