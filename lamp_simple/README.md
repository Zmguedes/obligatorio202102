**Cambios realizados para el obligatorio de Taller de Servidores Linux 2021**

Se nos proporcionó un repositorio para instalar un paquete LAMP, el cual requería correcciones ya que:
- Solo aplicaba para servidores RedHat.
- Tenía configuraciones obsoletas (iptables, MySQL, etc).
- Tenía variables configuradas directamente en los archivos main.yml de los directorios task y template.
- Intalaciones faltantes (SELinux).

Los cambios realizados fueron los siguientes:

1- Se agregaron los archivos ansible.cfg e inventario. En el archivo ansible.cfg se configuró la ruta del inventario, y en el archivo inventario se configuraron las IP de los servidores CentOS y Ubuntu.

2- COMMON
        
        A) Se agregó un directorio VARS, donde se colocó un archivo main.yml, en el cual se declaran los valores de:
                - ntp_driftfile
                - ntp_restrict_1
                - ntp_restrict_2
                - ntp_include_file
                - ntp_keys
                - selinux
        Cabe mencionar que la última variable contiene la lista de paquetes necesarios para el funcionamiento de SELinux en Ubuntu.
        
        B) En el archivo main.yml del directorio TASKS se agregó la instalación de ntp apropiada para Ubuntu, ya que usa apt envés de yum. También se agregó una tarea para instalar SELinux en Ubuntu, activarlo y configurarlo en modo Enforcing.
        C) En el archivo ntp.conf.j2 del directorio TEMPLATES, se cambiaron todas las rutas configuradas allí migrándolas a archivo main.yml del directorio VARS.
        
3- DB

        A) Se agregó un directorio VARS, donde se colocó un archivo main.yml, en el cual se declaran los valores de:
                - mariadb_packages_rh
                - mariadb_packages_db
                - mariadb_port
                - mariadb_datadir
                - mariadb_socket
                - mariadb_user
                - mariadb_symbolic_links
                - mariadb_log_error
                - mariadb_pid_file
                - mariadb_dbname
                - mariadb_dbuser
                - mariadb_upassword
        B) En el archivo main.yml del directorio TASKS se agregó la instalación de MariaDB apropiada para Ubuntu, ya que usa apt envés de yum. Además se cambió todo lo rolacionado a MySQL a MariaDB, exceptuando algunos módulos o variables que aún se utilizan. Y se reemplazó las reglas de iptables por firewalld o ufw, dependiendo de la distribución del Sistema Operativo, habilitando el puerto 3306.
        C) En el archivo main.yml del directorio HANDLERS, se cambió el handler "restart mysql" por "restart mariadb", y se agregó un handler para reiniciar el servicio firewalld (solo en RedHat).
        D) En el archivo my.cnf.j2 del directorio TEMPLATES, se cambiaron todas las rutas configuradas allí migrándolas a archivo main.yml del directorio VARS.
        
4- WEB
        
        A) Se agregó un directorio VARS, donde se colocó un archivo main.yml, en el cual se declaran los valores de:
                - packages_rh
                - packages_dd
                - repository
        B) En el archivo install_httpd.yml del directorio TASKS se agregó la instalación de Apache para Ubuntu, ya que usa apt envés de yum. Además se reemplazó las reglas de iptables por firewalld o ufw, dependiendo de la distribución del Sistema Operativo, habilitando el puerto 80.
        C) En el archivo main.yml del directorio HANDLERS, se agregó un handler para reiniciar el servicio firewalld (solo en RedHat).


Para iniciar este playbook se deben ejecutar los siguientes comandos:

[ansible@ansible ~]$ cd /home/ansible/repositorios/obligatorio202102/lamp_simple/

[ansible@ansible lamp_simple]$ ansible-playbook site.yml
