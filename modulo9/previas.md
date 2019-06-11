# Configuraciones previas

Los equipos tienen instalado la versión 18.04 de Ubuntu.

Para agilizar la instalación se supone que es posible acceder desde el
nodo master a los workers a través de ssh, para lo cual es
recomendable crear una clave ssh y compartirla con los otros nodos:

    ssh-keygen -t ecdsa
	ssh-copy-id -i ~/.ssh/id_ecdsa.pub 10.0.0.2
	ssh-copy-id -i ~/.ssh/id_ecdsa.pub 10.0.0.3

Por si necesitamos acceder como root, copiamos las claves autorizadas
en cada worker al directorio .ssh de root:

    sudo cp ~/.ssh/authorized_keys /root/.ssh/
	
Y en el master copiamos las claves pública y privada:

    sudo cp ~/.ssh/id_ecdsa* /root/.ssh/
	
## Configuración de sudo sin contraseña

En este despliegue y por sencillez y facilidad es conveniente por
facilidad que el usuario, por lo que en el fichero /etc/sudores deberá
aparecer la línea:

    administrador    ALL=(ALL:ALL) ALL

## Lista de nodos

Vamos a utilizar durante la instalación varios pasos que se van a dar
en todos los nodos o en todos los workers, para lo que es cómodo
definir las variables de entorno:

    export NODOS="master worker1 worker2"
	export WORKERS="worker1 worker2"

Por simplicidad, los nodos utilizarán resolución estática entre ellos,
por lo que habrá que adaptar convenientemente los ficheros /etc/hosts
incluyen en todos las entradas:

    10.0.0.1 master
	10.0.0.2 worker1
	10.0.0.3 worker2

Podemos simplemente ejecutar:

    for i in $NODOS; do
	ssh $i <<EOF
	sed -i '1 a 10.0.0.4 master' /etc/hosts
	sed -i '2 a 10.0.0.8 worker1' /etc/hosts
	sed -i '3 a 10.0.0.10 worker2' /etc/hosts
	EOF
	done
    
## Modificaciones en /etc/sysctl.conf

Ejecutamos como root:

	for i in $NODOS; do
	ssh $i <<EOF
	sed -i '1 i net.ipv4.ip_forward = 1' /etc/sysctl.conf
	sed -i '1 i net.ipv4.conf.all.forwarding = 1' /etc/sysctl.conf
	sed -i '1 i net.bridge.bridge-nf-call-iptables = 1' /etc/sysctl.conf
	sysctl -p /etc/sysctl.conf
	EOF
	done

## Instalación de docker

Todos los nodos (master y workers) deben tener instalado un "runtime"
para la ejecución de contenedores, tanto para ejecutar contenedores de
componentes o complementos de k8s en el master, como los propios de
las aplicaciones en los workers.

    sudo apt install docker.io
