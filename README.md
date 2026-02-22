# Tutorial de Apache Maven

En este documento se presenta un breve tutorial pensado para introducir Maven en la asignatura de Desarrollo de Aplicaciones Web, de tercero del Grado en Ingeniería Informática y cuarto del Doble Grado en Ingeniería Informática y Matemáticas de la Universidad de Málaga (España). Para obtener más información sobre Maven y recursos adicionales se puede consultar la [página oficial de Apache Maven](https://maven.apache.org/index.html). Para hacer los ejemplos que se muestran abajo se recomienda descargar un ejemplo simple (que se construya rápido). Un ejemplo de proyecto puede ser [este](https://github.com/jfrchicanog/jpa-demo). Otro proyecto con múltples módulos es [este](https://github.com/jfrchicanog/AgendaWeb).

## ¿Qué es Maven?

Es una herramienta y un sistema de distribución de paquetes que facilita la construcción de aplicaciones y bibliotecas en Java. Se parece a herramientas como `make` o el paquete `autoconf`. En el mundo Java es similar a herramientas como `ant` (que es anterior) y `gradle` (que es posterior).

## ¿Qué ventajas tiene Maven?

Algunas de las ventajas de Maven para la construcción de código Java son:
* __Identificación de paquetes__: todos los paquetes tienen unas coordenadas que permiten identificarlo de forma unívoca, lo que facilita la identificación de paquetes y versiones.
* __Distribución de paquetes__: los repositorios de Maven permiten almacenar paquetes de forma que no sea necesario descargar de una URL los paquetes de forma manual, sino que Maven detecta las dependencias y los descarga de forma automática.
* __Gestión de dependencias__: permite la declaración de dependencias de los proyectos y se encarga de resolver dichas dependencias descargando los paquetes necesarios.
* __Automatización de procesos de construcción complejos__: es posible ejecutar un complejo flujo de construcción usando un simple comando.
* **Independencia de los IDEs**: permite una construcción del software completo con independencia del entorno de desarrolo (IDE) que usemos. Los principales IDEs tienen soporte para Maven y es posible realizar la construcción sin ellos. Esto es especialmente útil en un equipo de desarrollo donde cada persona tiene un IDE diferente.
* __Ejecución de pruebas unitarias y de integración y reporte de resultados__: distingue entre pruebas unitarias, que se ejecutan sobre clases particulares tras la compilación y antes del empaquetar el software, y pruebas de integración, que se ejecutan una vez que el software está empaquetado. Permite reportar el resultado tras la ejecución de las pruebas y puede incluirse en herramientas de control de calidad como SonarQube.
* __Construcción de proyectos con múltiples módulos__: se pueden definir módulos dentro de un proyecto para software más complejo e idenfica las dependencias entre módulos para realizar al construcción en el orden correcto.

## Conceptos básicos

En esta sección explicaremos algunos conceptos básicos de Maven, necesarios para comenzar a trabajar con él.

### Coordenadas y dependencias

Una de las grandes ventajas de Maven es que se pueden especificar de forma declarativa las dependencias entre distintas bibliotecas (paquetes). Antes de la existencia de este tipo de herramientas, las bibliotecas debían descargarse manualmente (normalmente un fichero JAR) y colocarlas en una carpeta asociada al proyecto para incluirlas en el _classpath_ durante la construcción y la ejecución. Si la biblioteca descargada tenía otras dependencias, estas también debían descargarse, y así sucesivamente hasta resolver todas las dependencias. Con Maven basta indicar de qué biblioteca depende nuestro proyecto y la herramienta se encargará de buscar la dependencia (descargarla si es necesario), así como todas sus dependencias, y ubicarla en el _classpath_ para la construcción del software.

Para poder especificar las dependencias de forma declarativa, es necesario que cada paquete en Maven esté identificado. Para eso se usan las _coordenadas Maven_:
* __groupId__: es un identificador que normalmente identifica la organización o el grupo de desarrollo.
* __artifactId__: identifica el paquete dentro del grupo de desarrollo (su nombre).
* __version__: es la versión del artefacto. Se recomienda seguir el estándar de [Versionado semántico 1.0.0](https://semver.org/spec/v1.0.0.html).

La terna anterior identifica de forma unívoca un paquete concreto (un fichero JAR) que puede contener una biblioteca, una aplicación, recursos, etc. De esta forma es posible indicar claramente en un proyecto qué dependencias tenemos. Es habitual usar la notación `groupId:artifcatId:version` para identificar de forma concisa un paquete Maven.

Es posible indicar a Maven que el artefacto está aún en desarrollo (no es definitivo) añadiendo el sufijo `-SNAPSHOT` a la versión. Maven da un tratamiento especial a estos artefactos. En particular, siempre consulta el repositorio remoto cuando debe resolver las dependencias, aunque haya una versión en el repositorio local, porque al estar en desarrollo el artefacto podría haber cambiado.

A la hora de especificar las dependencias de un proyecto es posible indicar su _ámbito_ (_scope_). Este ámbito determina cuándo será necesario añadir el paquete al _classpath_ y si dicha dependencia hay que trasladarla a otros paquetes que dependan del actual. El ámbito puede tomar los siguientes valores:
* **compile** (valor por defecto). Indica que la dependencia debe estar disponible en cualquier classpath (compilación, pruebas y ejecución) y que se debe incluir también en el classpath de los proyectos dependientes.
* **runtime**. Indica que la dependencia debe estar disponible en tiempo de ejecución y pruebas pero no es necesaria para la compilación.  
* **test**. Indica que la dependencia solo es necesaria durante las pruebas, por lo que se añadirá a la fase de compilación de casos de prueba y su ejecución. 
* **provided**. Indica que la dependencia se espera que esté presente en el entorno de ejecución (típicamente un contenedor de un servidor de aplicaciones). Se encontrará en el classpath de compilación y pruebas.
* **system**. Indica que la dependencia se encuentra en el sistema de ficheros y Maven no debe buscarlo en un repositorio (debe proporcionarse el JAR en este caso usando el elemento `systemPath`). Este ámbito está obsoleto y debe evitarse.
* **import**. Usado para importar todas las dependencias de proyectos multi-módulo (hablaremos de estos proyectos más abajo).

### Repositorios

Los paquetes, una vez construidos pueden publicarse en repositorios de paquetes. En todo sistema que use Maven existe un _repositorio local_, asociado al usuario que ejecuta Maven, y que normalmente se encuentra en el directorio `.m2` dentro del directorio de trabajo del usuario. Este repositorio local actúa como una caché para almacenar los paquetes descargados por Maven de otros repositorios remotos. También se puede utilizar para instalar paquetes que estamos construyendo por si los necesitan otros proyectos (usando el comando `mvn install`). 

Si Maven no encuentra un paquete en el repositorio local, busca en repositorios remotos. Estos repositorios pueden configurarse en un fichero llamado `settings.xml` ubicado en el directorio `.m2`. Si no configuramos ningún repositorio remoto, Maven usará el repositorio [Maven Central](https://search.maven.org).

### Project Object Model (pom)

El _project object model_ es el elemento fundamental para la especificación y configuración de la construcción de un proyecto. Se trata de un fichero llamado normalmente `pom.xml` donde se especifica (en formato XML) las dependencias del proyecto, sus coordenadas y todas las instrucciones necesarias para la construcción del software. Cuando un proyecto se construye con Maven y contiene este fichero `pom.xml` decimos que es un _proyecto Maven_.

Vamos a ver un ejemplo de fichero `pom.xml` sencillo a continuación:

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>es.uma.informatica.daw</groupId>
    <artifactId>Persistencia</artifactId>
    <version>1.0.1</version>
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

Se puede ver cómo se especifican las dependencias del proyecto dentro del elemento `<dependencies>` del fichero XML. También se especifican aspectos como la versión Java usada para el código fuente y el código objeto, la codificación de caracteres que se debe asumir en el proyecto y el tipo de empaquetado que debe usarse (archivo JAR en este caso).

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
12. process-test-resources
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
se ejecutarán todas las fases anteriores hasta llegar a la fase `package`. El resultado será un paquete con el software. Este comando debe escribirse en el directorio donde se encuentra el fichero `pom.xml`del proyecto. Alternativamente, puede indicarse la ubicación del fichero `pom.xml` con la opción `-f`. Por ejemplo:

```
mvn -f proyecto/pom.xml package
```

Esto también se aplica cuando el fichero POM no tiene el nombre estándar (`pom.xml`):

```
mvn -f mi-otro-pom.xml package
```

Si escribimos:

```
mvn verify
```
se ejecutarán todas las fases hasta `verify` (que comprueba que las pruebas de integración han pasado).

La secuencia anterior de fases forma el denominado _ciclo de vida por defecto_ de Maven. Pero Maven define otros dos _ciclos de vida_, con una secuencia de fases diferente. Por un lado, tenemos el ciclo de vida _clean_, que se centra en la eliminación de los artefactos generados en el directorio de trabajo del proyecto (no en los repositorios), con fases:

1. pre-clean
2. clean
3. post-clean

Por otro lado, existe un ciclo de vida _site_ para la generación de la documentación asociada al proyecto, con ciclos de vida:

1. pre-site
2. site
3. post-site
4. site-deploy

Se puede encontrar más información sobre los ciclos de vida en Maven [aquí](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html).

Asociadas a cada una de las fases del ciclo de vida hay una serie de acciones que se ejecutan que se denominan _objetivos_ (_goals_) en la terminología Maven. Estos objetivos están implementados en _plugins_ de Maven, que son, a su vez, paquetes de Maven que permiten extender la funcionalidad de Maven. Por ejemplo, la compilación del código Java se lleva a cabo en la fase `compile` y la realiza el [plugin _compiler_](https://maven.apache.org/plugins/maven-compiler-plugin/) de Apache Maven. En particular, en dicha fase se ejecuta el objetivo denominado `compile`. Otro objetivo definido por el mismo plugin es `test-compile` que se encarga de compilar el código fuente de las pruebas de código. 

El nombre completo de un objetivo viene determinado por el plugin que lo implementa y el nombre del objetivo. Por ejemplo, `compiler:compile` hace referencia al objetivo que compila el código fuente del plugin `compiler`. Es posible ejecutar un objetivo específico de un plugin escribiendo el nombre completo del objetivo tras el comando `mvn`. Por ejemplo, si ecribimos:

```
mvn compiler:compile
```
se ejecutará solo la compilación del código fuente del proyecto y no las fases previas del ciclo de vida (como la generación de código fuente).

No obstante, hay que advertir que `compiler` es, a su vez, una abreviatura del nombre completo del plugin compilador de Maven. Los plugins, al ser paquetes Maven, tienen también coordenadas. Las coordenadas de la versión más reciente del plugin `compiler` son: `org.apache.maven.plugins:maven-compiler-plugin:3.15.0` (publicada el 27 de enero de 2026).

Existe una asociación por defecto entre fases y objetivos de plugins en Maven que depende del tipo de empaquetado que usemos en nuestro proyecto (jar, war, ear, etc). Esta asociación puede consultarse [aquí](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#built-in-lifecycle-bindings). Además de la asociación por defecto, siempre es posible configurar nuestro proyecto (editando el fichero `pom.xml`) para asociar algún objetivo a una de las fases del ciclo de vida que no esté configurada por defecto. En el ejemplo siguiente se configura el plugin `properties-maven-plugin` de Codehaus para que cree un fichero llamado `pom.properties` en el directorio de salida conteniendo los pares clave/valor definidos en las propiedades del proyecto. Podemos ver que se asocia de forma explícita el objetivo `write-project-properties` a la fase `generate-resources` del ciclo de vida por defecto de Maven.

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>es.uma.informatica.daw</groupId>
    <artifactId>Persistencia</artifactId>
    <version>1.0.1</version>
    <packaging>jar</packaging>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
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
        </dependency>
        <dependency>
            <groupId>org.apache.derby</groupId>
            <artifactId>derbyclient</artifactId>
            <version>10.13.1.1</version>
        </dependency>
    </dependencies>
    <build>
    	<plugins>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>properties-maven-plugin</artifactId>
                <version>1.0.0</version>
                <executions>
                    <execution>
                        <phase>generate-resources</phase>
                        <goals>
                            <goal>write-project-properties</goal>
                        </goals>
                        <configuration>
                            <outputFile>${project.build.outputDirectory}/pom.properties</outputFile>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

Podemos combinar la ejecución de fases y objetivos en Maven indicando dichas fases y objetivos como argumentos del comando `mvn`. Por ejemplo:

```
mvn clean package
```
ejecutará las fases `pre-clean` y `clean` del ciclo de vida `clean` y luego todas las fases hasta `package`del ciclo de vida por defecto. 

Para más información sobre los plugins de Maven se puede visitar [esta página](https://maven.apache.org/plugins/index.html).

### Estructura de un proyecto Maven

Maven asume una determinada estructura de directorios para el proyecto. En particular, el fichero `pom.xml` asume que se encuentra en el directorio raíz del proyecto, junto con la licencia y fichero `readme`. Todo el código fuente debe encontrarse bajo el subdirectorio `src` y todo el código objeto generado así como ficheros empaquetados lo almacenará bajo el directorio `target` (este directorio es eliminado al hacer `mvn clean`).

Debajo de `src` encontraremos el subdirectorio `main` para el código y recursos principales de la aplicación y el directorio `test` para el código y recursos de pruebas. Dentro de cada uno de estos directorios encontramos, a su vez, los subdirectorios `java` y `resources` que contienen el código fuente Java y los recursos (ficheros de texto, ficheros de propiedades, imágenes) que use el código fuente. Cuando estamos desarrollando una aplicación Web, el subdirectorio `webapp` dentro de `src/main` se usa para contener las páginas HTML, JSP, y facelets de la aplicación, así como los recursos CSS y JS de la misma.

Para más información sobre la estructura de un proyecto Maven se puede visitar [este enlace](https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html).

## Propiedades en Maven

El fichero `pom.xml` tiene una sección donde se pueden especificar propiedades que luego pueden usarse en el resto del documento o dentro de plugins. Por ejemplo, la propiedad `maven.compiler.source` que ha aparecido en los ejemplos anteriores es usada por el `compiler` plugin para determinar la versión de Java en el código fuente. Podríamos usar el valor de dicha propiedad en otra parte del documento `pom.xml` simplemente escribiendo `${maven.compiler.source}` allá donde la necesitemos.

Es posible acceder también a cualquier elemento del fichero `pom.xml` como si fuera una propiedad escribiendo `${project.x}` donde `x` es la ruta (con elementos separados por punto) al elemento que queremos usar. Por ejemplo, `${project.version}` es sustituido por la versión del artefacto que estamos definiendo.

También es posible acceder a propiedades especificadas en línea de comandos con la opción `-D`. Sea el siguiente fichero `pom.xml` (incompleto).

```
<project>
...
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-ejb-plugin</artifactId>
            <configuration>
                <ejbVersion>3.2</ejbVersion>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <configuration>
                <skip>${skip.ejb.tests}</skip>
            </configuration>
        </plugin>
    </plugins>
</build>
</project>
```

En dicho fichero se usa una propiedad llamada `skip.ejb.tests` que no está definida en la sección de propiedades del documento. Cuando dicha propiedad sea cierta, Maven se saltará la fase de prueba del proyecto (tanto unitarias como de integración). Podemos hacer cierta dicha propiedad simplemente añadiéndola a la línea de comando cuando ejecutamos Maven:

```
mvn package -Dskip.ejb.tests
```

El comando anterior creará un paquete con el proyecto pero se saltará la fase de pruebas unitarias (a la fase de pruebas de integración no llega).

## Proyectos con múltiples módulos

Es posible estructurar un proyecto de Maven como una jerarquía de módulos. Cada módulo se presenta en un subdirectorio diferente y tiene su propio fichero `pom.xml` y cada módulo tiene sus propias coordenadas. Podemos pensar en un proyecto multi-módulo como una colección de proyectos relacionados que pueden procesarse a la vez. El siguiente fichero define un proyecto Maven con cuatro módulos:

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>es.uma.informatica.daw</groupId>
  <artifactId>AgendaWeb</artifactId>
  <version>1.0</version>
  <packaging>pom</packaging>
  <name>AgendaWeb</name>
  <description>Código base para la práctica 6 de Sistemas de Información para Internet</description>
  <scm>
  	<url>https://github.com/jfrchicanog/AgendaWeb.git</url>
  	<developerConnection>Francisco Chicano</developerConnection>
  </scm>
  <modules>
  	<module>AgendaWeb-ejb</module>
  	<module>AgendaWeb-war</module>
  	<module>AgendaWeb-jpa</module>
  	<module>AgendaWeb-ear</module>
  </modules>
  <dependencyManagement>
  	<dependencies>
  		<dependency>
  			<groupId>javax</groupId>
  			<artifactId>javaee-api</artifactId>
  			<version>8.0</version>
  		</dependency>
  	</dependencies>
  </dependencyManagement>
</project>
```

En el `pom.xml` del proyecto multi-módulo es posible definir propiedades y configuraciones que afecten a todos los módulos. Si miramos el fichero `pom.xml` de uno de los módulos (`AgendaWeb-jpa`) encontramos lo siguiente:
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>es.uma.informatica.daw</groupId>
    <artifactId>AgendaWeb</artifactId>
    <version>1.0</version>
  </parent>
  <artifactId>AgendaWeb-jpa</artifactId>
  <name>JPA</name>
  <description>Entidades JPA de la aplicación</description>
  <scm>
  	<url>https://github.com/jfrchicanog/AgendaWeb.git</url>
  </scm>
  <properties>
  	<maven.compiler.source>1.8</maven.compiler.source>
  	<maven.compiler.target>1.8</maven.compiler.target>
  </properties>
  <dependencies>
  	<dependency>
  		<groupId>javax</groupId>
  		<artifactId>javaee-api</artifactId>
  		<scope>provided</scope>
  	</dependency>
  </dependencies>
</project>
```

Observamos que con el elemento `parent` es posible definir el proyecto padre de este módulo. También observamos que no es necesario especificar la versión de la dependencia a `javaee-api` ya que el proyecto padre la ha definido dentro del elemento `dependencyManagement`. 

Es posible que un módulo del proyecto dependa de otro (necesita que el otro se construya antes). Maven detecta estas dependencias y las resuelve, permitiendo que el orden en que se presentan los módulos en el proyecto padre sea irrelevante. Maven siempre construirá los módulos en un orden que resuelva todas las dependencias.

## Arquetipos

Los arquetipos son plantillas de proyectos Maven que permiten construir un primer proyecto con una estructura inicial (incluyendo código y recursos). Son útiles para no tener que partir de una carpeta vacía. Los arquetipos también tienen coordenadas Maven y se distribuyen en repositorios. Para usar un arquetipo tenemos que usar el objetivo `archetype:generate`. Por ejemplo, para construir un pequeño proyecto Maven con una clase Java y una clase de prueba podemos usar el arquetipo `maven-archetype-simple` invocándolo de la siguiente forma:

```
mvn archetype:generate \
 -DarchetypeGroupId=org.apache.maven.archetypes \
 -DarchetypeArtifactId=maven-archetype-simple \
 -DarchetypeVersion=1.4
```

Para más información sobre arquetipos puede visitar [este enlace](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). Para aprender a crear sus propios arquetipos puede visitar [esta página](https://maven.apache.org/guides/mini/guide-creating-archetypes.html).
