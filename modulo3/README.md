# Módulo 3: Depliegue de aplicaciones en Kubernetes

## Ejemplo 1: Pod

Para crear el pod desde el fichero yaml:

    kubectl create -f nginx.yaml

Y podemos ver que el pod se ha creado:

    kubectl get pods

Si queremos saber en qué nodo del cluster se está ejecutando:

    kubectl get pod -o wide

Para obtener información más detallada del pod:

    kubectl describe pod nginx

Para eliminar el pod:

    kubectl delete pod nginx

Podemos modificar las características de cualquier recurso de kubernetes una vez creado, por ejemplo podemos modificar la definición del pod de la siguiente manera:

    kubectl edit pod nginx
    KUBE_EDITOR="nano" kubectl edit pod nginx


