# Maven
## Usar MI repositorio en GitHub

El repositorio está en: https://github.com/daoc/maven-repository

En el `pom.xml` del proyecto se debe añadir, dentro de /project/repositories/:

```
<repository>
    <id>lib</id>
    <url>https://github.com/daoc/maven-repository/raw/master/</url>
</repository>
```
Luego, abrir un terminal en el directorio raíz del proyecto y ejecutar: `mvn install`

Al terminar, el programa se instala en el repositorio maven local: `<user-dir>/.m2/repository/`

Si 	<groupId>A.B</groupId> y <artifactId>C</artifactId> se debe copiar desde el directorio A incluido
