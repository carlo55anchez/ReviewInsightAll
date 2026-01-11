# 🧠 **Review Insight – Back-End - Java-Spring Boot**

Back-End desarrollado en **Spring Boot** para el análisis de sentimiento de reseñas de texto, con persistencia dual en **Firebase Firestore** y **MongoDB Atlas**, y comunicación con un **microservicio FastAPI** encargado del modelo de Machine Learning.

---

## 🚀 Funcionalidades Principales

- 🔍 **Análisis de sentimiento individual**
- 📦 **Análisis de sentimiento por lotes (batch)**
- 🔗 **Integración con FastAPI (modelo ML externo)**
- 🔥 **Persistencia automática en Firebase Firestore** (Base de Datos Principal)
- 🍃 **Persistencia redundante en MongoDB Atlas** (Base de Datos Respaldo)
- 📤 **Exportación del historial de análisis desde ambas bases**
- 🩺 **Health check del servicio**
- 🌐 **CORS habilitado para consumo desde Front-End**
- 🔄 **Sincronización automática entre bases de datos**

---
## 🏗️ Arquitectura General

El proyecto sigue una arquitectura **Controller → Service → External Services / DB**, manteniendo responsabilidades claras y código desacoplado, con persistencia redundante en dos bases de datos diferentes.

```
review-insight
├── src
│ ├── main
│ │ ├── java
│ │ │ └── com.reviewinsight
│ │ │ ├── config
│ │ │ │ ├── FirebaseConfig.java
│ │ │ │ └── MongoConfig.java
│ │ │ ├── controller
│ │ │ │ └── SentimentController.java
│ │ │ ├── model
│ │ │ │ ├── ReviewEntity.java      # ✅ Modelo MongoDB
│ │ │ │ ├── ReviewRecord.java      # ✅ Modelo Firebase
│ │ │ │ ├── SentimentRequest.java
│ │ │ │ └── SentimentResponse.java
│ │ │ ├── repository
│ │ │ │ └── ReviewRepository.java  # ✅ Repositorio MongoDB
│ │ │ ├── service
│ │ │ │ ├── FirebaseService.java   # ✅ Servicio Firebase
│ │ │ │ ├── MongoService.java      # ✅ Servicio MongoDB
│ │ │ │ └── SentimentService.java
│ │ │ └── ReviewInsightApplication.java
│ │ └── resources
│ │ ├── application.properties
│ │ └── serviceAccountKey.json
│ └── test
├── Dockerfile
├── pom.xml
└── README.md
```
---

## 🗄️ Sistema de Persistencia Dual

### 🔥 Firebase Firestore (Base de Datos Principal)
- **Inicialización automática** al arrancar la app
- **Uso de credenciales** desde `serviceAccountKey.json`
- **Colección:** `reviews_history`
- **Propósito:** Base de datos principal para operaciones en tiempo real

### 🍃 MongoDB Atlas (Base de Datos Respaldo)
- **Conexión automática** al clúster Atlas/Local
- **Configuración mediante Spring Data MongoDB**
- **Colección:** `reviews_history`
- **Propósito:** Respaldo redundante y exportación de datos

### 📊 Estructura de Datos Común
Cada análisis se guarda en **ambas bases de datos** con esta estructura:

```json
{
  "id": "identificador_unico",
  "text": "Texto analizado",
  "sentiment": "Sentimiento detectado",
  "confidence": 0.95,
  "mode": "single|batch",
  "timestamp": "2024-01-15T10:30:00"
}
```

---

## 🔗 Integración con FastAPI (Machine Learning)

El Back-End **no ejecuta el modelo de ML**, sino que actúa como **puente** hacia un microservicio FastAPI.

### 🧠 FastAPI Endpoints consumidos
- `POST /predict`
- `POST /predict/batch`

La URL se configura mediante:

```properties
fastapi.url=http://localhost:8000
```

---

## 📡 Endpoints Disponibles

---

### 🩺 Health Check
**GET `/health`**

📥 **Response:**
```json
{
  "status": "ok",
  "message": "Sentiment API is running (FastAPI Bridge)",
  "databases": {
    "firebase": "connected",
    "mongodb": "connected"
  }
}
```

---

### 🔍 Análisis de Sentimiento Individual
**POST `/sentiment`**

📤 **Request:**
```json
{
  "text": "El hotel fue excelente"
}
```

📥 **Response:**
```json
{
  "success": true,
  "data": {
    "text": "El hotel fue excelente",
    "sentiment": "positive",
    "label": "POS",
    "confidence": 0.97
  },
  "storage": {
    "firebase": "saved",
    "mongodb": "saved"
  }
}
```

✔ Guarda automáticamente el resultado en **Firestore y MongoDB**.

---

### 📦 Análisis de Sentimiento por Lotes
**POST `/sentiment/batch`**

📤 **Request:**
```json
{
  "texts": [
    "Excelente servicio",
    "Muy mala atención"
  ]
}
```

📥 **Response:**
```json
{
  "success": true,
  "data": [
    {
      "text": "Excelente servicio",
      "sentiment": "positive",
      "confidence": 0.95
    },
    {
      "text": "Muy mala atención",
      "sentiment": "negative",
      "confidence": 0.92
    }
  ],
  "storage": {
    "firebase": "saved",
    "mongodb": "saved"
  }
}
```

📌 **Límite:** máximo **100 textos** por request.

---

### 📤 Exportar Historial
**GET `/sentiment/export`**



