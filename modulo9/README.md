# Instalación paso a paso

Se va a realizar una instalación paso a paso de k8s que tiene como
principal objetivo entender cómo funcionan los componentes de
kubernetes, más que realizar una instalación real.

Para la realización de esta instalación se ha utilizado además de la
experiencia y conocimientos propios, la documentación oficial de
kubernetes, la extraordinaria sesión que impartió Javier Salmerón en
las jornadas de 2018 en Bitnami y las referencias que se indican al
final.

## Punto de partida

3 equipos interconectados con los siguientes nombres y direcciones IP:

* master: 10.0.0.1/24
* worker1: 10.0.0.2/24
* worker2: 10.0.0.3/24
    
## Red de kubernetes

Vamos a utilizar una red tipo CNI con flannel con las siguientes
características:

* Red para flannel: 10.10.0.0/16
* Red para servicios: 10.20.0.0/24

## Pasos a seguir para la instalación

1. [Configuraciones previas](previas.md)
1. [Autoridad certificadora y kubeconfig](ca.md)
1. [etcd](etcd.md)
1. [flannel](flannel.md)
1. [kube-apiserver](kube-apiserver.md)
1. [kube-scheduler](kube-scheduler.md)
1. [kube-controller-manager](kube-controller-manager.md)
1. [kubelet](kubelet.md)
1. [kube-proxy](kube-proxy.md)

## Referencias

1. https://github.com/kelseyhightower/kubernetes-the-hard-way
1. https://icicimov.github.io/blog/kubernetes/Kubernetes-cluster-step-by-step/
