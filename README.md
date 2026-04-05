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