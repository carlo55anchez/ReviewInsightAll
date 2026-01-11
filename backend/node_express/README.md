# 🧠 **Review Insight – Back-End - Node-Express**

**API de análisis de sentimiento basada en Machine Learning** _(Node.js + FastAPI Bridge + Dual Database)_

---

## 📌 DESCRIPCIÓN GENERAL

Este proyecto implementa una API de análisis de sentimiento que permite:

- Analizar texto individual
- Analizar múltiples textos por lote
- Obtener sentimiento, etiqueta numérica y nivel de confianza
- Actuar como orquestador entre el cliente y el microservicio de inferencia
- **Persistencia dual automática** en Firebase Firestore y MongoDB Atlas
- Exportación histórica de análisis con redundancia de datos
- Sincronización automática entre bases de datos

La API funciona como un puente de alto rendimiento que consume un modelo de Machine Learning alojado en un microservicio independiente de FastAPI, garantizando alta disponibilidad mediante persistencia redundante.

---

## 🧱 ARQUITECTURA

### **Node.js (Express) - Orquestador Principal**

- Expone endpoints HTTP
- Valida entradas y gestiona la seguridad
- Realiza peticiones asíncronas al microservicio de IA
- Coordina persistencia dual (Firebase + MongoDB)
- Devuelve respuestas normalizadas en JSON

### **FastAPI (Python) - Motor de IA**

- Mantiene el modelo Scikit-learn cargado en memoria
- Realiza predicciones en tiempo real
- Procesa la lógica multiclase (Positivo, Negativo, Neutro)

### **Sistema de Persistencia Dual**

- **Firebase Firestore**: Base de datos principal para operaciones en tiempo real
- **MongoDB Atlas**: Base de datos de respaldo para alta disponibilidad y exportación
- **Sincronización automática**: Garantiza coherencia entre ambas bases

---

## 📂 ESTRUCTURA DEL PROYECTO

---

```
/
├── config/
│   ├── firebaseConfig.js
│   │   Configuración e interfaz de conexión con Firebase Firestore
│   └── mongoConfig.js
│       Configuración e interfaz de conexión con MongoDB
│
├── server.js
│   Servidor Express (API principal y orquestador)
│
├── serviceAccountKey.json
│   Credenciales de seguridad de Firebase (Ignorado en Git)
│
├── Dockerfile
│   Configuración de contenedor para despliegue en Node.js
│
├── package.json
│   Dependencias y scripts de Node.js
│
├── package-lock.json
│   Bloqueo de versiones de dependencias
│
├── .gitignore
│   Archivos y carpetas ignorados por Git
│
├── node_modules/
│   Dependencias de Node
│
└── README.md
    Este documento

```

---

## 🧠 MODELO DE MACHINE LEARNING

- **Tipo:** Pipeline de Scikit-learn (Logistic Regression)
- **Clasificación:** Ternaria balanceada
- **Ubicación:** Alojado externamente en un microservicio dedicado para optimizar recursos

**Mapeo de etiquetas:**

- `0` → Negativo
- `1` → Positivo
- `3` → Neutro

---

## 🔄 FLUJO DE DATOS CON PERSISTENCIA DUAL

```

Cliente → API Node.js → FastAPI (ML) → [Predicción] → 📊 Resultado
↓
🔥 Firebase Firestore (Principal)
🍃 MongoDB Atlas (Respaldo)
↓
📤 Respuesta al Cliente

```

### **Características de la Persistencia Dual:**

1. **Escritura en paralelo**: Cada análisis se guarda simultáneamente en ambas bases
2. **Recuperación inteligente**: El endpoint de exportación intenta primero Firebase, luego MongoDB
3. **Sincronización automática**: Endpoint `/admin/sync` para reconciliar datos faltantes
4. **Resistencia a fallos**: Si una base falla, el sistema continúa operando con la otra

---

## 🚀 ENDPOINTS DISPONIBLES

### **GET** `/health`

**Health check de la API con estado de bases de datos**

**Respuesta:**

```json
{
  "status": "ok",
  "message": "Sentiment API is running (FastAPI Bridge with Dual Storage)"
}
```

---

### **POST** `/sentiment`

**Análisis de sentimiento individual con persistencia dual**

**Body:**

```json
{
  "text": "El hotel estaba muy limpio y el personal fue muy amable"
}
```

**Respuesta:**

```json
{
  "success": true,
  "data": {
    "text": "El hotel estaba muy limpio y el personal fue muy amable",
    "sentiment": "Positivo",
    "label": 1,
    "confidence": 0.93
  },
  "storage": {
    "firebase": "saved",
    "mongodb": "saved"
  }
}
```

✅ **Persistencia automática** en ambas bases de datos

---

### **POST** `/sentiment/batch`

**Análisis de sentimiento por lotes con persistencia dual**

**Body:**

```json
{
  "texts": [
    "Música y gritos hasta las 6 am",
    "Desayuno variado",
    "Cumple con lo básico"
  ]
}
```

**Respuesta:**

```json
{
  "success": true,
  "data": [
    {
      "text": "Música y gritos hasta las 6 am",
      "label": 0,
      "sentiment": "Negativo",
      "confidence": 0.88
    },
    {
      "text": "Desayuno variado",
      "label": 1,
      "sentiment": "Positivo",
      "confidence": 0.95
    },
    {
      "text": "Cumple con lo básico",
      "label": 3,
      "sentiment": "Neutro",
      "confidence": 0.62
    }
  ],
  "storage": {
    "firebase": "saved",
    "mongodb": "saved"
  }
}
```

**Límite:** máximo 100 textos por request

---

### **GET** `/sentiment/export`

**Exportación del historial acumulado con redundancia**

