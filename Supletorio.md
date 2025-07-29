# Práctica_Supletorio: Contenerización de Arquitectura de Microservicios para Examen de Ubicación de Inglés Implementando el Gateway.

## 1. Título  
**Contenerización de microservicios para una aplicación de examen de ubicación de inglés con API Gateway y servicio descubridor usando Docker**

## 2. Tiempo de duración  
**180 minutos**

## 3. Fundamentos  

Esta práctica aplica los principios de arquitectura de microservicios a una aplicación práctica de examen de ubicación de inglés. Cada microservicio gestiona una responsabilidad específica, como la administración de usuarios, la creación de cuestionarios, la gestión de preguntas y respuestas, y los catálogos del sistema. Para garantizar una comunicación eficiente entre servicios, se incorpora un servicio descubridor (Eureka) y un API Gateway que enruta las peticiones del cliente. Todos los servicios son contenerizados con Docker y orquestados mediante Docker Compose, asegurando independencia, escalabilidad y facilidad de despliegue. La base de datos utilizada es PostgreSQL.

## 4. Conocimientos previos

- Fundamentos de Docker y `docker-compose`.
- Arquitectura de microservicios.
- Conocimiento básico de APIs REST (preferible NestJS o Spring Boot).
- Manejo básico de PostgreSQL.
- Conceptos de API Gateway y servicio descubridor (Eureka, Consul).
- Comandos básicos en terminal.

## 5. Objetivos a alcanzar

- Implementar microservicios independientes para el sistema de examen.
- Usar un servicio descubridor para registrar dinámicamente los microservicios.
- Implementar un API Gateway como punto de entrada único.
- Contenerizar todos los servicios con Docker.
- Orquestar y comunicar los servicios mediante Docker Compose.

## 6. Equipo necesario

- Docker y `docker-compose`.
- Node.js y NestJS o Java y Spring Boot.
- Editor de código (Visual Studio Code).
- PostgreSQL (como contenedor).
- Navegador web moderno.
- Conexión a internet.

## 7. Material de apoyo

