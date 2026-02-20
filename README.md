# arqbroker
Proyecto investigacion 
---


#  Patrón Arquitectónico Broker

## Descripción

El **patrón arquitectónico Broker** es un patrón diseñado para la construcción de **sistemas distribuidos desacoplados**, donde múltiples componentes (clientes y servidores) se comunican a través de un intermediario llamado *broker*.

Este patrón permite que los clientes invoquen servicios remotos sin conocer su ubicación, implementación o detalles de comunicación, promoviendo escalabilidad, flexibilidad y mantenibilidad.

---

## Objetivo

Resolver los problemas de:

* Acoplamiento fuerte entre clientes y servicios.
* Localización de servicios en entornos distribuidos.
* Evolución y escalabilidad de aplicaciones no monolíticas.
* Comunicación remota transparente.

---

##  Estructura del Patrón

El patrón Broker está compuesto por los siguientes elementos:

### 1 Cliente

* Solicita servicios.
* No conoce la ubicación ni la implementación del servidor.
* Se comunica únicamente con el broker.

### 2 Servidor

* Implementa y expone funcionalidades mediante interfaces.
* Se registra en el broker.
* Procesa peticiones y devuelve resultados.

### 2 Broker

* Actúa como intermediario.
* Registra servidores.
* Localiza servicios adecuados.
* Redirige peticiones y respuestas.
* Gestiona errores.

### 4 Proxy (Cliente y Servidor)

* Abstrae detalles de comunicación.
* Maneja serialización y deserialización de datos.
* Permite que la invocación parezca local aunque sea remota.

---

##  Flujo de Funcionamiento

### Registro de un Servidor

1. El servidor inicia.
2. Se registra en el broker indicando qué interfaz implementa.
3. El broker almacena esa información.

### Petición de un Cliente (Síncrona)

1. El cliente solicita un servicio.
2. El proxy del cliente serializa la petición.
3. El broker localiza el servidor adecuado.
4. El proxy del servidor deserializa la petición.
5. El servidor procesa la solicitud.
6. La respuesta vuelve al cliente a través del broker.

---

##  Variantes del Patrón

### 1 Comunicación Directa

El broker interviene solo en la primera petición. Luego, cliente y servidor se comunican directamente para evitar cuellos de botella.

### 2 Paso de Mensajes (Publish/Subscribe)

En lugar de invocaciones remotas:

* Los clientes publican mensajes en una cola.
* Los servidores suscritos procesan los mensajes.
* Se utiliza un modelo asincrónico.

---

##  Invocaciones

### 🔹 Síncronas

El cliente espera la respuesta antes de continuar.

### 🔹 Asíncronas

El cliente continúa ejecutándose y recibe la respuesta posteriormente (normalmente mediante colas o buffers).

---

##  Ventajas

*  Transparencia de localización
*  Desacoplamiento entre componentes
*  Escalabilidad potencial
*  Fácil extensibilidad
*  Sustitución sencilla de implementaciones
*  Portabilidad entre plataformas
*  Reusabilidad de servicios

---

##  Desventajas

*  Posible pérdida de eficiencia
*  Punto único de fallo (si existe un solo broker)
*  Mayor complejidad en pruebas y depuración
*  Mayor esfuerzo de diseño (interfaces y modelo de objetos)

---

##  Consideraciones de Diseño

Para implementar correctamente el patrón Broker es necesario:

* Definir interfaces claras y bien diseñadas.
* Especificar tipos de datos intercambiados.
* Diseñar correctamente el modelo de errores.
* Elegir el mecanismo de comunicación:

  * IDL (Interface Definition Language)
  * Protocolos nativos
* Definir si las invocaciones serán síncronas o asíncronas.
* Evaluar tolerancia a fallos (múltiples brokers, balanceadores).

---

##  Casos de Uso

* Sistemas distribuidos empresariales.
* Arquitecturas basadas en microservicios.
* Plataformas con plugins desacoplados.
* Sistemas que requieren escalabilidad dinámica.
* Arquitecturas basadas en colas de mensajes.

