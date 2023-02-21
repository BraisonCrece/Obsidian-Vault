## Comandos para trabajo remoto

```bash
git clone url_del_servidor_remoto 
```
--> Nos permite descargar los archivos de la última versión de la rama principal y todo el historial de cambios en la carpeta `.git`.

```bash
git push 
```
--> Luego de hacer `git add` y `git commit` debemos ejecutar este comando para mandar los cambios al servidor remoto.

```bash
git fetch
```
--> Lo usamos para traer actualizaciones del servidor remoto y guardarlas en nuestro repositorio local (en caso de que hayan, por supuesto).

```bash
git merge 
```
--> También usamos el comando `git merge` con servidores remotos. Lo necesitamos para combinar los últimos cambios del servidor remoto y nuestro directorio de trabajo.

```bash
git pull
```
--> Básicamente, `git fetch` y `git merge` al mismo tiempo.


### Adicionalmente, tenemos otros comandos que nos sirven para trabajar en proyectos muy grandes:

```bash
git log --oneline
```
--> Te muestra el id commit y el título del commit.

```bash
git log --decorate
```
--> Te muestra donde se encuentra el head point en el log.

```bash
git log --stat
```
--> Explica el número de líneas que se cambiaron brevemente.

```bash
git log -p
```
--> Explica el número de líneas que se cambiaron y te muestra que se cambió en el contenido.

```bash
	git shortlog
```
--> Indica que commits ha realizado un usuario, mostrando el usuario y el título de sus commits.

```bash
git log --graph --oneline --decorate 
git log --pretty=format:"%cn hizo un commit %h el dia %cd"
```
--> Muestra mensajes personalizados de los commits.

```bash
	git log -3
```
--> Limitamos el número de commits.

```bash
	git log --after=“2018-1-2”
	git log --after=“today”
	git log --after=“2018-1-2” --before=“today”
```
--> Commits para localizar por fechas.

```bash
	git log --author=“Name Author” 
```
--> Commits hechos por autor que cumplan exactamente con el nombre.

```bash
	git log --grep=“INVIE”
```
--> Busca los commits que cumplan tal cual está escrito entre las comillas.

```bash
	git log --grep=“INVIE” –i
```
--> Busca los commits que cumplan sin importar mayúsculas o minúsculas.

```bash
	git log – index.html
```
--> Busca los commits en un archivo en específico.

```bash
	git log -S “Por contenido” 
```
--> Buscar los commits con el contenido dentro del archivo.

```bash
	git log > log.txt
```
--> Guardar los logs en un archivo txt

```bash
	git log --oneline 
```
--> Te muestra el id commit y el título del commit.

```bash
	git log --decorate
```
--> Te muestra donde se encuentra el head point en el log.

```bash
	git log --stat 
```
--> Explica el número de líneas que se cambiaron brevemente.

```bash
	git log -p
```
--> Explica el número de líneas que se cambiaron y te muestra que se cambió en el contenido.

```bash
	git shortlog
``` 
--> Indica que commits ha realizado un usuario, mostrando el usuario y el titulo de sus commits.


## Cómo funcionan las ramas en GIT

Las ramas son la manera de hacer cambios en nuestro proyecto sin afectar el flujo de trabajo de la rama principal. Esto porque queremos trabajar una parte muy específica de la aplicación o simplemente experimentar.

```bash
	git branch -nombre de la rama-
```
--> Con este comando se genera una nueva rama.
   
```bash
	git checkout -nombre de la rama- 
```
--> Con este comando puedes saltar de una rama a otra.

```bash
	git checkout -b rama
```
--> Genera una rama y nos mueve a ella automáticamente, Es decir, es la combinación de git brach y git checkout al mismo tiempo.

```bash
	git reset id-commit
```
--> Nos lleva a cualquier commit no importa la rama, ya que identificamos el id del tag., eliminando el historial de los commit posteriores al tag seleccionado.


```bash
	git checkout rama-o-id-commit
```
--> Nos lleva a cualquier commit sin borrar los commit posteriores al tag seleccionado.

## Resolución de conflictos al hacer un merge

**Git nunca borra nada**, a menos que nosotros se lo indiquemos. Cuando usamos los comandos `git merge` o `git checkout` estamos cambiando de rama o creando un nuevo _commit_, no borrando ramas ni _commits_ (recuerda que puedes borrar commits con `git reset` y ramas con `git branch -d`).

Git es muy inteligente y puede resolver algunos conflictos automáticamente: cambios, nuevas líneas, entre otros. Pero algunas veces no sabe cómo resolver estas diferencias, por ejemplo, cuando dos ramas diferentes hacen cambios distintos a una misma línea.

Esto lo conocemos como **conflicto** y lo podemos resolver manualmente. Solo debemos hacer el _merge_, ir a nuestro editor de código y elegir si queremos quedarnos con alguna de estas dos versiones o algo diferente. Algunos editores de código como Visual Studio Code nos ayudan a resolver estos conflictos sin necesidad de borrar o escribir líneas de texto, basta con hacer clic en un botón y guardar el archivo.

Recuerda que siempre debemos crear un nuevo commit para aplicar los cambios del merge. Si Git puede resolver el conflicto, hará commit automáticamente. Pero, en caso de no pueda resolverlo, debemos solucionarlo y hacer el commit.

Los archivos con conflictos por el comando `git merge` entran en un nuevo estado que conocemos como **_Unmerged_**. Funcionan muy parecido a los archivos en estado _Unstaged_, algo así como un estado intermedio entre Untracked y Unstaged. Solo debemos ejecutar `git add` para pasarlos al área de staging y `git commit` para aplicar los cambios en el repositorio.

## Cómo revertir un merge

Si nos hemos equivocado y queremos cancelar el merge, debemos usar el siguiente comando:

```bash
git merge --abort
```

## Conflictos en repositorios remotos

Al trabajar con otras personas, es necesario utilizar un repositorio remoto.  

­ Para copiar el repositorio remoto al directorio de trabajo local, se utiliza el comando `git clone <url>`, y para enviar cambios al repositorio remoto se utiliza `git push`.  
­ Para actualizar el repositorio local se hace uso del comando `git fetch`, luego se debe fusionar los datos traídos con los locales usando `git merge`.

­ Para traer los datos y fusionarlos a la vez, en un solo comando, se usa `git pull`.  
­ Para crear commits rápidamente, fusionando `git add` y `git commit -m ""`, usamos `git commit -am ""`.  
­ Para generar nuevas ramas, hay que posicionarse sobre la rama que se desea copiar y utilizar el comando `git branch <nombre>`.
­ Para saltar entre ramas, se usa el comando `git checkout <branch>`  
­ Una vez realizado los cambios en la rama, estas deben fusionarse con `git merge`.
­ El merge ocurre en la rama en la que se está posicionado. Por lo tanto, la rama a fusionar se transforma en la principal.
­ Los merges también son commits.
­ Los merges pueden generar conflictos, esto aborta la acción y pide que soluciones el problema manualmente, aceptando o rechazando los cambios que vienen.