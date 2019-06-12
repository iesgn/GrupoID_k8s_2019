# etcd

etcd es un almacén clave-valor esencial en kubernetes ya que guarda
toda la información del clúster. Es un servicio que lógicamente cuando
se ejecuta en un entorno en producción se hace replicado en varios
nodos máster, pero que aquí por simplificar, lo haremos en uno solo.

IMPORTANTE: Acceder a etcd equivale a tener todos los privilegios de
acceso al clúster de kubernetes.

Ya que estamos en Ubuntu 18.04, aprovechamos que este software sí está
empaquetado para instalarlo utilizando apt:

    apt install etcd-server etcd-client
	
Vamos a utilizar los certificados del servidor, pero como el usuario
que ejecuta el demonio de etcd por defecto es "etcd", tenemos que
hacer algunos cambios de permisos y propietarios para que pueda
acceder a los certificados y claves:

    chmod 0750 /opt/pki
	chgrp -R ssl-cert /opt/pki/
	adduser etcd ssl-cert
	
Y modificamos los siguientes parámetros del fichero /etc/default/etcd:

    ETCD_NAME="curso"
	ETCD_DATA_DIR="/var/lib/etcd/curso"
	ETCD_LISTEN_CLIENT_URLS="http://localhost:2379,https://10.0.0.1:2379"
	ETCD_ADVERTISE_CLIENT_URLS="http://localhost:2379,https://10.0.0.1:2379"
	ETCD_CERT_FILE="/opt/pki/master.crt"
	ETCD_KEY_FILE="/opt/pki/master.key"
	ETCD_CLIENT_CERT_AUTH
	ETCD_TRUSTED_CA_FILE="/etc/ssl/certs/ca.pem"
	
Reinicamos etcd y comprobamos que funciona correctamente.

El servicio etcd no está habilitado por defecto, por lo que debemos
asegurarnos que lo esté para que funcione correctamente al reiniciar
el nodo:

    systemctl enable etcd.service
