# kube-apiserver

Componente principal de k8s, encargado de ofrecer la API y gestionar
las peticiones.

## Descarga de los binarios

No tenemos paquetes de kubernetes, descargamos los binarios directamente del proyecto:

https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.14.md

    wget https://dl.k8s.io/v1.14.3/kubernetes-server-linux-amd64.tar.gz
	
Descomprimimos el archivo, ubicamos el binario de kube-apiserver en
/usr/local/bin y le damos permisos de ejecución.

	tar xvf kubernetes-server-linux-amd64.tar.gz
    mv kubernetes/server/bin/kube-apiserver /usr/local/bin/
    chmod +x /usr/local/bin/kube-apiserver
	
Podemos probar a ejecutar kube-apiserver desde línea de comandos
directamente a través de los siguientes parámetros:

    kube-apiserver \
	--tls-cert-file=/opt/pki/master.crt \
	--tls-private-key-file=/opt/pki/master.key \
	--client-ca-file=/etc/ssl/certs/ca.pem \
	--etcd-cafile=/etc/ssl/certs/ca.pem \
	--etcd-certfile=/opt/pki/master.crt \
	--etcd-keyfile=/opt/pki/master.key \
	--etcd-servers=https://master:2379 \
	--service-cluster-ip-range=10.20.0.0/24 \
	--authorization-mode=RBAC,AlwaysAllow \
	--etcd-servers=https://master:2379 \
	--v=2
	
	