---

##  Comparación con Arquitectura Monolítica

| Monolito                          | Broker                         |
| --------------------------------- | ------------------------------ |
| Componentes fuertemente acoplados | Componentes desacoplados       |
| Difícil escalabilidad             | Escalabilidad más flexible     |
| Difícil reutilización             | Alta reutilización             |
| Actualizaciones complejas         | Actualizaciones independientes |

---

##  Conclusión

El patrón Broker es una solución arquitectónica fundamental para sistemas distribuidos modernos. Aunque introduce mayor complejidad en el diseño y la infraestructura, proporciona una base sólida para construir sistemas escalables, extensibles y desacoplados.

---

ejemplo de proyecto en Java con Spring Boot + RabbitMQ usando Gradle

 Estructura del repositorio

 build.gradle

 Todas las clases necesarias

 Docker Compose opcional para levantar RabbitMQ



Estructura del repositorio

```text

spring-boot-rabbitmq-demo/
│
├── build.gradle
├── settings.gradle
├── docker-compose.yml
├── README.md
│
└── src/
    └── main/
        ├── java/
        │   └── com/example/rabbitdemo/
        │       ├── RabbitDemoApplication.java
        │       ├── config/
        │       │   └── RabbitMQConfig.java
        │       ├── controller/
        │       │   └── MessageController.java
        │       ├── producer/
        │       │   └── MessageProducer.java
        │       ├── consumer/
        │       │   └── MessageConsumer.java
        │       └── model/
        │           └── MessageDTO.java
        │
        └── resources/
            └── application.yml

build.gradle

plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.5'
    id 'io.spring.dependency-management' version '1.1.4'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'

java {
    sourceCompatibility = '17'
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-amqp'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
    useJUnitPlatform()
}

 settings.gradle
rootProject.name = 'spring-boot-rabbitmq-demo'

application.yml

spring:
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest

Clase principal

RabbitDemoApplication.java
package com.example.rabbitdemo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class RabbitDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(RabbitDemoApplication.class, args);
    }
}

Configuración RabbitMQ

RabbitMQConfig.java
package com.example.rabbitdemo.config;

import org.springframework.amqp.core.*;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class RabbitMQConfig {

    public static final String QUEUE = "demo.queue";
    public static final String EXCHANGE = "demo.exchange";
    public static final String ROUTING_KEY = "demo.routingkey";

    @Bean
    public Queue queue() {
        return new Queue(QUEUE);
    }

    @Bean
    public TopicExchange exchange() {
        return new TopicExchange(EXCHANGE);
    }

    @Bean
    public Binding binding(Queue queue, TopicExchange exchange) {
        return BindingBuilder
                .bind(queue)
                .to(exchange)
                .with(ROUTING_KEY);
    }
}

DTO del mensaje
MessageDTO.java
package com.example.rabbitdemo.model;

public class MessageDTO {

    private String content;

    public MessageDTO() {}

    public MessageDTO(String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
    }
}

  Producer
MessageProducer.java
package com.example.rabbitdemo.producer;

import com.example.rabbitdemo.config.RabbitMQConfig;
import com.example.rabbitdemo.model.MessageDTO;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.stereotype.Service;

@Service
public class MessageProducer {

    private final RabbitTemplate rabbitTemplate;

    public MessageProducer(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    public void sendMessage(MessageDTO message) {
        rabbitTemplate.convertAndSend(
                RabbitMQConfig.EXCHANGE,
                RabbitMQConfig.ROUTING_KEY,
                message
        );
    }
}

 Consumer
MessageConsumer.java
package com.example.rabbitdemo.consumer;

import com.example.rabbitdemo.config.RabbitMQConfig;
import com.example.rabbitdemo.model.MessageDTO;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Service;

@Service
public class MessageConsumer {

    @RabbitListener(queues = RabbitMQConfig.QUEUE)
    public void receiveMessage(MessageDTO message) {
        System.out.println("Mensaje recibido: " + message.getContent());
    }
}

 Controller REST
MessageController.java
package com.example.rabbitdemo.controller;

import com.example.rabbitdemo.model.MessageDTO;
import com.example.rabbitdemo.producer.MessageProducer;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/messages")
public class MessageController {

    private final MessageProducer producer;

    public MessageController(MessageProducer producer) {
        this.producer = producer;
    }

    @PostMapping
    public String sendMessage(@RequestBody MessageDTO message) {
        producer.sendMessage(message);
        return "Mensaje enviado a RabbitMQ!";
    }
}

 docker-compose.yml (Opcional)
version: '3.8'

services:
  rabbitmq:
    image: rabbitmq:3-management
    container_name: rabbitmq
    ports:
      - "5672:5672"
      - "15672:15672"


Panel web:

http://localhost:15672
usuario: guest
password: guest


# Spring Boot RabbitMQ Demo

Ejemplo simple de integración entre Spring Boot y RabbitMQ usando Gradle.

## Tecnologías

- Java 17
- Spring Boot 3
- RabbitMQ
- Gradle

## Ejecutar RabbitMQ

```bash
docker-compose up -d


