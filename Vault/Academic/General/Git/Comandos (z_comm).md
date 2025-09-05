### Orden para push
```js
git pull origin Ag-dev01 (asegurate de tener los cambios)
git add .
git commit -m "msj"
git push origin Ag-dev01
mergear pull request desde github
```
### Pullear master
```js
git checkout master
git pull origin master
```

### Borrar la rama usada Y crear nueva
```js
. git branch -d <nombre-de-la-rama-para-borrar
. git push origin --delete <nombre-de-la-rama.borradosa>
```

### Crear nueva
```js
. git checkout -b Ag-01
. git push -u origin Ag-01
```

### Guardar en stash los cambios
```js
git stash push -u -m "Guardando todos los cambios de la branch Ag-2"
git stash list
git stash apply stash@{0} o git stash pop
```