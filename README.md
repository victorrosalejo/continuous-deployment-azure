# Despliegue continuo en Azure

Este proyecto consta de un servidor WEB sencillo para la gestión de posts (anuncios).

## Despliegue en Azure (local)

En primer lugar, nos logueamos en el cliente de Azure (el login se realizará con ayuda del navegador) 
```
az login
```

Creamos un grupo de recursos `posts-group`
```
az group create --name posts-group --location westeurope
```

Damos valor a las siguientes variables de entorno:

```
DOCKERHUB_USERNAME=
DOCKERHUB_READ_TOKEN=
```

Creamos nuestra aplicación lanzando un contenedor a partir de la imagen `maes95/posts:v1`. Le pondremos a la aplicación el nombre `urjc-posts`

```
az container create \
    --resource-group posts-group \
    --name posts-michel \
    --image maes95/posts:v1 \
    --registry-login-server index.docker.io \
    --registry-username $DOCKERHUB_USERNAME \
    --registry-password $DOCKERHUB_READ_TOKEN \
    --dns-name-label posts-michel \
    --ports 8080 \
    --os-type Linux \
    --cpu 1 \
    --memory 1
```

La URL dónde podremos acceder a la aplicación será: posts-michel.spaincentral.azurecontainer.io

Podemos ver el estado de nuestra aplicación con el siguiente comando
```
az container show  \
    --resource-group posts-group \
    --name posts-michel \
    --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
    --out table
```

Podemos revisar los logs utilizando el siguiente comando:
```
az container attach --resource-group posts-group --name posts-michel
```