Panel:
http://localhost:15672

Ejecutar la aplicación
./gradlew bootRun

Enviar mensaje

POST:

http://localhost:8080/api/messages


Body:

{
  "content": "Hola RabbitMQ!"
}


El mensaje será consumido automáticamente y mostrado en consola.


---

#  Resultado

Este proyecto:

✔ Expone un endpoint REST  
✔ Envía mensajes a RabbitMQ  
✔ Consume automáticamente desde una cola  
✔ Está listo para producción básica  
✔ Usa Gradle  
✔ Compatible con Java 17


Incluye:

Dockerfile optimizado multi-stage

Docker Compose con red interna

Variables de entorno

Healthcheck

Espera automática de RabbitMQ

Persistencia de datos

Estructura del repositorio

spring-boot-rabbitmq-docker/
│
├── build.gradle
├── settings.gradle
├── Dockerfile
├── docker-compose.yml
├── .dockerignore
├── README.md
│
└── src/
    └── main/
        ├── java/
        │   └── com/example/rabbitdemo/
        │       ├── RabbitDemoApplication.java
        │       ├── config/RabbitMQConfig.java
        │       ├── controller/MessageController.java
        │       ├── producer/MessageProducer.java
        │       ├── consumer/MessageConsumer.java
        │       └── model/MessageDTO.java
        │
        └── resources/
            └── application.yml

Dockerfile (Multi-Stage Optimizado)
# ---------- Stage 1: Build ----------
FROM gradle:8.5-jdk17-alpine AS builder
WORKDIR /app
COPY . .
RUN gradle clean bootJar --no-daemon

# ---------- Stage 2: Runtime ----------
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

COPY --from=builder /app/build/libs/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]

docker-compose.yml
version: "3.9"

