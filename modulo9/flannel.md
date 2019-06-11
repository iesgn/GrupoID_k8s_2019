# Flannel

Puesto que habíamos decidido utilizar el segmento 10.10.0.0/16 para la
red de flannel, creamos un registro en etcd para ello:

    etcdctl set /coreos.com/network/config '{ "Network": \
	"10.10.0.0/16, "SubnetLen": 24, "Backend": {"Type": "vxlan"} }'
	
## Instalación

En ubuntu disponemos de paquete flannel, por lo que procedemos a
instalarlo con apt en todos los nodos:

    apt install flannel
	
El paquete no proporciona ningún directorio de configuración, ni
unidad de systemd ni nada, simplemente los binarios.