- [Docker Docs](https://docs.docker.com/)
- [NestJS Docs](https://docs.nestjs.com/)
- [Spring Cloud Netflix (Eureka)](https://spring.io/projects/spring-cloud-netflix)
- [PostgreSQL Docs](https://www.postgresql.org/docs/)
- [API Gateway Concepts](https://microservices.io/patterns/apigateway.html)

## 8. Procedimiento

### Paso 1:  Preparar el entorno local
**Diagrama de Arquitectura**
<img width="674" height="459" alt="image" src="https://github.com/user-attachments/assets/79ce0e6f-78eb-43ed-8b70-f081bfc17505" />


- `auth-service`: usuarios, roles, estados.
- `cuestionario-service`: creación y asignación de exámenes.
- `pregunta-service`: preguntas y tipos de pregunta.
- `respuesta-service`: respuestas disponibles y respuestas dadas por los estudiantes.
- `catalogo-service`: acceso a catálogos como tipo de pregunta, tipo de acceso y estado.
- `discovery-service`: Eureka como servicio de descubrimiento.
- `api-gateway`: acceso unificado mediante Spring Cloud Gateway.
- Cada microservicio tiene su propia base de datos PostgreSQL.

**Instala lo siguiente si no lo tienes:**

- Docker Desktop
- Node.js (si usarás NestJS)
- Java 17+ JDK (si usarás Spring Boot)
- Visual Studio Code
- Extensiones útiles en VS Code:
    - Docker
    - YAML
    - Java o NestJS (según nuestro stack)

**Crea una carpeta para tu proyecto:**

<img width="910" height="763" alt="Captura de pantalla 2025-07-29 010330" src="https://github.com/user-attachments/assets/a7d560ba-a5f1-4aa1-924d-854bf777ef96" />


```bash
mkdir microservicios-examen-ingles
cd microservicios-examen-ingles

```
**Dentro de esta carpeta, crea subcarpetas para cada microservicio:**

<img width="909" height="759" alt="Captura de pantalla 2025-07-29 010447" src="https://github.com/user-attachments/assets/45498b9e-9780-4a07-88ff-024089ce159c" />


```bash
mkdir auth-service cuestionario-service pregunta-service respuesta-service catalogo-service discovery-service api-gateway

```

### Paso 2:  Crear el microservicio base (auth-service) con NestJS

**Entra al directorio y crea el proyecto:**

```bash
cd auth-service
nest new auth-service

```

<img width="980" height="761" alt="Captura de pantalla 2025-07-29 010705" src="https://github.com/user-attachments/assets/b9b99cc6-a4d9-481d-bf65-0bddd472a363" />

- Selecciona el gestor de paquetes (`npm` o `yarn`).

<img width="986" height="766" alt="Captura de pantalla 2025-07-29 010730" src="https://github.com/user-attachments/assets/5d1ae0ce-df14-41fe-9643-4ec63444501a" />
<img width="978" height="758" alt="Captura de pantalla 2025-07-29 010856" src="https://github.com/user-attachments/assets/4bbe1799-46d7-43eb-841e-0886897febb5" />


**Modifica `src/main.ts`:** 

<img width="969" height="1041" alt="Captura de pantalla 2025-07-29 011458" src="https://github.com/user-attachments/assets/4edde166-035f-430e-a537-e9a563feb26f" />


```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.setGlobalPrefix('api/auth');
  await app.listen(3001);
}
bootstrap();

```
**Crea un archivo `Dockerfile`en la raíz del microservicio:** 

- Ejecuta este comando en tu carpeta del proyecto:
```cmd
echo. > Dockerfile
```
<img width="1477" height="356" alt="image" src="https://github.com/user-attachments/assets/111b9211-ac13-4c5d-9346-6b7d0009dead" />

- Eso creará un archivo vacío llamado `Dockerfile`.
  
<img width="960" height="607" alt="Captura de pantalla 2025-07-29 012625" src="https://github.com/user-attachments/assets/26c525e5-3262-4c39-a96a-c82e3d883f68" />

**Crear con Visual Studio Code** 
**Si tienes VS Code:**
- Abre tu carpeta del proyecto en VS Code.
- Haz clic derecho en el explorador de archivos (en la izquierda) → New File.
- Escribe `Dockerfile` como nombre y presiona Enter.
- Pegar el contenido:

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
EXPOSE 3001
CMD ["node", "dist/main.js"]

```
<img width="966" height="1045" alt="Captura de pantalla 2025-07-29 013226" src="https://github.com/user-attachments/assets/7ac1869f-2ab8-4f34-aade-c3d9c6f0be53" />

**Agrega un `.dockerignore`:** 
```nginx
node_modules
dist
```
<img width="969" height="947" alt="Captura de pantalla 2025-07-29 013616" src="https://github.com/user-attachments/assets/2e5767cb-73c5-402e-a7ee-bfcd36963241" />

### Paso 3: Configurar Eureka Discovery Service (Spring Boot)

**Crea un proyecto Spring Boot desde Spring Initializr:**
- Ve a https://start.spring.io
- Configura así:
    - Project: Maven
    - Language: Java
   - Spring Boot: 3.x o superior (compatible con Java 17)
   - Group: com.example
   - Artifact: discovery-service
   - Name: discovery-service
   - Dependencies:
      - Spring Web
      - Eureka Server
        
        <img width="1919" height="941" alt="Captura de pantalla 2025-07-29 014426" src="https://github.com/user-attachments/assets/b28287fb-a882-495d-aeb6-1da829fdac61" />

 - Haz clic en "Generate" para descargar el proyecto .zip
   
<img width="959" height="985" alt="Captura de pantalla 2025-07-29 014801" src="https://github.com/user-attachments/assets/0b9b4be4-5aff-4865-a581-ac2a962e9cb5" />

 - Descomprime el proyecto y ábrelo en IntelliJ, VSCode o tu editor favorito.
   
   <img width="1176" height="643" alt="Captura de pantalla 2025-07-29 014852" src="https://github.com/user-attachments/assets/21a0672e-ebc9-45bc-90b1-48835c020b51" />


**Configurar el `application.yml`**
**Ve al archivo: `src/main/resources/application.yml`**

```yaml
server:
  port: 8761

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false

```
<img width="968" height="1021" alt="Captura de pantalla 2025-07-29 015237" src="https://github.com/user-attachments/assets/17c05ae8-ec74-424e-a6e8-ba705531e4e2" />

**Agrega la anotación `@EnableEurekaServer` en la clase principal.**
**Abre la clase principal, normalmente ubicada en:`src/main/java/com/example/discoveryservice/DiscoveryServiceApplication.java`**

```java
package com.example.discoveryservice;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class DiscoveryServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(DiscoveryServiceApplication.class, args);
    }
}

```
<img width="925" height="1044" alt="Captura de pantalla 2025-07-29 015715" src="https://github.com/user-attachments/assets/21459f86-e848-4ce1-ab1f-861e49736af0" />

- Esto convierte la app en un servidor Eureka.

**Compilar el proyecto**
- Abre una terminal en la raíz del proyecto y ejecuta:
```bash
mvnw.cmd clean package -DskipTests
```
<img width="1488" height="782" alt="Captura de pantalla 2025-07-29 020522" src="https://github.com/user-attachments/assets/18d6adf9-7fc4-4d92-8ac9-3ab89785c4f7" />
- Este comando:
    - Limpia el proyecto (clean)
    - Lo empaqueta (package)
    - Ignora los tests (-DskipTests)
- Genera el archivo:
    `target/discovery-service-0.0.1-SNAPSHOT.jar`

<img width="716" height="644" alt="Captura de pantalla 2025-07-29 021014" src="https://github.com/user-attachments/assets/37bb0426-b2f5-4d0a-9a72-65483b9bb9e1" />

**Crea un archivo `Dockerfile`**
- Abre el explorador de archivos
- Ve a la carpeta raíz del proyecto
- Crea un nuevo archivo llamado exactamente:
```nginx
echo. > Dockerfile
```
<img width="1811" height="637" alt="image" src="https://github.com/user-attachments/assets/11e7513d-cb20-49fd-b2fb-ed86e1c9a196" />

- Abre el archivo con el bloc de notas o tu editor.
- Pega este contenido:
```dockerfile
FROM openjdk:17-jdk-alpine
VOLUME /tmp
COPY target/discovery-service.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
EXPOSE 8761
```
<img width="1795" height="653" alt="image" src="https://github.com/user-attachments/assets/e8ad3757-d3ef-4eba-99c9-c1500b7d274f" />

**Construir la imagen Docker**

- Abre PowerShell o CM
- Ve a la carpeta donde está mi `Dockerfile` y el proyecto
- Ejecuta el siguiente comando:
```bat
docker build -t discovery-service .
```

<img width="1084" height="758" alt="image" src="https://github.com/user-attachments/assets/7cbdf67f-3c47-4511-a6eb-151f666dad4a" />
<img width="1089" height="762" alt="image" src="https://github.com/user-attachments/assets/4be05183-4eeb-44af-9833-248396b89328" />

- Explicación:
    - `docker build`: construye una imagen

    - `-t discovery-service`: le da nombre a la imagen

    - `.`: indica que el `Dockerfile` está en el directorio actual

**Ejecutar el contenedor**
- En la misma terminal, ejecuta:
  
```bat
docker run -p 8761:8761 discovery-service
```
- Explicación:

    - `-p 8761:8761`: mapea el puerto del contenedor al de tu máquina
    - `discovery-service`: es el nombre de la imagen que construiste

- Abre tu navegador y ve a:
  http://localhost:8761    
<img width="872" height="510" alt="image" src="https://github.com/user-attachments/assets/72de6acc-260a-4fc4-94f5-736620bbc13a" />

### Paso 4: Configurar el API Gateway (Spring Cloud Gateway)
- Ve a https://start.spring.io
- Configura así:
    - Project: Maven
    - Language: Java
   - Spring Boot: 3.x o superior 
   - Group: com.tuempresa
   - Artifact: api-gateway
   - Name: api-gateway
   - Dependencies:
      - Spring Cloud Gateway
      - Eureka Discovery Client
      - (Opcional) Spring Boot DevTools para desarrollo local más ágil
<img width="1919" height="1035" alt="image" src="https://github.com/user-attachments/assets/a93fe718-e51b-41cd-982e-2f7cd593ce3d" />

**`application.yml` del API Gateway**

```yaml
server:
  port: 8080

spring:
  application:
    name: api-gateway
  cloud:
    gateway:
      routes:
        - id: auth-service
          uri: lb://AUTH-SERVICE
          predicates:
            - Path=/api/auth/**
        - id: cuestionario-service
          uri: lb://CUESTIONARIO-SERVICE
          predicates:
            - Path=/api/cuestionarios/**

eureka:
  client:
    service-url:
      defaultZone: http://discovery-service:8761/eureka/


```
<img width="1289" height="1030" alt="image" src="https://github.com/user-attachments/assets/0528a4ae-d353-42a1-94e2-588b850b685b" />


**Dockerfile del API Gateway**
<img width="1660" height="639" alt="image" src="https://github.com/user-attachments/assets/f84d8a17-54c6-40e2-880a-1fb7ae6f86e7" />
- Guarda esto como Dockerfile en la raíz del proyecto (api-gateway/):
```dockerfile
FROM openjdk:17-jdk-alpine
COPY target/api-gateway.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
EXPOSE 8080
```
<img width="970" height="1009" alt="image" src="https://github.com/user-attachments/assets/00e3ad78-4ee2-4d28-8559-6ad5e2d1ef7d" />

- Antes de construir la imagen, compila el proyecto:

```bash
mvnw.cmd clean package -DskipTests
```
- Y luego construye la imagen Docker:
```bash
docker build -t api-gateway .
```
- Y para ejecutarlo:

```bash
docker run -p 8080:8080 api-gateway
```

### Paso 5: Crear archivo `docker-compose.yml`
En la raíz del proyecto, crea el archivo `docker-compose.yml`:
```yaml
version: '3.8'

services:
  discovery-service:
    build: ./discovery-service
    ports:
      - "8761:8761"
    networks:
      - microservices-net

  api-gateway:
    build: ./api-gateway
    ports:
      - "8080:8080"
    depends_on:
      - discovery-service
    networks:
      - microservices-net

  auth-service:
    build: ./auth-service
    ports:
      - "3001:3001"
    depends_on:
      - discovery-service
    environment:
      - EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://discovery-service:8761/eureka/
      - DB_HOST=auth-db
      - DB_PORT=5432
    networks:
      - microservices-net

  auth-db:
    image: postgres:15
    environment:
      POSTGRES_DB: authdb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
    volumes:
      - auth_db_data:/var/lib/postgresql/data
    networks:
      - microservices-net

networks:
  microservices-net:

volumes:
  auth_db_data:
```
<img width="969" height="1079" alt="image" src="https://github.com/user-attachments/assets/7085cd0e-d997-4fe7-80d1-823e16dfbcd0" />

## Paso 6. Levantar todos los servicios
- Ejecuta este comando en la raíz del proyecto:
```bash
docker-compose up --build -d
```
- Esto:
    - Construye todos los servicios desde sus Dockerfiles.
    - Levanta Eureka en http://localhost:8761.
    - Levanta el API Gateway en http://localhost:8080.
    - Levanta el microservicio auth-service en http://localhost:3001.
    - Docker:
      <img width="1919" height="1076" alt="image" src="https://github.com/user-attachments/assets/1effbac6-74d8-45fa-af0a-c829450050ff" />


## 9. Resultados esperados

- Eureka muestra el registro dinámico de los microservicios.
- El API Gateway enruta las solicitudes correctamente.
- Cada microservicio puede ser escalado y actualizado individualmente.
- Los contenedores se comunican de forma eficiente.

## 10. Bibliografía

- [Docker Docs](https://docs.docker.com/)
- [NestJS Docs](https://docs.nestjs.com/)
- [Spring Cloud Netflix Eureka](https://spring.io/projects/spring-cloud-netflix)
- [API Gateway Pattern](https://microservices.io/patterns/apigateway.html)
- [PostgreSQL Docs](https://www.postgresql.org/docs/)