services:

  rabbitmq:
    image: rabbitmq:3-management
    container_name: rabbitmq
    restart: always
    ports:
      - "5672:5672"
      - "15672:15672"
    environment:
      RABBITMQ_DEFAULT_USER: admin
      RABBITMQ_DEFAULT_PASS: admin
    volumes:
      - rabbitmq_data:/var/lib/rabbitmq
    healthcheck:
      test: ["CMD", "rabbitmq-diagnostics", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

  app:
    build: .
    container_name: spring-rabbit-app
    restart: always
    depends_on:
      rabbitmq:
        condition: service_healthy
    ports:
      - "8080:8080"
    environment:
      SPRING_RABBITMQ_HOST: rabbitmq
      SPRING_RABBITMQ_PORT: 5672
      SPRING_RABBITMQ_USERNAME: admin
      SPRING_RABBITMQ_PASSWORD: admin
    networks:
      - app-network

volumes:
  rabbitmq_data:

networks:
  app-network:

⚙ application.yml (Optimizado para Docker)
spring:
  rabbitmq:
    host: ${SPRING_RABBITMQ_HOST:localhost}
    port: ${SPRING_RABBITMQ_PORT:5672}
    username: ${SPRING_RABBITMQ_USERNAME:guest}
    password: ${SPRING_RABBITMQ_PASSWORD:guest}
  jackson:
    serialization:
      INDENT_OUTPUT: true

server:
  port: 8080

.dockerignore
build/
.gradle/
.git/
.gitignore
README.md

Cómo ejecutar todo

Desde la raíz del proyecto:

docker-compose up --build

 Accesos

Aplicación
http://localhost:8080

RabbitMQ Management UI
http://localhost:15672
usuario: admin
password: admin

 Probar envío de mensaje
curl -X POST http://localhost:8080/api/messages \
-H "Content-Type: application/json" \
-d '{"content":"Hola desde Docker"}'


Verás el mensaje en los logs del contenedor:

docker logs spring-rabbit-app


# Spring Boot RabbitMQ Dockerized

Proyecto completo dockerizado con:

- Spring Boot 3
- RabbitMQ
- Gradle
- Docker Multi-stage
- Healthchecks
- Persistencia de datos

## Ejecutar

docker-compose up --build

## Endpoints

POST http://localhost:8080/api/messages

Body:

{
  "content": "Hola Docker"
}

## RabbitMQ UI

http://localhost:15672
admin / admin

 Arquitectura resultante
┌─────────────────────┐
│   Cliente REST      │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│ Spring Boot App     │
│  Producer + Consumer│
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│      RabbitMQ       │
│ Exchange + Queue    │
└─────────────────────┘

Qué tiene esta versión

✔ Multi-stage build (imagen ligera)
✔ RabbitMQ persistente
✔ Healthchecks
✔ Espera automática del broker
✔ Variables de entorno
✔ Red interna docker
✔ Lista para CI/CD

---

## 1. ¿Qué es y qué hace un Message Broker (como RabbitMQ)?

Es un servidor que recibe mensajes, los pone en una fila (**cola**) y los distribuye.

### Sus funciones principales:

1. **Ruteo:** Decidir a qué cola va cada mensaje (como un cartero inteligente).
2. **Almacenamiento:** Guarda el mensaje si el consumidor está offline para que no se pierda.
3. **Desacoplamiento:** Permite que dos partes de un sistema no dependan directamente entre sí.
4. **Escalabilidad:** Si hay muchos mensajes, puedes poner a "muchos conejos" (consumidores) a trabajar sobre la misma cola para vaciarla más rápido.

---

## 2. Comparativa: RabbitMQ vs. El resto

El texto menciona que, aunque RabbitMQ es un estándar sólido, compite con otros gigantes:

| Herramienta     | Tipo           | Ideal para...                                                         | 
|                 |                |                                                                       |
| **RabbitMQ**    | Message Broker | Ruteo complejo, sistemas que necesitan confirmación de entrega (ACK). |
| **Kafka**       | Message Bus    | Volúmenes masivos de datos (Big Data) y persistencia a largo plazo.   |
| **AWS SQS**     | Managed Queue  | Si ya estás en Amazon y quieres algo simple y "serverless".           |
| **AWS Kinesis** | Data Stream    | Procesamiento de datos en tiempo real a gran escala.                  |

---

## 3. Conceptos clave para no perderse

* **AMQP:** Es el protocolo binario (idioma) que usa RabbitMQ por defecto. Es eficiente y rápido.
* **Productor:** El que envía el mensaje.
* **Consumidor:** El que recibe y procesa el mensaje.
* **Dead Letter Queue (DLQ):** Es la "caja de objetos perdidos". Si un mensaje falla 10 veces y nadie puede procesarlo, va aquí para que un humano lo revise y el sistema no se trabe en un bucle infinito.
* **ACK (Acknowledgment):** Es el "recibido". El consumidor le dice a Rabbit: "Ya lo leí, podés borrarlo".

> **Dato curioso del texto:** RabbitMQ está programado en **Erlang**, un lenguaje funcional diseñado para que las cosas no se caigan nunca (muy usado en telecomunicaciones).

---

