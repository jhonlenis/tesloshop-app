# tesloshop-app 

Descripcion del proyecto

TesloShop es una aplicacion web end-to-end compuesta por frontend, backend y base de datos,
completamente cargada usando Docker y orquestada con Docker compose. El sistema permite ejecutar toda la aplicacion con un solo comando, facilitando el despliegue, la portabilidad y la escalabilidad del sistema

La aplicacion esta compuesta por:

+ Frontend (Angular + Nginx)
+ Backend (NestJS)
+ Base de datos (PostgreSQL)
+ Orquestación con Docker Compose
+ Red interna Docker
+ Volúmenes para persistencia de datos

# 1. Arquitectura 

Arquitectura general del sistema 

1. Frontend: Aplicación Angular servida mediante Nginx.
2. Backend: API REST desarrollada en NestJS.
3. Base de datos: PostgreSQL para almacenamiento persistente.
4. Docker Compose: Orquesta los contenedores, red y volúmenes.

El flujo de funcionamiento 

El flujo de la aplicacion funciona de la siguiente manera: 
1. El usuario accede desde el navegador al Frontend.
2. El Frontend está servido por Nginx.
3. Nginx redirige las peticiones /api al Backend (NestJS).
4. El Backend procesa la lógica del negocio.
5. El Backend se conecta a PostgreSQL para leer o guardar datos.
6. PostgreSQL devuelve la información al Backend.
7. El Backend responde al Frontend.
8. El Frontend muestra la información al usuario.

Comunicacion entre contenedores 

Los contenedores se comunican mediante una red interna de Docker llamada: 
"teslo-network"

Dentro de Docker: 
+ El backend se conecta a la base de datos usando el host db.
+ El frontend se conecta al backend usando el host backend.

Importante: Dentro de Docker nunca se usa localhost para conectar servicios entre contenedores.

# 2. Pasos de ejecucion 

Estos son los pasos para ejecutar el proyecto correctamente

* 2.1 Clonar el repositorio 
git clone https://github.com/DEV-SENA-TRAINING/tesloshop-app.git
cd tesloshop-app.git

* 2.2 Crear archivo de variables de entorno 
cp .env.example.env
Este edita el archivo .env y configura las variables: 
POSTGRES_PASSWORD
DB_PASSWORD
JWT_SECRET
Estas contraseñas deben coincidir entre PostgreSQL y el Backend.

* 2.3 Dar permisos a los scripts
chmod +x start.sh stop.sh

* 2.4 Iniciar la aplicacion con Docker 
./start.sh
O también se puede ejecutar manualmente:
docker compose up --build -d
Este comando:
+ Construye las imágenes Docker
+ Crea la red interna
+ Crea los volúmenes
+ Levanta los contenedores
+ Inicia la base de datos
+ Inicia el backend
+ Inicia el frontend

* 2.5 Verificar contenedores en ejecucion 
docker compose ps

* 2.6 Acceder a la aplicacion
una vez iniciado el sistemas: 
frontend: http://localhost:8080
backend: http://localhost:3000
base de datos: localhost:5432

* 2.7 ejecutar el Seed de la base de datos
para cargar los datos de prueba en la base de datos:
Desde el navegador:
http://localhost:3000/api/seed
o desde la terminal: 
curl http://localhost:3000/api/seed

* 2.8 Detener la aplicacion 
./stop.sh
o manualmente: 
docker compose down

# 3. Explicacion de servicion 
El sistema esta definido en el archivo docker-compose.yml, donde se configura los servicios, redes y volumenes

* 3.1 Servicio db - postgreSQL
Este servicio corresponde a la base de datos postgreSQL
funciones: 
+ Almacenar usuarios
+ Almacenar productos
+ Almacenar pedidos
+ Persistir información del sistema
Caracteristicas: 
+ Usa la imagen oficial postgres:14.3
+ Usa un volumen llamado postgres-data
+ Tiene un healthcheck para verificar que la base de datos esté lista antes de iniciar el backend
+ Expone el puerto 5432
El volumen permite que los datos no se pierdan al reiniciar los contenedores

* 3.2 Servicio backend 
Este servicio corresponde al backend desarrollado en NestJS.
Funciones: 
+ API REST
+ Autenticación con JWT
+ Gestión de usuarios
+ Gestión de productos
+ Conexión con PostgreSQL
+ Documentación Swagger
+ Seed de base de datos 
El Backend: 
+ Se construye desde el Dockerfile en teslo-shop
+ Usa multi-stage build (dev y prod)
+ Se conecta a la base de datos usando:
DB_HOST=db
+ Expone el puerto 3000
+ Depende del servicio db
+ Usa volúmenes para hot-reload en desarrollo

* 3.3 Servicio frontend 
Este servicio corresponde al frontend de la aplicacion
Funciones: 
+ Interfaz gráfica del usuario
+ Mostrar productos
+ Login de usuarios
+ Comunicación con la API
+ Servir archivos estáticos con Nginx
+ Proxy de /api hacia el backend
El Dockerfile del frontend: 
1. compila la aplicacion angular
2. copia los archivos compilados a Nginx
3. Nginx sirve la aplicacion en el puerto 8080
4. Nginx redirige las peticiones /api al backend

Esto evita problemas CORS porque frontend y API parecen estar en el mismo servidor.

* 3.4 Redes Docker
Se utiliza una red llamada:
teslo-network

Esta red permite que los contenedores se comuniquen entre si usando el nombre del servicio como hostname: 
+ PostgreSQL: db
+ Backend: backend
+ Frontend: frontend

* 3.5 Volumenes Docker
Se utiliza el volumen: 
postgres-data

