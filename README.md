# Tarea10_odoo
### Compose comentado   
``
services:
  odoo:
    image: odoo:17
    restart: always
    ports: #Puerto por defecto de este servicio
      - "8069:8069"
    depends_on: #Dependencia necesaria de este servicio para hacer este ejercicio
      - db
    environment: #Necesario para saltarse el proceso de crear cuenta
      - USER=odoo
      - PASSWORD=odoo
    volumes: #Volumen para que la info de este contenedor persista
      - ./config:/etc/odoo
      - ./extra-addons:/mnt/extra-addons
  db:
    image: postgres:latest
    restart: always
    environment:
      - POSTGRES_USER=odoo
      - POSTGRES_PASSWORD=odoo
    volumes: #Volumen para que la info de este contenedor persista
      - local_pgdata:/var/lib/postgresql/data
  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin4_container
    restart: always
    depends_on: #Dependencia necesaria de este servicio para hacer este ejercicio (y para que vaya bien el servicio)
      - db
    ports:
      - "8888:80"
    environment: #Variables para que no sea necesario crear la cuenta de pgAdmin
      PGADMIN_DEFAULT_EMAIL: ygarcialago@danielcastelao.org
      PGADMIN_DEFAULT_PASSWORD: 1234
    volumes: #Volumen para que la info de este contenedor persista
      - pgadmin_data:/var/lib/pgadmin
volumes:
  local_pgdata:
  pgadmin_data:
``
    
Ya con el compose creado tendremos que poner el siguiente comando:  

``
sudo docker compose up -d
``
*El parámetro -d se usa para que los lance en 2do plano*

Para poder acceder al contenedor a través del navegador tendrás que hacer lo siguiente *si no les pusiste un nombre personalizado al contenedor*:  
``
sudo docker ps
``
Este comando te mostrará, entre otras cosas, **el id del contenedor**, lo que es necesario para es siguiente comando:   

``
sudo docker inspect <id_del_contenedor-nombre>
``

Con este comando se te mostrará la **información** del contenedor, pero lo que nos interesa es el apartado de **IP_Address** y el de **Ports** *(si no sabes, no te acuerdas o no quieres verlo en el compose)*.  

## Servicios  
### Odoo   
Después de ver la IP, tendremos que poner en el navegador la siguiente dirección:  
``
http://<IP_DEL_CONTENEDOR>:<PUERTO_DEL_CONTENEDOR>
``
Esto nos sirve para acceder tanto a Odoo como para cualquier otro contenedor.   
Al entrar a Odoo nos saldrá la siguiente pantalla:   
![Cap1](https://github.com/YoelGarciaLago/Tarea10_odoo/blob/main/C1.png?raw=true)
Aquí tendremos que **guardar la contraseña del manager de la BD en nuestro PC local** o **poner nuestra propia contraseña** y rellenar todos los datos *(salvo en nº de tlfo)*. Tened en cuenta que el nombre de la base de datos tiene que ser una inexistente.    
Es recomendable tener marcada la casilla de **Demo Data** para que cuando cree las BB.DD. tengan algo de información.  
Si seguimos los pasos, nos pedirá que pongamos nuestro nombre de usuario y nuestra contraseña, que están definidas en el **environment del servicio en el compose**.   
Tras rellenar los datos nos saldrá la siguiente imagen -->   
![Cap2](https://github.com/YoelGarciaLago/Tarea10_odoo/blob/main/C2.png?raw=true)   
Y con esto ya acabaríamos con Odoo.


### pgAdmin   
Para esto ponemos la ip del contenedor + su puerto *(si no se sabe alguno de los datos más arriba se explica)* y se nos mostrará un inicio de sesión. Los datos que tendremos que poner están definidos en el **environment del servicio en el compose**.   
Tras iniciar sesión nos saldrá la pantalla principal. Para acceder al servicio de PostgreSQL tendremos que darle click derecho al apartado de ***Servers*** -> ***Register*** --> ***Server***. Si todo se hizo bien saldrá lo siguiente:   
![Cap3](https://github.com/YoelGarciaLago/Tarea10_odoo/blob/main/C3.png?raw=true)   
En el apartado de nombre, en **General**, le puedes poner el que sea *(no afectará a la conexión)*, sin embargo, en el apartado **Conexión**, tendremos que poner la IP del servicio de Postgres en el apartado de **Dirección del servidor**.    
Después, si se modificó el puerto, podremos el del contenedor. En el apartado de nombre de usuario y en contraseña, tendrás que utilizar el definido en el **environment del compose**.   
Si todo se siguió bien tendría que aparecer lo siguiente:   
![Cap4](https://github.com/YoelGarciaLago/Tarea10_odoo/blob/main/C4.png?raw=true)    
![Cap5](https://github.com/YoelGarciaLago/Tarea10_odoo/blob/main/C5.png?raw=true)   

## Preguntas teóricas     
### ¿Qué pasa si el ordenador local tiene el puerto 5432 ocupado?   
En teoría, este compose no tiene ningún puerto mapeado en el psql, por lo que para conectarse de manera externa a la red de docker usaría otro puerto, pero, si se definiese de la siguiente manera:   
``
ports:
  5432:5432
``
No se podría crear el contenedor.

### ¿Qué pasa si el ordenador local tiene el puerto 8069 ocupado?   
En este caso, sí que nos saltaría el error de que no se puede crear el contenedor de primeras, ya que en el compose se define los puertos que va a utilizar el servicio

### ¿Cómo solucionarlo?     
Para solucionar este conflicto tienes dos opciones:  
- Parar los servicios de la máquina local *(con comandos de systemctl por ejemplo)*.   
- Modificar los puertos del compose.    
