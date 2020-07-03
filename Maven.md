# Maven
## Usar MI repositorio en GitHub

El repositorio está en: https://github.com/daoc/maven-repository

En el `pom.xml` de su proyecto debe añadir, dentro de /project/repositories/:

```
<repository>
    <id>lib</id>
    <url>https://github.com/daoc/maven-repository/raw/master/</url>
</repository>
```