Este volumen sirve para: 
+ Guardar los datos de PostgreSQL
+ Evitar pérdida de información
+ Mantener la base de datos aunque se eliminen los contenedores

# 4. Tecnologias utilizadas
+ Docker
+ Docker Compose
+ PostgreSQL
+ NestJS
+ Angular
+ Nginx
+ Node.js
+ TypeScript
+ JWT Authentication
+ Swagger

# 5. Conclusion

Este proyecto implementa una arquitectura moderna basada en contenedores utilizando
Docker, permitiendo ejecutar una aplicación completa end-to-end con frontend, backend y
base de datos mediante Docker Compose. La contenerización facilita el despliegue, la
portabilidad, la escalabilidad y la reproducibilidad del entorno de desarrollo y
producción, garantizando que la aplicación funcione de la misma manera en cualquier
máquina.

* Imagenes

  1. Dockerfile del backend
     
    <img width="791" height="1293" alt="Captura de pantalla 2026-04-04 154825" src="https://github.com/user-attachments/assets/4748fd0e-b68d-4c54-aa25-5844967839a2" />


  2. Dockerfile del Frontend
     
     <img width="922" height="935" alt="Captura de pantalla 2026-04-04 155017" src="https://github.com/user-attachments/assets/99da643c-8394-479f-b9d8-c666b321c0b1" />


   3. Creacion del Nginx.config del Frontend
      
      <img width="930" height="1355" alt="Captura de pantalla 2026-04-04 155203" src="https://github.com/user-attachments/assets/bdb7771e-f0dd-47d4-b3f1-0431863bfd6f" />


   4. Configuracion del .env.example y modificiacion del .env
      <img width="1071" height="1132" alt="Captura de pantalla 2026-04-04 155800" src="https://github.com/user-attachments/assets/9db69e51-d88d-44cd-8315-ba7268a46193" />
      
      <img width="1080" height="818" alt="Captura de pantalla 2026-04-04 155825" src="https://github.com/user-attachments/assets/baa99e4c-7fb3-4e92-8097-b8f85882f187" />


   5. Confguracion del start.sh
      
      <img width="961" height="821" alt="Captura de pantalla 2026-04-04 160031" src="https://github.com/user-attachments/assets/85adbb22-4c14-4c50-a7ad-cc1b5cf7faff" />
   

   6. Construyendo las imágenes
      
      <img width="715" height="597" alt="Captura de pantalla 2026-04-04 211113" src="https://github.com/user-attachments/assets/6b6d5d1c-12f6-4dc1-9aec-5965eea9947f" />


  7. imágenes creadas

   <img width="755" height="207" alt="Captura de pantalla 2026-04-04 211445" src="https://github.com/user-attachments/assets/b03f8f60-4d24-428d-8fe5-280393ec1425" />


   8. Configuracion del Stop.sh
      
      <img width="883" height="287" alt="Captura de pantalla 2026-04-04 160052" src="https://github.com/user-attachments/assets/642cef28-dc6d-4528-b70c-5df82006ca24" />


   9. Version del docker y version del docker compose
      
      <img width="460" height="161" alt="Captura de pantalla 2026-04-04 160134" src="https://github.com/user-attachments/assets/5411f7a5-ef4b-4da6-bfaa-35e9f59e9809" />


   10. Permisos a los Scripts
       
       <img width="515" height="121" alt="Captura de pantalla 2026-04-04 205935" src="https://github.com/user-attachments/assets/b4516c83-241b-40f0-be27-50573172ee43" />


   11. Docker-compose.yml terminado
       
       <img width="811" height="1249" alt="Captura de pantalla 2026-04-04 160757" src="https://github.com/user-attachments/assets/be6b0614-6587-4451-bef5-3580066184c9" />


  12. Lanzar la aplicacion
      
      <img width="1021" height="1334" alt="Captura de pantalla 2026-04-04 161836" src="https://github.com/user-attachments/assets/a1fbf6e1-2f5e-4ea9-965e-f2d95d7063fe" />
      
 
13.   aplicación funcionando
 
      <img width="967" height="743" alt="Captura de pantalla 2026-04-04 165052" src="https://github.com/user-attachments/assets/d94e8421-fc77-4fcc-b570-3b2e2e2470a8" />


  13. Verificacion de que servicios estan corriendo
      
      <img width="1077" height="234" alt="Captura de pantalla 2026-04-04 161947" src="https://github.com/user-attachments/assets/e5badf42-a607-4dbc-ad9c-ce05d52e7cd5" />


  14. Poblar la base de datos con el Seed
      
      <img width="487" height="78" alt="Captura de pantalla 2026-04-04 162038" src="https://github.com/user-attachments/assets/a0abb9ba-f5e2-41d0-beca-322d44d7b430" />


  15. Vista de la app desde la API
      
      <img width="2529" height="1461" alt="Captura de pantalla 2026-04-04 164023" src="https://github.com/user-attachments/assets/6335a0e7-620d-427c-8f71-e265e5099756" />
      
      <img width="2537" height="1453" alt="Captura de pantalla 2026-04-04 164414" src="https://github.com/user-attachments/assets/3a31e762-8ce1-4b17-a526-66d9967b3c6d" />


  16. Vista de la app desde el Frontend
      
      <img width="2517" height="1464" alt="Captura de pantalla 2026-04-04 171947" src="https://github.com/user-attachments/assets/d1018f82-f2dd-417d-8f69-00d08878bd1d" />
      
      <img width="2542" height="1454" alt="Captura de pantalla 2026-04-04 172002" src="https://github.com/user-attachments/assets/60e9b4e8-2006-4774-a491-47884870773b" />
