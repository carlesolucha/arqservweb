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

Otro de los pasos que tuvimos que tener en cuenta fue la creación de dos máquinas virtuales de las que las dos estarán dentro de la misma subred. Para ello creamos dos:
![maquinas virtuales](https://github.com/carlesolucha/arqservweb/assets/73532775/c69d2ea2-23f0-4504-a586-7f73899dbbb2)
1)maquinaprincipal corresponde al servidor desde el que nos vamos a conectar
2)salto corresponde al servidor de salto mediante el que nos conectaremos a la máquina principal.

Al final, al aplicar el comando ssh 35.208.22.222 en mi ordenador me permitirá conectarme a la máquina de salto como se puede ver en esta imagen:

![asalto](https://github.com/carlesolucha/arqservweb/assets/73532775/5ab91e1d-aa92-41c9-9cd7-5706ba41a8f5)

Después necesitaremos hacer el mismo proceso pero dentro de la máquina de salto y aplicando la IP 35.209.191.206

![amaquinaprincipal](https://github.com/carlesolucha/arqservweb/assets/73532775/41ec00bc-ecb6-422f-a209-9906b9b3ee26)

De esta manera es como hemos creado una estructura parecida a la siguiente:


### Instalación

Indica cómo instalar tu proyecto y sus dependencias, si las tiene. Proporciona instrucciones paso a paso.

```bash
# Ejemplo de comandos de instalación si es un proyecto de Node.js
git clone https://github.com/tuusuario/turepositorio.git
cd turepositorio
npm install
