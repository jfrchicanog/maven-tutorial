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

Para poder especificar las dependencias de forma declarativa, es necesario que cada paquete en Maven esté identificado. Para eso se usan las _coordenadas Maven_:
* __groupId__: es un identificador que normalmente identifica la organización o el grupo de desarrollo.
* __artifactId__: identifica el paquete dentro del grupo de desarrollo (su nombre).
* __version__: es la versión del artefacto. Se recomienda seguir el estándar de [Versionado semático 1.0.0](https://semver.org/spec/v1.0.0.html).

La terna anterior identifica de forma unívoca un paquete concreto (un fichero JAR) que puede contener una biblioteca, una aplicación, recursos, etc. De esta forma es posible indicar claramente en un proyecto qué dependencias tenemos. Es habitual usar la notación `groupId:artifcatId:version` para identificar de forma concisa un paquete Maven.

### Repositorios

Los paquetes, una vez construidos pueden publicarse en repositorios de paquetes. En todo sistema que use Maven existe un _repositorio local_, asociado al usuario que ejecuta Maven, y que normalmente se encuentra en el directorio `.m2` dentro del directorio de trabajo del usuario. Este repositorio local actúa como una caché para almacenar los paquetes descargados por Maven de otros repositorios remotos. También se puede utilizar para instalar paquetes que estamos construyendo por si los necesitan otros proyectos (usando el comando `mvn install`). 

Si Maven no encuentra un paquete en el repositorio local, busca en repositorios remotos. Estos repositorios pueden configurarse en un fichero llamado `settings.xml` ubicado en el directorio `.m2`. Si no confiuramos ningún repositorio remoto, Maven usará el repositorio [Maven Central](https://search.maven.org).

### Project Object Model (pom)

El __project object model__ es el elemento fundamental para la especificación y configuración de la construcción de un proyecto. Se trata de un fichero llamado normalmente `pom.xml` donde se especifica (en formato XML) las dependencias del proyecto, sus coordenadas y todas las instrucciones necesarias para la construcción del software. Cuando un proyecto se construye con Maven y contiene este fichero `pom.xml` decimos que es un _proyecto Maven_.

Vamos a ver un ejemplo de fichero `pom.xml` sencillo a continuación:

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>es.uma.informatica.sii</groupId>
    <artifactId>Persistencia</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <dependencies>
        <dependency>
            <groupId>org.eclipse.persistence</groupId>
            <artifactId>eclipselink</artifactId>
            <version>2.5.2</version>
        </dependency>
        <dependency>
            <groupId>org.eclipse.persistence</groupId>
            <artifactId>org.eclipse.persistence.jpa.modelgen.processor</artifactId>
            <version>2.5.2</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.derby</groupId>
            <artifactId>derbyclient</artifactId>
            <version>10.13.1.1</version>
        </dependency>
    </dependencies>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
</project>
```

Se peude ver cómo se especifican las dependencias del proyecto dentro del elemento `<dependencies>` del fichero XML. También se especifican aspectos como la versión Java usada para el código fuente y el código objeto, la codificación de caracteres que se debe asumir en el proyecto y el equipo de empaquetado que debe usarse (archivo JAR en este caso).

Para ver todo lo que puede ir dentro de un fichero `pom.xml` se recomienda visitar [este enlace](https://maven.apache.org/pom.html).

### Ciclo de vida de la construcción: fases y objetivos

Maven organiza la construcción de los paquetes en fases, cada una de las cuales se centra en un aspecto de la construcción del software. Las fases definidas para la construcción en Maven se pueden encontrar [aquí](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#default-lifecycle) y son las siguientes:

1. validate
2. initialize
3. generate-sources
4. process-sources
5. generate-resources
6. process-resources
7. compile
8. process-classes
9. generate-test-sources
10. process-test-sources
11. generate-test-resources
12. process-tes-resources
13. test-compile
14. process-test-classes
15. test
16. prepare-package
17. package
18. pre-integration-test
19. integration-test
20. post-integration-test
21. verify
22. install
23. deploy

No es necesario ejecutar todas estas fases cuando realizamos la construcción del software. Normalmente indicamos mediante línea de comando la fase a la que queremos llegar y Maven ejecuta todas las fases hasta la indicada (incluida). Por ejemplo, si escribimos:

```
mvn package
```
se ejecutarán todas las fases anteriores hasta llegar a la fase `package`. El resultado será un paquete con el software. Si escribimos:

```
mvn verify
```
se ejecutarán todas las fases hasta `verify` (que comprueba que las pruebas de integración han pasado).

La secuencia anterior de fases forma el denominado _ciclo de vida por defecto_ de Maven. Pero Maven define otros dos _ciclos de vida_, con una secuencia de fases diferente. Por un lado, tenemos el ciclo de vida _clean_, que se centra en la eliminación de los artfectos generados en el directorio de trabajo del proyecto (no en los repositorios), con fases:

1. pre-clean
2. clean
3. post-clean

Por otro lado, existe un ciclo de vida _site_ para la generación de la documentación asociada al proyecto, con ciclos de vida:

1. pre-site
2. site
3. post-site
4. site-deploy

Se puede encontrar más información sobre los ciclos de vida en Maven [aquí](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html).

## Propiedades en Maven

## Proyectos con múltiples módulos

## Arquetipos

