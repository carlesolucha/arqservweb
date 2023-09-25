# Práctica 2

## Ej1: creación de una máquina de salto:
Para este ejercicio, lo primero que se hizo fue crear una subred en VPC Network y crear las diferentes firewall rules (se podía haber hecho en la de defecto pero prefería hacerlo así para practicar a hacer redes)
![redesvpc](https://github.com/carlesolucha/arqservweb/assets/73532775/a17b4aa1-aff7-4b61-b90a-ec7c6de83a97)

Dentro de la misma se creó una subnet llamada 'subnet' que es donde configuraríamos el firewall posteriormente:
![subnet](https://github.com/carlesolucha/arqservweb/assets/73532775/23eab2e1-b7ed-4703-b06c-8b0f19d4efaf)

Finalmente se configuró el firewall con las premisas que necesitábamos únicamente para exponer nuestra máquina lo mínimo posible al exterior:

![firewall1](https://github.com/carlesolucha/arqservweb/assets/73532775/a74dd5fa-f6d8-4943-8be6-93601b3c3fe2)

En este firewall se crearon tres normas:
1) bastion-to-main: se trata de dar permisos a conexiones tcp al puerto 22 de las IP's internas de la máquina de salto:
2) allow-ssh-to-bastion: permite la conexión tcp del puerto 22 con las IP's de mi ordenador en mi casa como en ICAI.
3) http-from-bastion: permite la conexión de la máquina de salto.
   NOTA: estar imágenes fueron creadas antes de una versión final de la práctica. Los puertos permitidos para su acceso son los indispensables. Por ejemplo, en la norma http-from-bastion es el puerto 80 únicamente y en la norma bastion-to-main solamente se permite a la ip interna de la máquina de salto acceder como aparece en la imagen.

Otro de los pasos que tuvimos que tener en cuenta fue la creación de dos máquinas virtuales de las que las dos estarán dentro de la misma subred. Para ello creamos dos:
![maquinas virtuales](https://github.com/carlesolucha/arqservweb/assets/73532775/c69d2ea2-23f0-4504-a586-7f73899dbbb2)
1)maquinaprincipal corresponde al servidor desde el que nos vamos a conectar
2)salto corresponde al servidor de salto mediante el que nos conectaremos a la máquina principal.

Al final, al aplicar el comando ssh 35.208.22.222 en mi ordenador me permitirá conectarme a la máquina de salto como se puede ver en esta imagen:

