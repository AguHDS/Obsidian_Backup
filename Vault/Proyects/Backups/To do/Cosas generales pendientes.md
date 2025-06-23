### Cosas pendientes para chequear

usar return res, y podemos decir el tipo de respuesta en la funcion es Response

-  Ajustar los index.ts para que cada index en su subcarpeta exporte las cosas y el index.ts en la carpeta princpal de cada cosa exporte todo. Arreglar los errores en todos los archivos **tomar cosas del front como ejemplo que ya hice**

-  Check case where user uploads more than 1 file and 1 file size is bigger than allowed uploadFilesMiddleware.ts y tambien obtain file size and limit number to use them in the error messages uploadFilesMIddlewaret.s

-  Chequear el tema de Lifting state en mis componentes (pasar estado al padre en comun de los hermanos si estos necesitan acceder a ese estado)

-  Cuando actualizo title en Profile no se actualiza fijarse bien en la base de datos también **Esto pasa?**

-  En getProfileController quiza necesite incluir el id de las secciones y files si los necesita. Pero si esto se hace en otro controller, esta bien no hacerlo (creo que se hacen en este controller igual)

-  Each user will be able to add an additional section (if their level allows it). For handling the persist data of each section (the title and description) i created a users_profile_sections table, it can be used in the future to do this. **<- Esto esta hecho?**

-  Crear error boundlary mirar curso react del bigotudo por la hora 9 en adelante Errorboundary. Tambien ver como con esto poder manejar el error en caso de que el backend esté caido para que la aplicación no se cuelge cuando visitas el perfil de alguien **ya hay un issue de esto

- usar un factory para evitar hacer inyeccion de dependencias manualmente de useCases
###  Poner al Sprint

-  En el login controler probar si funciona igual cambiando ...data a ...user, porque data tambien trae la password y no la estamos usando ahi

-  Ver que consultas son estaticas y dinamicas, si son estaticas ponerlas en archivos con extension sql, y si son dinamicas .ts, en la carpeta static o dynamic de queries (estaticas las que no cambian - ej: getUserById o getUserByName -) <- **Investigar si esto es necesario o redundante**

-  Cuando se registra un usuario, deberia agregarse a users_profile tambien automaticamente, y poner su foreign key **<- Esto realmente esta pasando?**

-  Arreglar tema de cors que no deja hacer nuevo refresh token cuando expira por primera vez el access token <- **Esto realmente esta pasando?**

-  En la tabla refresh_tokens cuando expira en expires_at una fila, no se borra. Se borra solo caundo el usuario se deslogea manualmente. Buscar una forma de que se borre sola la fila cuando expires_at llega a su tiempo de expiración. **<- Esto esta pasando?**

-  Las foreign keys deberian tener el mismo nombre que el id unico al que referencian. cambiar user_id en refresh tokens a fk_users_id. **<- Esto esta hecho?**

-  Cambiar nombre user_id(refresh_tokens) a fk_users_id (ya que es una clave foreanea)
	ALTER TABLE refresh_tokens 
	RENAME COLUMN user_id TO fk_users_id;
	al hacer esto tambien hay que editar en el codigo en el backend y renombrar user_id por fk_users_id para que funcione **<- Esto esta hecho?**

-  Create a table (user_backupsData) that will contian:  max space, space available, space used,         space shared, space needed

-  Create a table (user_files) that will contain: all the files that the user upload using links 

-  Describir el codigo de cada tabla de la DB cuando termine la base de datos, para en caso de tener que volver a crearla, ya tener el codigo para las tablas hecho

-  Websockets / aprender bien a usar esto e implementarlo en mi app

-  Recordar cambiar fecha de expliracion de los tokens.. access token 10min, refresh token 7 o 14 dias cuando este terminado

### Cosas random
Estudiar en updateProfileController como maneja sections array desde ahi y profileContent porque lo hice asi nomas y no entendi bien ver la funcion en la query como hace todo