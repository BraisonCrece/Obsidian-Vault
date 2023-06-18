## File Transfer Protocol -> Cliente - Servidor
- Capa de rede TCP/IP
- Portos 20 (datos), 21 (control)
- Acceso:
	- Anónimo (usuario anonymous) so pode descargar
	- Autorizado (user + password) poden facer de todo si estan autorizados

## FTPS o SFTP:
- conexión cifraga segura, pode ser:
	- SSL implícito
		- como conexiones HTTPS
		- puertos 989, 990
	- SSL explícito
		- puertos estandar, 20 y 21, pero con cifrado
		- usa AUTH SSL
	- TLS explícito
		- similar a SSL explícito pero usa AUTH TLS

## Flujo de comunicación (cifrado):
![[Pasted image 20230531103534.png]]

## VSFTPD
Ruta ao arquivo de conf:
`/etc/vsftpd.conf`

Configuración:
- <span style='color:#3867d6'>anonymous_enable </span>->Permite os accesos anónimos ao servidor. (anonymous)

- <span style='color:#3867d6'>local_enable</span> ->  permite loguearse aos usuarios coas credenciais dos usuarios locais da máquina do servidor.

- <span style='color:#3867d6'>ftpd_banner</span> -> Mensaxe de benvida ao servidor

### Control de acceso de usuarios:
- <span style='color:#3867d6'>userlist_file</span> -> ruta do arquivo coa lista de usuarios que se permiten ou non accedes, dependendo de outras configuracións `/etc/nome_do_ficheiro`
- <span style='color:#3867d6'>userlist_enable</span> -> Permite o uso da userlist para control de acceso
- <span style='color:#3867d6'>userlist_deny</span> -> Se userlist_enable está activo e userlist_file tamén, indica se os usuarios da lista serán denegados ou non

### Permisos de escritura
- <span style='color:#3867d6'>write_enable</span> -> establece si se permite a escritura ou non
- <span style='color:#3867d6'>anon_upload_enable</span> -> especifica se os usuarios anónimos poden ou non subir arquivos
- <span style='color:#3867d6'>anon_mkdir_write_enable</span> -> o mesmo pero para crear cartafois
- <span style='color:#3867d6'>anon_root</span> -> especifica cal e o directorio do root para o usuario anonimo (por defecto /srv/ftp)

### Opcións chroot
chroot = ENGAIOLAR aos usuarios no seu directorio home
- <span style='color:#3867d6'>chroot_local_user</span> -> engaiola aos usuarios no seu directorio persoal (YES / NO)
- <span style='color:#3867d6'>chroot_list_enable</span> -> Habilita a lista de usuarios que son excluídos de ser engaiolados (YES / NO)
- <span style='color:#3867d6'>chroot_list_file</span> -> Indica o ficheiro coa lista de usuarios que son excluídos de ser engaiolados

### Denegar acceso a "emails"
- <span style='color:#3867d6'>deny_email_enable</span>=<span style='color:#8854d0'>YES</span>
- <span style='color:#3867d6'>banned_email_file</span>=<span style='color:#8854d0'>/etc/vsftpd.banned_emails</span>  (aquí metemos os "emails" que queremos banear)

### Impedir que todos los usuarios, salvo pedro y ana, tenga autorizado el acceso
- <span style='color:#3867d6'>userlist_enable</span>=<span style='color:#8854d0'>YES</span>
- <span style='color:#3867d6'>userlist_file</span>=<span style='color:#8854d0'>/etc/vsftpd.user_list</span>
- <span style='color:#3867d6'>userlist_deny</span>=<span style='color:#8854d0'>NO</span>

### Permitir el acceso a todos los usuarios salvo a root y a administrador
- <span style='color:#3867d6'>userlist_enable</span>=<span style='color:#8854d0'>YES</span>
- <span style='color:#3867d6'>userlist_file</span>=<span style='color:#8854d0'>/etc/vsftpd.user_list_prohibido</span>
- <span style='color:#3867d6'>userlist_deny</span>=<span style='color:#8854d0'>YES</span>

### Activar el acceso anónimo con contraseña y con permisos de escritura (crear directorios, subir archivos, etc.), siendo el directorio /home/anon el predeterminado para dicho acceso. Las contraseñas permitidas son: fernando@yahoo.com y isabel@linux.org.
- <span style='color:#3867d6'>anon_root</span>=<span style='color:#8854d0'>/home/anon</span> (directorio home de anon)
- <span style='color:#3867d6'>anon_upload_enable</span>=<span style='color:#8854d0'>YES</span>
- <span style='color:#3867d6'>anon_mkdir_write_enable</span>=<span style='color:#8854d0'>YES</span>
- <span style='color:#3867d6'>email_password_file</span>=<span style='color:#8854d0'>/etc/vsftpd.email_passwords</span>
- <span style='color:#3867d6'>secure_email_list_enable</span>=<span style='color:#8854d0'>YES</span>

