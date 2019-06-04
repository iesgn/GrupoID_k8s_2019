# Configuración de aplicaciones

## Ejemplo 1: Variables de entorno

Podemos definir un Deployment que defina un contenedor configurado por medio de variables de entorno.

Creamos el despliegue:

    kubectl create -f mariadb-deployment.yaml

O directamente ejecutando:

    kubectl run mariadb --image=mariadb --env MYSQL_ROOT_PASSWORD=my-password

Veamos el pod creado:

    kubectl get pods -l app=mariadb

Y probamos si podemos acceder, introduciendo la contraseña configurada:

    kubectl exec -it mariadb-deployment-fc75f956-f5zlt -- mysql -u root -p


