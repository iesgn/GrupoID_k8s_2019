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

    for i in $NODOS; do
	ssh $i <<EOF
    addgroup --gid 54 ssl-cert
    chgrp -R ssl-cert /etc/ssl/private
    chmod 750 /etc/ssh/private
	EOF
    done
	
### Creación de la entidad certificadora

En el modo master:

    openssl genrsa -out /etc/ssl/private/ca.key 4096
	openssl req -x509 -new -nodes -key /etc/ssl/private/ca.key -sha256 -days 1024 -out /usr/local/share/ca-certificates/ca.crt -subj "/CN=ca"
	update-ca-certificates
	
En el resto de nodos se copia el certicado ca.crt y se pone en la
misma ubicación:

    for i in $WORKERS; do
	scp /usr/local/share/ca-certificates/ca.crt $i:/usr/local/share/ca-certificates/ca.crt
	ssh $i update-ca-certificates
	done

### Creación de los certificados para todos los equipos y servicios

No solo los equipos, sino los diferentes servicios de kubernetes
utilizar un certificado para autenticarse, por lo que crearemos
certificados x509 para todos ellos, los ubicaremos en un directorio de
trabajo y los iremos distribuyendo a medida que los necesitemos.

    mkdir /opt/pki
	for i in $NODOS; do
	openssl genrsa -out /opt/pki/${i}.key 4096
	openssl req -new -key /opt/pki/${i}.key -out /opt/pki/${i}.csr -subj "/CN=${i}"
	openssl x509 -req -in /opt/pki/${i}.csr -CA /etc/ssl/certs/ca.pem -CAcreateserial -CAkey /etc/ssl/private/ca.key -out /opt/pki/${i}.crt -days 500
	done
	
	export SERVICIOS="kube-apiserver kube-scheduler kube-controller-manager"
	for i in $SERVICIOS; do
	openssl genrsa -out /opt/pki/${i}.key 4096
	openssl req -new -key /opt/pki/${i}.key -out /opt/pki/${i}.csr -subj "/CN=${i}"
	openssl x509 -req -in /opt/pki/${i}.csr -CA /etc/ssl/certs/ca.pem -CAcreateserial -CAkey /etc/ssl/private/ca.key -out /opt/pki/${i}.crt -days 500
	done

Por último, repetimos lo mismo para el usuario admin (que hay que
incluir en O=system:masters para que tenga todos los privilegios), que
utilizaremos para administrar el clúster de kubernetes:

    openssl genrsa -out /opt/pki/admin.key 4096
    openssl req -new -key /opt/pki/admin.key -out /opt/pki/admin.csr -subj "/CN=admin/O=system:masters"
    openssl x509 -req -in /opt/pki/admin.csr -CA /etc/ssl/certs/ca.pem -CAcreateserial -CAkey /etc/ssl/private/ca.key -out /opt/pki/admin.crt -days 500 

## Instalación de kubectl

Siguiendo la instalación de binarios desde la documentación de kubernetes:

    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
    chmod +x ./kubectl
	sudo mv ./kubectl /usr/local/bin/kubectl
	
## Kubeconfig

Vamos a configurar un clúster, un usuario y un contexto para que el
usuario admin pueda comunicarse posteriormente con el kube-apiserver.

    kubectl config set-cluster curso \
    --certificate-authority=/etc/ssl/certs/ca.pem --embed-certs=true \
    --server=https://master
	
    kubectl config set-credentials admin \
    --client-certificate=/opt/pki/admin.crt \
    --client-key=/opt/pki/admin.key --embed-certs=true
	
	kubectl config set-context curso --cluster=curso --user=admin
	
	kubectl config use-context curso
	
Comprobamos la configuración correcta de todo con la instrucción:

    kubectl config view
	
No solo para el usuario admin, también el resto de componete del
master necesitan tener su propio fichero kubeconfig configurado para
poder interactuar con el kube-apiserver, por lo que repetimos lo
anterior para kube-scheduler y kube-controller-manager:

    for i in kube-scheduler kube-controller-manager; do
	kubectl config set-cluster curso \
	--certificate-authority=/etc/ssl/certs/ca.pem --embed-certs=true \
	--server=https://master --kubeconfig=/var/lib/${i}/kubeconfig
	kubectl config set-credentials ${i} --client-certificate=/opt/pki/${i}.crt \
	--client-key=/opt/pki/${i}.key --embed-certs=true \
	--kubeconfig=/var/lib/${i}/kubeconfig
	kubectl config set-context curso --cluster=curso --user=${i} \
	--kubeconfig=/var/lib/${i}/kubeconfig
	done
	
	