### Enjaular a todos los usuarios salvo a root y a administrador
- <span style='color:#3867d6'>chroot_local_user</span>=<span style='color:#8854d0'>YES</span>
- <span style='color:#3867d6'>chroot_list_users</span>=<span style='color:#8854d0'>YES</span>
- <span style='color:#3867d6'>chroot_list_file</span>=<span style='color:#8854d0'>/etc/vsftpd.chroot_list</span> (lista de usuarios no enjaulados)

### Configurar el servicio para que, por defecto, los archivos que creen los usuarios no tengan activados los permisos de escritura para nadie. Para los usuarios pedro y ana se hará una excepción: los archivos que creen tendrán permisos de lectura y escritura para ellos y su grupo y ninguno para el resto (others).
- <span style='color:#3867d6'>listen</span>=<span style='color:#8854d0'>NO</span>
- <span style='color:#3867d6'>local_enable</span>=<span style='color:#8854d0'>YES</span>
- <span style='color:#3867d6'>write_enable</span>=<span style='color:#8854d0'>NO</span>
- <span style='color:#3867d6'>listen_ipv6</span>=<span style='color:#8854d0'>YES</span>
- <span style='color:#3867d6'>local_umask</span>=<span style='color:#8854d0'>0117</span>
- <span style='color:#3867d6'>file_open_mode</span>=<span style='color:#8854d0'>0777</span>
- <span style='color:#3867d6'>user_config_dir</span>=<span style='color:#8854d0'>/etc/vsftpd</span>  (aqui dentro creamos los archivos de cada user, con el nombre del user, y las directivas personales)
```predo
# archivo de directivas para pedro

write_enable=YES
local_root=/var/datos
```

De este modo a los usuario pedro y ana y sólo a ellos, se le aplican las directivas incluidas en sus propios ficheros, que <span style='color:#eb3b5a'>sobreescribirán a la que puedan encontrarse en el fichero general</span>.

### Para un servidor FTP de música en el que para cada género haya un directorio, activar un mensaje personalizado que aparezca al entrar a cada directorio.
- <span style='color:#3867d6'>dirmessage_enable</span>=<span style='color:#8854d0'>YES</span>
- <span style='color:#3867d6'>message_file</span>=<span style='color:#8854d0'>/home/musica/mensage.message</span> (fichero para el directorio)
```message
# /home/musica/mensage.message
Bienvenidos a mi carpeta musical!
```

### Suponiendo que el servidor se encuentra en una red privada, activar el modo pasivo sabiendo que sólo podemos abrir los puertos 21, 4001, 4002 y 4003 de nuestro router NAT
- <span style='color:#3867d6'>pasv_enable</span>=<span style='color:#8854d0'>YES</span>
- <span style='color:#3867d6'>listen_port</span>=<span style='color:#8854d0'>4002</span>

### Conéctate a un servidor FTP desde la línea de comandos y realiza las siguientes operaciones: 
1. Cambiar a modo binario. 
	- `binary`
2. Crear un directorio llamado documentos. 
	- `mkdir documentos`
1. Subir un archivo al directorio anterior. 
	- `archivo send: archivo_remoto`
2. Listar el contenido de documentos.
	- `dir`
1. Renombrar el archivo subido como informe. 
	- `rename ola.txt adios.txt`
1. Borrar el archivo informe del servidor. 
	- `delete adios.txt`
1. Borrar el directorio documentos. 
	- `rmdir documentos`
1. Descargar un archivo cualquiera del servidor. 
	- `get ola.txt ola.txt`
1. Salir
	- `quit`



# provsftpd

directorio dos arquivos de configuracion:
`/etc/proftpd`
```bash
# This is a basic ProFTPD configuration file (rename it to 
# 'proftpd.conf' for actual use. It establishes a single server 
# and a single anonymous login. It assumes that you have a user/group 
# "nobody" and "ftp" for normal operation and anon. ServerName "ProFTPD Default Installation" ServerType standalone DefaultServer on UseIPv6 off 
# To cause every FTP user to be "jailed" (chrooted) into their home 
# directory, uncomment this line. DefaultRoot ~

ServerName           "ProFTPD Default Installation" 
ServerType           standalone 
DefaultServer        on 
UseIPv6              off 

# To cause every FTP user to be "jailed" (chrooted) into their home # directory, uncomment this line. 
DefaultRoot          ~
```

Directorio por defecto (osea root):
`/srv/ftp`

Si queremos habilitar a subida de ficheiros, debemos crear unha carpeta nese directiorio, cambiarlle o dono:
```bash
chown ftp:nogroup /srv/ftp/nova_carpeta
```

Cando temos eso, temos que definir o arquivo de configuración:
```bash
# A basic anonymous configuration, no upload directories. If you do not # want anonymous users, simply delete this <anonimous> entire section. 
<Anonymous ~ftp>
	User                ftp 
	Group               nogroup 
# We want clients to be able to login with "anonymous" as well as "ftp" 
	UserAlias           anonymous ftp
	RequireValidShell   off
	<Directory *>
	    <LIMIT WRITE>
	        Deny All
	    </LIMIT>
	</Directory>
	<Directory upload>
	    <LIMIT STOR,MKD>
	        Allow All
	    </LIMIT>
	</Directory>
</Anonymous>
```