![asalto](https://github.com/carlesolucha/arqservweb/assets/73532775/5ab91e1d-aa92-41c9-9cd7-5706ba41a8f5)

Después necesitaremos hacer el mismo proceso pero dentro de la máquina de salto y aplicando la IP 35.209.191.206

![amaquinaprincipal](https://github.com/carlesolucha/arqservweb/assets/73532775/41ec00bc-ecb6-422f-a209-9906b9b3ee26)

De esta manera es como hemos creado una estructura parecida a la siguiente:


## Ej2: introducción a los WAF: Web Application Firewall (firewall capa 7):
En este ejercicio se nos pedía quitar la IP pública de la máquinaprincipal:

![quitadaipsmaquinaprincipal](https://github.com/carlesolucha/arqservweb/assets/73532775/7f3f5377-ef7f-45e2-be56-20bdef1fc7cf)

Después tuvimos que crear el balanceador de carga:
![balanceadorDeCarga](https://github.com/carlesolucha/arqservweb/assets/73532775/5cd4c8f3-21b8-4128-8ca6-f168418f9664)

Sin embargo, para crear esto era necesario hacer un instancegroup al que tuvimos que meter la maquinaprincipal
![instancegroup](https://github.com/carlesolucha/arqservweb/assets/73532775/61ec3d9d-f92d-49e1-826f-f799cb36dd09)

Finalmente, tuvimos que habilitar Armor teniendo en cuenta los criterios que se nos pedían:
![armor](https://github.com/carlesolucha/arqservweb/assets/73532775/0460c4b7-5f3f-4209-9b91-89525770c717)
En este caso hemos denegado a todos aquellos que usen sql injection o xss scripting. Además, hemos permitido acceso a todos los países que pertenecen a la Unión Europea.

Por último, aplicamos todo esto al backend que queríamos para tener mayor seguridad:
![targets](https://github.com/carlesolucha/arqservweb/assets/73532775/26ec4908-f0c5-40fa-9f3b-f475eccd78db)

Finalmente, lo que hicimos fue conectarnos mediante ssh a la máquina de salto:
![asalto2](https://github.com/carlesolucha/arqservweb/assets/73532775/15b54820-9aa0-47d2-9da7-5ac76aeccbfa)

Posteriormente, nos conectamos a maquinaprincipal y descargamos el servidor nginx comprobando que teníamo acceso a internet debido a una NATque habíamos configurado previamente:
![amaquinaprincipal2](https://github.com/carlesolucha/arqservweb/assets/73532775/ca12cf74-5e31-487f-80b5-ecef761a8d9c)

![descargarnginx](https://github.com/carlesolucha/arqservweb/assets/73532775/7c44754f-cc5f-4578-970e-b9cdcbe4fba6)

![nginxfinal](https://github.com/carlesolucha/arqservweb/assets/73532775/528a0d0c-88eb-4a0d-b89b-792d0d8f2de7)

### ¿Qué ventajas e inconvenientes tiene el uso de https offloading en el balanceador?
Hacer "HTTPS offloading" (también conocido como SSL termination) en el balanceador significa que el balanceador de carga es el que se encarga de cifrar y descifrar el tráfico HTTPS, mientras que el tráfico entre el balanceador y los servidores de back-end es a menudo HTTP plano o algún otro protocolo no cifrado debido a que ya nos encontramos dentro de nuestra red y "hay confianza".

#### VENTAJAS	
1) Desempeño Mejorado: Descifrar y cifrar tráfico SSL puede ser intensivo en términos de CPU. Al hacerlo en el balanceador de carga, se libera a los servidores de back-end de esta tarea, lo que puede resultar en un mejor rendimiento y tiempos de respuesta más rápidos.
2) Centralización de Certificados SSL: Todos los certificados y claves SSL están en un solo lugar (el balanceador de carga) en lugar de estar dispersos en varios servidores. Esto facilita la gestión, renovación y rotación de certificados.
3) Simplificación de la Configuración: La terminación SSL en el balanceador puede simplificar la configuración de los servidores de back-end, ya que no necesitan ser configurados para SSL.
4) Inspección de Tráfico: Al descifrar el tráfico en el balanceador, se pueden aplicar reglas y políticas basadas en el contenido del tráfico, permitiendo por ejemplo la detección y bloqueo de amenazas antes de que lleguen a los servidores de back-end.
5) Optimización del Balanceo de Carga: Con el tráfico descifrado en el balanceador, este puede tomar decisiones de balanceo basadas en el contenido de las solicitudes.
   
#### INCONVENIENTES
1) Tráfico no cifrado internamente: El tráfico entre el balanceador de carga y los servidores de back-end es generalmente no cifrado. Esto podría ser una preocupación si hay riesgo de que alguien pueda interceptar este tráfico interno. Sin embargo, en entornos controlados, como un centro de datos privado o una red VPC, el riesgo suele ser mínimo.
2) Posible Punto Único de Fallo: Si el balanceador de carga se ve comprometido, el atacante tendría acceso a todo el tráfico descifrado. Sin embargo, este riesgo puede mitigarse con adecuadas medidas de seguridad y arquitecturas de alta disponibilidad.
3) Sobrecarga del Balanceador: Si hay una gran cantidad de tráfico HTTPS, el balanceador de carga podría enfrentarse a una alta carga de trabajo descifrando y cifrando tráfico. Sin embargo, muchos balanceadores de carga están optimizados para esta tarea.
4) Potenciales Problemas con Aplicaciones Sensibles: Algunas aplicaciones pueden esperar un cierto encabezado o comportamiento que indique que están detrás de SSL. Estas aplicaciones podrían necesitar configuraciones adicionales para ser conscientes de que están detrás de un offloading.

#### ¿Qué pasos adicionales has tenido que hacer para que la máquina pueda salir a internet para poder instalar el servidor nginx?

Hemos tenido que crear un NAT Gateway y configurarlo para que tuviera salida a internet. Finalmente hemos verificado que la MV tiene acceso a internet mediante la instalación del servidor nginx y hemos provado con páginas existentes como elpais.com
![pingpais](https://github.com/carlesolucha/arqservweb/assets/73532775/d7e10c72-4596-4739-935c-b51fdacd138b)

	


