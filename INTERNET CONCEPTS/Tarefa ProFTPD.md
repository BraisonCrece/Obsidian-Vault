# 1. <span style='color:#8854d0'>Instalar un servidor ProFTPD en una máquina</span>:

   a. Actualizamos lista de paquetes do repositorio apt-get do sistema:
   ```bash
   sudo apt-get update
   ```
   b. Instalamos ProFTPD:
   ```shell
   sudo apt-get install proftpd
   ```


# 2. <span style='color:#8854d0'>Configuración básica del servicio y conexión mediante Filezilla</span>:  
   a. Primiero imos facer unha copia de seguridade do arquivo orixinal por defecto, co seguinte comando:
```shell
   sudo cp proftpd.conf proftpd_origin.conf
```
   
   b. Editamos o arquivo de configuración de ProFTPD:
   ```shell
   sudo nano /etc/proftpd/proftpd.conf
   ```
   
   b. Definimos o nome do servidor, a ruta por defecto que vai servir:
   ```
   ServerName                      "My Server"
   DefaultRoot                     ~
   ```
   
   c. Gardamos o arquivo e reiniciamos o servidor:
   ```
   sudo systemctl restart proftpd
   ```
   
   d. Conectámonos mediante Filezilla, cos datos dun usuario local:
![[Pasted image 20230502153655.png]]

# 3. <span style='color:#8854d0'>Verifica el acceso al sistema de archivos y modifica la configuración para restringir el acceso</span>:

Teño acceso ao sistema de arquivos que lle definin como punto de entrada, neste caso ~, ou sexa a /.

Si se quere restrinxir o acceso a un cartafol determinado p
   ```
   DefaultRoot                     ~/ftp
   ```
   Reinicia el servicio ProFTPD:
   ```
   sudo systemctl restart proftpd
   ```

# 4. <span style='color:#8854d0'>Configurar un directorio de usuario anónimo con permisos de lectura y subdirectorio para subir archivos</span>:

   a. Editamos o arquivo de configuración:
   ```
   sudo nano /etc/proftpd/proftpd.conf
   ```
   
   b. Agregamos a seguinte configuracion ao arquivo:
   ```xml
   <Anonymous /srv/ftp>
     User                            ftp
     Group                           nogroup
     UserAlias                       anonymous ftp
     <Directory *>
       <Limit WRITE>
         DenyAll
       </Limit>
     </Directory>
     <Directory uploads>
       <Limit STOR>
         AllowAll
       </Limit>
     </Directory>
   </Anonymous>
   ```
   
   c. Gardamos o arquivo e reiniciamos o servidor:
   ```
   sudo systemctl restart proftpd
   ```
   
   d. Creamos o directorio "uploads" en /srv/ftp:
   ```
   sudo mkdir /srv/ftp/uploads
   ```
   
   e. Establecemos os permisos para o directorio "uploads":
   ```
   sudo chown ftp:nogroup /srv/ftp/uploads
   sudo chmod 755 /srv/ftp/uploads
   ```

![[Pasted image 20230502160522.png]]

![[Pasted image 20230502160632.png]]











