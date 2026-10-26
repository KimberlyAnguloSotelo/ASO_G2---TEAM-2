ASO - GRUPO G2 - TEAM 2
Despliegue LAMP, Samba, FTP y PostgreSQL con roles de Ansible (openSUSE 16.0)

=====================================================================
a) Link to GitHub
=====================================================================
https://github.com/KimberlyAnguloSotelo/ASO_G2---TEAM-2
Rama principal: main
Repositorio compartido con: gcussi@yahoo.com

=====================================================================
b) Steps to run ansible project
=====================================================================

REQUISITOS
- Maquina master: openSUSE 16.0 con Ansible instalado.
- Maquina destino: openSUSE 16.0 con SSH activo y acceso como root.
- Las dos maquinas en la misma red.

PASO 1. Preparar el destino (en la maquina destino, como root)
   El destino debe poder resolver su propio nombre; si no, Samba
   tarda en responder.
   hostnamectl set-hostname destino
     
PASO 2. Acceso por clave SSH
   Asi el inventario no necesita usuario ni contrasena.
   En openSUSE 16 el login de root por contrasena viene desactivado,
   y ssh-copy-id lo necesita una vez para copiar la clave.

   2.1 En el destino, como root:
     zypper install -y openssh-server-config-rootlogin
     systemctl restart sshd

   2.2 En la master:
     ssh-keygen -t ed25519
     ssh-copy-id root@IP_DEL_DESTINO
     ssh root@IP_DEL_DESTINO hostname
   El ultimo comando debe entrar sin pedir contrasena.

PASO 3. Descargar el proyecto (en la master)
     git clone https://github.com/KimberlyAnguloSotelo/ASO_G2---TEAM-2.git
     cd ASO_G2---TEAM-2
     chmod -R go-w .
   Si la carpeta tiene permisos 777, Ansible ignora ansible.cfg.

PASO 4. Instalar las colecciones de Ansible (si no estan)
     ansible-galaxy collection install community.general ansible.posix

PASO 5. Poner la IP del destino en el inventario
     vim hosts
   Cambiar la IP en la linea:
     destino ansible_host=IP_DEL_DESTINO

PASO 6. Probar la conexion
     ansible cliente -m ping --ask-vault-pass
   Debe responder "pong".
   La contrasena del vault se entrega al docente por privado.

PASO 7. Ejecutar el despliegue
     ansible-playbook site.yml --ask-vault-pass
   Al final se muestra el tiempo de cada tarea y el tiempo total.
   Debe terminar con failed=0. Si se ejecuta otra vez, debe
   terminar con changed=0 (idempotencia).

=====================================================================
ESTRUCTURA DEL PROYECTO
=====================================================================
  ansible.cfg              Configuracion y mejoras de tiempo de respuesta
  hosts                    Inventario (sin usuario ni contrasena)
  site.yml                 Playbook principal (solo llama a los roles)
  group_vars/all/vars.yml  Variables que apuntan al vault
  group_vars/all/vault.yml Usuarios y contrasenas cifrados (ansible-vault)
  roles/apache             Apache, PHP 8, modulos y rendimiento
  roles/mariadb            MariaDB: configuracion, base de datos y usuario
  roles/phpmyadmin         phpMyAdmin
  roles/postgresql         PostgreSQL: libreria PHP, contrasena de postgres,
                           usuario, base, acceso remoto y cifrado SSL
  roles/samba              Usuario de sistema, usuario Samba, directorio
                           y recurso con create mask
  roles/ftp                Servidor vsftpd con modo pasivo

=====================================================================
VERIFICACION
=====================================================================
  Web PHP:     http://IP_DEL_DESTINO/index.php
  phpMyAdmin:  http://IP_DEL_DESTINO/phpMyAdmin/
  Samba:       smbclient //IP_DEL_DESTINO/compartido -U smbuser1 -c 'ls'
  FTP:         ftp IP_DEL_DESTINO   (con un usuario local del destino)
  PostgreSQL:  psql "host=IP_DEL_DESTINO dbname=aso_pg user=aso_pg_user sslmode=require"
