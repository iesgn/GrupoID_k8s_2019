# Configuraciones previas

Los equipos tienen instalado la versión 18.04 de Ubuntu

## Modificaciones en /etc/sysctl.conf

Ejecutamos como root:

    cat >> /etc/sysctl.conf <<EOF
    net.ipv4.ip_forward = 1
    net.ipv4.conf.all.forwarding = 1
	net.bridge.bridge-nf-call-iptables = 1
	EOF
	sysctl -p /etc/sysctl.conf

## Instalación de docker

Todos los nodos (master y workers) deben tener instalado un "runtime"
para la ejecución de contenedores, tanto para ejecutar contenedores de
componentes o complementos de k8s en el master, como los propios de
las aplicaciones en los workers.

    sudo apt install docker.io
