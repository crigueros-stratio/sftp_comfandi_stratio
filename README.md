# SFTP

# Supported tags and respective `Dockerfile`

- `debian`
# Securely share your files

Easy to use SFTP ([SSH File Transfer Protocol](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)) server with [OpenSSH](https://en.wikipedia.org/wiki/OpenSSH).

# Proceso de instalacion en Datacentric

    # Proceso Local

 - Se elimina imagen si ya ha sido creada.
    - ```docker rmi camiloriguer/sftp```
   
 - Se construye la imagen del sftp.
    - ```docker build -t camiloriguer/sftp . ```
 - Se sube imagen creada al registry que se tenga disponible.
    - ```docker push camiloriguer/sftp:latest```
    
    
    # Proceso En Datacentric
    
Se crea descriptor para realizar el despliegue del servicio.
 
 - Se crea variable de entorno por la cual sera creado el usuarios y directorio
```
       env": { 
              "SFTP_USERS": "pos:comfandiSFTP2O2O:1001:1001:in,procesados" 
            }
```
- Se configura proxy para que tenga salida por Marathon el servicio.
```
      "labels": {
          "HAPROXY_0_MODE": "tcp",
          "HAPROXY_GROUP": "external",
          "HAPROXY_0_PORT": "20022"
        }
```
- Se configura el servicio siempre se despliegue en el mismo servidor, esto a que los archivos que sean dejados en el sftp se guardan en el volumen compartido en el servidor.
```
       "constraints": [
              [
                "hostname",
                "LIKE",
                "10.2.34.19"
              ]
            ],
```    
- Puerto en el cual despliega el sftp en el contenedor. 
```
       "portMappings": [
                {
                  "containerPort": 22,
                  "servicePort": 10205
                }
              ],
```     
- Volumnen en el cual se dejan los archivos cargos al sftp. directorio del servidor `/home/{user}` y en el contenedor `/comfandi/volumen/sftp/`

```
       "volumes": [
                 {
                   "containerPath": "/home",
                   "hostPath": "/comfandi/volumen/sftp/",
                   "mode": "RW"
                 }
```      
    
- Registry de donde se va tomar la imagen para desplegar el servicio.
```
  "image": "camiloriguer/sftp:latest",
 ```   
#### Nota
- El resto de parametros del descriptor vienen por defecto al momento de la creación.




