# Git
Manejo de git, GitHub, etc

### Clonar un repositorio existente

`git clone https://<username>:<password>@github.com/<username>/<repo_name>.git`

### Actualizar el repositorio
Antes de iniciar el trabajo es necesario poner el repositorio local al día, con lo que otros puedieron haber modificado y guardado en el repositorio remoto

`git pull`

### Añadir archivos al repositorio (Stage files)
Antes de hacer Commit es necesario que los nuevos archivos incluidos en el proyecto sean añadidos al control de versión. El punto indica que todos los archivos nuevos serán incluidos. Se puedee poner una lista de nomrbes de archivos cuando se desea incluir solo ciertos archivos

`git add .`

### Commit
Es necesario registrar todos los cambios dentro del repositorio local (antes de pasarlos al remoto)

`git commit -m "<un mensaje cualquiera>"`

### Push
Registrar todos los cambios en el repositorio remoto

`git push <remote_name> <branch_name>`

**Generalmente:**

`git push origin master`

### Tags
Una tag identifica un commit específico. Para crear una tag sobre el último commit:

`git tag -a v1.0.0 -m "Mensaje explicatorio para la tag v1.0.0"`

Las tags deben ser cargadas o publicadas al repositorio de manera independiente, así que cuando hayamos creado nuevas tags es    necesario ejecutar:

`git push --tags`

Para listar las tags que tenemos junto con sus mensajes, utilizar:

`git tag -n`