📥 **Response:**
```json
{
  "success": true,
  "count": 25,
  "source": "mongodb",
  "data": [
    {
      "id": "...",
      "text": "...",
      "sentiment": "...",
      "confidence": 0.88,
      "timestamp": "...",
      "mode": "single"
    }
  ]
}
```
✔ Resultados ordenados por **fecha descendente**.
✔ Recuperados desde **MongoDB Atlas** para mejor rendimiento.
---

### 🔄 Sincronización de Bases de Datos
**GET `/sentiment/sync`**

Endpoint administrativo para forzar la integridad de datos entre las nubes.

📥 **Response:**
```json
{
  "success": true,
  "message": "Sincronización completada",
  "syncedToFirebase": 0,
  "syncedToMongo": 1
}
```
✔ **Detecta discrepancias:** Compara registros entre Firestore y MongoDB mediante identificadores únicos.
✔ **Autocuración:** Si un registro existe en una base pero no en la otra, el servicio lo clona automáticamente para restaurar el espejo.

---

## 🧩 Modelos de Datos

---

### 🗂 ReviewRecord
Representa un análisis almacenado en **Firestore**.
**Paquete:** `model.com.reviewinsight.ReviewRecord`

---

### 📂 ReviewEntity
Representa un análisis almacenado en **MongoDB**.
**Paquete:** `model.com.reviewinsight.ReviewEntity`

**Características:**
- Anotado con `@Document(collection = "reviews_history")`
- Usa `@Id` para el identificador MongoDB
- Campos mapeados con `@Field`
- Incluye soporte para `LocalDateTime`

---

### 📥 SentimentRequest
- Validación automática con **@NotBlank**
- Soporte para **requests individuales y batch**

---

## ⚙️ Tecnologías Utilizadas

- ☕ Java 21 (LTS)
- 🌱 Spring Boot 4.0.1
- 🔥 Firebase Admin SDK
- 🗄 Firestore
- 🍃 Spring Data MongoDB
- 🌐 RestTemplate
- 🐍 FastAPI (externo)
- 🐳 Docker
- 📦 Lombok

---

## 🔧 Configuración

### application.properties
```properties
# FastAPI Configuration
fastapi.url=http://localhost:8000

# MongoDB Configuration
spring.data.mongodb.uri=mongodb+srv://<username>:<password>@cluster.mongodb.net/review_insight
spring.data.mongodb.database=review_insight

# Firebase Configuration
firebase.database.url=https://My-project.firebaseio.com

#⚠️ Nota: Es necesario incluir el archivo serviceAccountKey.json en src/main/resources/ para la conexión con Firebase.
```

### Dependencias clave (pom.xml)
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
<dependency>
    <groupId>com.google.firebase</groupId>
    <artifactId>firebase-admin</artifactId>
    <version>9.2.0</version>
</dependency>
```
---

## 🐳 Docker
Incluye **Dockerfile** para facilitar el despliegue en contenedores o plataformas cloud.

```dockerfile
FROM openjdk:17-jdk-slim
# ... configuración del contenedor
```
---

## 📊 Flujo de Datos

1. Cliente → POST /sentiment
2. Spring Boot → FastAPI (ML Model)
3. FastAPI → Predicción de Sentimiento
4. Spring Boot → Guarda en Firebase (Principal)
5. Spring Boot → Guarda en MongoDB (Respaldo)
6. → Retorna respuesta al Cliente

---

## 🔐 Consideraciones de Seguridad

- ✅ **Validación de entrada** en todos los endpoints
- ✅ **Límite de batch** para prevenir abusos
- ✅ **Conexiones seguras** a ambas bases de datos
- ✅ **Credenciales externas** en archivos de configuración
- ✅ **CORS configurado** para dominios específicos

---

## 📈 Escalabilidad y Resiliencia

- **Arquitectura desacoplada** permite cambiar el modelo ML fácilmente.
- **Persistencia dual** asegura alta disponibilidad.
- **Separación de responsabilidades** entre servicios.
- **Capacidad de migración** entre bases de datos.

[!TIP]
## 🛡️ Mecanismo de Resiliencia
- El sistema implementa un esquema de **failover pasivo**: si una de las bases de datos no está disponible o falla la autenticación, la operación de análisis continúa en la base secundaria. El error se registra en los logs del servidor, pero el flujo principal permanece activo, garantizando que el usuario siempre reciba su respuesta.

---

## 🧪 Nuevas Funcionalidades (Actualización)

### ✅ **Persistencia Redundante en MongoDB**
- Configuración automática con `MongoConfig.java`
- Repositorio Spring Data: `ReviewRepository.java`
- Servicio especializado: `MongoService.java`

### ✅ **Exportación Optimizada**
- Recuperación desde MongoDB para mejor performance
- Ordenamiento por timestamp descendente
- Estructura de datos uniforme

### ✅ **Monitoreo de Conexiones**
- Verificación automática de conexión a ambas bases
- Logs detallados de estado de conexión
- Manejo de errores individualizado por base de datos

---

## 📌 Notas Finales

Este Back-End está diseñado para:
- 📈 **Escalar fácilmente** con arquitectura modular
- 🧠 **Mantener desacoplado** el modelo de Machine Learning
- 🗄️ **Garantizar disponibilidad** con persistencia dual
- 🌐 **Servir como una API robusta** para Front-End web o desktop
- 🔄 **Facilitar migraciones** entre sistemas de persistencia

---

✨ **Review Insight** – Inteligencia de sentimiento con persistencia redundante al servicio de tus datos.

