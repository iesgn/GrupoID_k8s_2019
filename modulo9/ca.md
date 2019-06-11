# Autoridad certificadora y kubeconfig
La autenticación entre los diferentes componentes de k8s y con los
usuarios puede realizarse de varias formas, una de las más comunes es
instalando y configurando una autoridad certificadora que firme los
certificados de cada uno de los elementos, para que unos puedan
confiar en otros y la comunicación se realice de forma segura. Este
paso es necesario pero ralentiza la instalación de los componentes de
k8s que es en lo que nos queremos centrar, por lo que intentaremos
automatizarlo lo más posible.

## Creación de la CA y ubicación de los certificados

Las claves se ubicarán en el directorio /etc/ssl/private y los
certificados en /usr/local/share/ca-certificates como es habitual en
los sistemas debian y derivados. Los procesos que se ejecuten como
usuario root podrán acceder a las claves sin problema, pero los que se
ejecuten con otros usuarios será necesario que se añadan esos usuarios
al grupo ssl-cert.

### Creación del grupo ssl-cert

    sudo addgroup --gid 54 ssl-cert
	chgrp ssl-cert /etc/ssl/private
	
### Creación de la entidad certificadora

En el modo master:

    openssl genrsa -out /etc/ssl/private/ca.key 4096
	chmod 0400 /etc/ssl/private/ca.key	
	openssl req -x509 -new -nodes -key /etc/ssl/private/ca.key -sha256 -days 1024 -out /usr/local/share/ca-certificates/ca.crt -subj "/CN=ca"
	update-ca-certificates
	
En el resto de nodos se copia el certicado ca.crt y se pone en la
misma ubicación:

    scp /usr/local/share/ca-certificates/ca.crt worker1:/usr/local/share/ca-certificates/ca.crt
	ssh worker1 update-ca-certificates
    scp /usr/local/share/ca-certificates/ca.crt worker2:/usr/local/share/ca-certificates/ca.crt
	ssh worker2 update-ca-certificates
	
	
