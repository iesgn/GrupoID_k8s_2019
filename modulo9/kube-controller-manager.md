# kube-controller-manager

Ubicamos el binario de kube-controller-manager en el directorio
/usr/local/bin de master y le damos permisos de ejecución:

    mv kubernetes/server/bin/kube-controller-manager /usr/local/bin/
	chmod +x /usr/local/bin/kube-controller-manager
	
	kube-controller-manager --allocate-node-cidrs=true \
	--authentication-kubeconfig=/var/lib/kube-controller-manager/kubeconfig \
	--authorization-kubeconfig=/var/lib/kube-controller-manager/kubeconfig \
	--bind-address=127.0.0.1 \
	--client-ca-file=/etc/ssl/certs/ca.pem \
	--cluster-cidr=10.10.0.0/16 \
	--cluster-signing-cert-file=/etc/ssl/certs/ca.pem \
	--cluster-signing-key-file=/etc/ssl/private/ca.key \
	--controllers=*,bootstrapsigner,tokencleaner \
	--kubeconfig=/var/lib/kube-controller-manager/kubeconfig \
	--leader-elect=true --node-cidr-mask-size=24 \
	--requestheader-client-ca-file=/opt/pki/kube-proxy.crt \	\
	--root-ca-file=/etc/ssl/certs/ca.pem \
	--service-account-private-key-file=/opt/pki/kube-proxy.key \
	--use-service-account-credentials=true