**Características:**

- Intenta recuperar datos primero desde **Firebase**
- En caso de error, fallback automático a **MongoDB**
- Datos ordenados por timestamp descendente
- Estructura uniforme independientemente de la fuente

**Respuesta:**

```json
{
  "success": true,
  "count": 150,
  "source": "firebase", // o "mongodb" si fue respaldo
  "data": [
    {
      "id": "doc_id_123",
      "text": "Excelente servicio",
      "sentiment": "Positivo",
      "confidence": 0.98,
      "mode": "single",
      "timestamp": "2024-01-15T10:30:00.000Z"
    }
  ]
}
```

---

### **GET** `/admin/sync` _(NUEVO)_

**Sincronización global entre bases de datos**

**Funcionalidad:**

- Detecta registros faltantes en cada base
- Sincroniza bidireccionalmente
- Logs detallados del proceso

**Respuesta:**

```json
{
  "success": true,
  "message": "Sincronización completada",
  "syncedToFirebase": 5,
  "syncedToMongo": 3
}
```

---

## ⚙️ REQUISITOS

**Node.js**

- Node 18 o superior (Soporte nativo para Fetch API)

**Bases de Datos**

- **Firebase Firestore**: Cuenta activa con Firestore habilitado
- **MongoDB Atlas**: Clúster configurado o instancia local de MongoDB
- Archivo de credenciales `serviceAccountKey.json` en la raíz del proyecto
- URI de conexión de MongoDB en `mongoConfig.js`

**Conectividad**

- Acceso al microservicio de inferencia (FastAPI) debidamente configurado en la variable `FASTAPI_URL`
- Conexión a Internet para servicios en la nube (Firebase y MongoDB Atlas)

---

## ▶️ EJECUCIÓN DEL PROYECTO

1. **Instalar dependencias:**

   ```bash
   npm install
   ```

2. **Configurar bases de datos:**

   - Configurar URI de MongoDB en `mongoConfig.js`
   - Verificar que `serviceAccountKey.json` esté en la raíz

3. **Iniciar servidor:**

   ```bash
   node server.js
   ```

4. **Verificar conexiones:**
   ```
   🚀 Sentiment API corriendo en http://localhost:7860
   📡 Conectado a FastAPI en http://127.0.0.1:8000
   🔥 Firebase conectado exitosamente
   🍃 MongoDB conectado exitosamente (Respaldo)
   ```

La API corre por defecto en:  
**http://localhost:7860**

---

## 🗄️ CONFIGURACIÓN DE BASES DE DATOS

### **Firebase Firestore (Principal)**

```javascript
// En firebaseConfig.js
const serviceAccount = require("./serviceAccountKey.json");
// Configuración automática al iniciar
```

### **MongoDB Atlas (Respaldo)**

```javascript
// En mongoConfig.js
const uri = "mongodb+srv://usuario:contraseña@cluster.mongodb.net/";
const dbName = "review_insight_db";
const collectionName = "reviews_history";
```

### **Estructura de Datos Común**

Ambas bases almacenan documentos con esta estructura:

```json
{
  "text": "Texto analizado",
  "sentiment": "Positivo|Negativo|Neutro",
  "confidence": 0.95,
  "mode": "single|batch",
  "timestamp": "2024-01-15T10:30:00.000Z"
}
```

---

## 🔐 SEGURIDAD Y CONSIDERACIONES

- **Desacoplamiento total** entre el backend y el modelo de ML
- **Validación estricta** de tipos de entrada
- **Persistencia redundante** para alta disponibilidad
- **Gestión de errores** robusta en comunicación entre servicios
- **Límite de batch** para prevenir abusos de procesamiento
- **Almacenamiento seguro** de credenciales mediante archivos ignorados por control de versiones
- **Persistencia asíncrona** para no penalizar el tiempo de respuesta de la API
- **Sincronización automática** para garantizar coherencia de datos

---

## 🧪 USOS TÍPICOS

- Dashboards de reputación hotelera con datos redundantes
- Clasificación de feedback en tiempo real con alta disponibilidad
- Procesamiento masivo de reseñas históricas con respaldo garantizado
- Integración con interfaces de usuario modernas (Vite + React)
- Sistemas donde la disponibilidad de datos es crítica

---

## 📈 VENTAJAS DE LA ARQUITECTURA DUAL

### **🔥 Firebase Firestore**

- ✅ Tiempo real y escalabilidad automática
- ✅ Fácil integración con aplicaciones móviles/web
- ✅ Reglas de seguridad granulares

### **🍃 MongoDB Atlas**

- ✅ Flexibilidad en consultas complejas
- ✅ Mayor control sobre índices y rendimiento
- ✅ Exportación masiva eficiente

### **🔄 Sistema Combinado**

- ✅ **Alta disponibilidad**: Si una base falla, la otra responde
- ✅ **Respaldo automático**: Datos duplicados sin intervención manual
- ✅ **Flexibilidad de migración**: Fácil transición entre tecnologías
- ✅ **Rendimiento optimizado**: Cada base para su caso de uso ideal

---

## 📌 ESTADO DEL PROYECTO

- ✔ **Funcional** con persistencia dual
- ✔ **Producción-ready** con alta disponibilidad
- ✔ **Arquitectura moderna** de microservicios
- ✔ **Alta disponibilidad** y escalabilidad
- ✔ **Redundancia de datos** garantizada
- ✔ **Sincronización automática** entre bases

---

## ✍️ AUTOR

API de análisis de sentimiento desarrollada con arquitectura desacoplada y persistencia redundante  
Node.js (Express) + FastAPI (Inferencia) + Firebase Firestore + MongoDB Atlas
