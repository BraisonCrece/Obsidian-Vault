## <span style='color:#3867d6'>Exercicio 1</span>
Configurar el servidor para que se permita el acceso como anónimo pero se deniegue utilizando como contraseña pedro@hotmail.com o ana@gmail.com.

No archivo vsftpd.conf que se atopa na ruta `/etc/vsftpd.conf`  :
```conf
	anonymous_enable=YES
	userlist_enable=YES
	userlist_deny=YES
	userlist_file=/etc/vsftpd.user_list
```

A continuación temos que crear o arquivo `/etc/vsftpd.user_list`
```user_list
	pedro@hotmail.com
	ana@gmail.com
```

## <span style='color:#3867d6'>Ejercicio 2</span> 
Impedir que todos los usuarios, salvo Pedro y Ana, tengan autorizado el acceso.

No arquivo `/etc/vsftpd.conf`:
```conf
	userlist_enable=YES
	userlist_deny=NO
	userlist_file=/etc/vsftpd.user_list
```

Arquivo `/etc/vsftpd.user_list`:
```user_list
	pedro@hotmail.com
	ana@gmail.com
```

## <span style='color:#3867d6'>Ejercicio 3 </span>
Permitir el acceso a todos los usuarios salvo a root y a administrador.

No arquivo `/etc/vsftpd.conf`:
```conf
	userlist_enable=YES
	userlist_deny=YES
	userlist_file=/etc/vsftpd.user_list
```

Arquivo `/etc/vsftpd.user_list`:
```user_list
	root
	administrador
```

## <span style='color:#3867d6'>Ejercicio 4 </span>
Activar el acceso anónimo con contraseña y con permisos de escritura (crear directorios, subir archivos, etc.), siendo el directorio /home/anon el predeterminado para dicho acceso. Las contraseñas permitidas son: fernando@yahoo.com y isabel@linux.org.

No arquivo `/etc/vsftpd.conf`:
```conf
	anonymous_enable=YES
	anon_upload_enable=YES
	anon_mkdir_write_enable=YES
	anon_root=/home/anon
	userlist_enable=YES
	userlist_deny=NO
	userlist_file=/etc/vsftpd.user_list
```
  
Arquivo `/etc/vsftpd.user_list`:
```conf
	fernando@yahoo.com
	isabel@linux.org
```

## <span style='color:#3867d6'>Ejercicio 5 </span>
Enjaular a todos los usuarios salvo a root y a administrador.

No arquivo `/etc/vsftpd.conf`:
```conf
	chroot_local_user=YES
	chroot_list_enable=YES
	chroot_list_file=/etc/vsftpd.chroot_list
```

Creamos arquivo `/etc/vsftpd.chroot_list`:
```chroot_list
	root
	administrador
```

## <span style='color:#3867d6'>Ejercicio 6 </span>
Configurar el servicio para que, por defecto, los archivos que creen los usuarios no tengan activados los permisos de escritura para nadie. Para los usuarios pedro y ana se hará una excepción: los archivos que creen tendrán permisos de lectura y escritura para ellos y su grupo y ninguno para el resto (others).

No arquivo `/etc/vsftpd.conf`:
```conf
	file_open_mode=644
```

Esta línea establece os permisos `rw-r--r--` (lectura e escritura para o propietario, e so lectura para o resto) para os novos arquivos creados.

Permitir escritura para os usuarios especificados (Pedro e Ana):
```conf
	local_umask=022
	userlist_file=/etc/vsftpd.allowed_users
	userlist_enable=YES
	userlist_deny=NO
```

A directiva `local_umask=022` establece unha máscara de permisos para os arquivos creados polos usuarios de `022`, o que significa que os arquivos terán permisos `rw-r--r--`, e dicir, o usuario e o seu grupo terán permisos de lectura e escritura e os demáis so terán permiso de lectura.

A lista de usuarios permitidos almacenarase no archivo `/etc/vsftpd.allowed_users`.
```allowed_users
	pedro
	ana
```

## <span style='color:#3867d6'>Ejercicio 7 </span>
Para un servidor FTP de música en el que para cada género haya un directorio, activar un mensaje personalizado que aparezca al entrar a cada directorio.

En cada un dos directorios correspondentes a cada xénero, creamos un arquivo mensaxe.txt coa mensaxe que queiramos mostrar.  

No arquivo `/etc/vsftpd.conf`:
```conf
	dirmessage_enable=YES
```

## <span style='color:#3867d6'>Ejercicio 8</span>
Suponiendo que el servidor se encuentra en una red privada, activar el modo pasivo sabiendo que sólo podemos abrir los puertos 21, 4001, 4002 y 4003 de nuestro router NAT.

No arquivo `/etc/vsftpd.conf`:
```conf
	pasv_enable=YES
	pasv_min_port=4001
	pasv_max_port=4003
	pasv_address=<dirección IP pública do servidor>
```

## <span style='color:#3867d6'>Ejercicio 9</span>
Conéctate a un servidor FTP desde la línea de comandos y realiza las siguientes
operaciones:

Conectarse:
```bash
	ftp <dirección IP ou nome de dominio>
```

1. Cambiar a modo binario.
```bash
	binary
```

2. Crear un directorio llamado documentos.
```bash
	mkdir documentos
```

3. Subir un archivo al directorio anterior.
```bash
	cd documentos
	put <nome do arquivo>
```

4. Listar el contenido de documentos.
```bash
	ls documentos
```

5. Renombrar el archivo subido como informe.
```bash
	rename <nome do arquivo subido> informe
```

6. Borrar el archivo informe del servidor.
```bash
	delete informe
```

7. Borrar el directorio documentos.
```bash
	rmdir documentos
```

8. Descargar un archivo cualquiera del servidor.
```bash
	get <nome do arquivo>
```

9. Salir.
```bash
	bye
```
