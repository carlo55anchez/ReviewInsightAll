# 🧠 **Review Insight – Front-End - React-Vite-Tailwind**

# 🎯 Clasificador de Sentimiento - Hotel Reviews

> Aplicación web para clasificación binaria de sentimientos en reseñas de hoteles en español.

---

## 📖 Descripción General

Esta aplicación forma parte de un proyecto de **Hackathon** que implementa un sistema de clasificación de sentimientos para reseñas de hoteles en **español y portugués**.  
El sistema utiliza técnicas de **Procesamiento de Lenguaje Natural (NLP)** para determinar si una reseña expresa un sentimiento **Positivo**, **Negativo** o **Neutro**, a partir de un modelo entrenado con datos bilingües balanceados.

---

## 🛠️ Tecnologías Utilizadas

### Frontend

| Tecnología      | Versión | Descripción               |
| --------------- | ------- | ------------------------- |
| ⚛️ React        | 18.3.1  | Biblioteca de UI          |
| ⚡ Vite         | -       | Build tool y dev server   |
| 🎨 Tailwind CSS | -       | Framework de estilos      |
| 📘 TypeScript   | -       | Tipado estático           |
| 🧩 shadcn/ui    | -       | Componentes de UI         |
| 🔔 Sonner       | 1.7.4   | Sistema de notificaciones |

---

## 🎨 Características Actuales

### Interfaz de Usuario

- **📝 Área de texto:** Campo para ingresar o pegar reseñas de hoteles (Soporta modo individual y por lotes).
- **🔄 Selector de Modo:** Permite alternar entre análisis simple o procesamiento masivo de reseñas.
- **📥 Botón de Exportación:** Descarga el historial acumulado de la base de datos en formato CSV.
- **🔘 Botón de análisis:** Dispara el proceso de clasificación según el modo seleccionado.
- **📊 Tarjeta de resultados:** Muestra el sentimiento detectado con:
  - 😊 Emoji indicador (Positivo/Negativo/Neutro)
  - 🏷️ Etiqueta de sentimiento (Clasificación Ternaria)
  - 📈 Porcentaje de confianza
  - 📊 Barra de progreso visual con gradientes dinámicos

### Funcionalidades de Datos

- **📦 Procesamiento por Lotes:** Análisis simultáneo de múltiples reseñas (separadas por saltos de línea).
- **💾 Persistencia en la Nube:** Guardado automático de cada análisis en Firebase Firestore.
- **📑 Reportes ReviewInsight:** Generación de archivos CSV con marca de tiempo y metadatos de análisis.

### Estados de la Aplicación

| Estado         | Descripción                          | Indicador Visual              |
| -------------- | ------------------------------------ | ----------------------------- |
| 🔵 **Idle**    | Formulario vacío, listo para entrada | Textarea habilitado           |
| 🟡 **Loading** | Procesando análisis                  | Spinner + botón deshabilitado |
| 🟢 **Success** | Resultado obtenido                   | Tarjeta con resultado         |
| 🔴 **Error**   | Fallo en el análisis                 | Mensaje de error              |

### Diseño Visual

- 🌙 **Tema oscuro** con paleta mediterránea
- ✨ **Acentos dorados/ámbar** para destacar elementos
- 🟢 **Verde** para resultados positivos
- 🔴 **Rojo** para resultados negativos
- 🟡**Amarillo/Ámbar** para resultados neutros
- 🪟 **Efecto glass-card** con transparencias
- 🎭 **Animaciones suaves** en transiciones

---

## 🚀 Instalación y Ejecución

```bash
# 1. Clonar el repositorio
git clone <URL_DEL_REPOSITORIO>

# 2. Navegar al directorio
cd clasificador-sentimiento

# 3. Instalar dependencias
npm install

# 4. Ejecutar en modo desarrollo
npm run dev

# 5. Abrir en el navegador
# http://localhost:5173
```

---

## 📁 Estructura del Proyecto

```
ReviewInsight/
├── .vscode/                   # Configuración del editor
│   └── settings.json
│
├── node_modules/               # Dependencias (no versionar)
│
├── public/
│   └── Review.ico              # Ícono de la aplicación
│
├── src/
│   ├── components/
│   │   ├── analyzer/           # Subcomponentes del análisis (Refactorización)
│   │   │   ├── AnalysisError.tsx    # Visualización de estados de error
│   │   │   ├── AnalysisInput.tsx    # Áreas de texto y botones de acción
│   │   │   ├── BatchResultsTable.tsx # Tabla de resultados por lotes
│   │   │   └── ModeSelector.tsx     # Cambio entre modo Single y Batch
│   │   │
│   │   ├── ui/                 # Componentes shadcn/ui
│   │   │   ├── button.tsx
│   │   │   ├── card.tsx
│   │   │   ├── textarea.tsx
│   │   │   ├── sonner.tsx
│   │   │   ├── toast.tsx
│   │   │   ├── toaster.tsx
│   │   │   ├── tooltip.tsx
│   │   │   └── use-toast.ts
│   │   │
│   │   ├── LoadingSpinner.tsx  # Indicador de carga
│   │   ├── ResultCard.tsx      # Tarjeta de resultados
│   │   └── SentimentAnalyzer.tsx # Componente orquestador principal
│   │
│   ├── hooks/
│   │   ├── use-toast.ts        # Hook de notificaciones
│   │   └── useSentiment.ts     # Hook de lógica de análisis y fetch
│   │
│   ├── lib/
│   │   ├── utils.ts            # Utilidades (cn, helpers)
│   │   ├── exportUtils.ts      # Utilidad de exportación a CSV con BOM UTF-8
│   │   └── constants.ts        # Configuración de Back-Ends y Endpoints
│   │
│   ├── pages/
│   │   ├── Index.tsx           # Página principal
│   │   └── NotFound.tsx        # Página 404
│   │
│   ├── App.tsx                 # Componente raíz
│   ├── index.css               # Estilos globales + Tailwind
│   └── main.tsx                # Punto de entrada (Vite)
│
├── .gitignore
├── eslint.config.js            # Configuración ESLint
├── index.html                  # HTML base (Vite)
├── package.json
├── package-lock.json
├── postcss.config.js           # PostCSS (Tailwind)
├── README.md
├── tailwind.config.js          # Configuración Tailwind
├── tsconfig.json               # Configuración TS base
├── tsconfig.app.json           # TS para la app
├── tsconfig.node.json          # TS para Vite/Node
├── vercel.json                 # Proxy de redirección
└── vite.config.ts              # Configuración Vite

```

---

### Arquitectura del Sistema

## ⚙️ Arquitectura del Sistema (Resiliencia Total)

```
┌─────────────────┐       (1) HTTP POST      ┌─────────────────┐       (2) Proxy      ┌─────────────────┐
│                 │ ───────────────────────► │                 │ ──────────────────►  │                 │
│     Frontend    │    ERR_CONN_REFUSED      │  Node.js (Main) │                      │  FastAPI (ML)   │
│     (React)     │ ◄─────────────────────── │  (Port: 7860)   │ ◄──────────────────  │  (Inferencia)   │
└────────┬────────┘          (3) Fallback    └────────┬────────┘       JSON           └────────┬────────┘
         │                                            │                                        │
         │          (4) Re-intento Exitoso            │ 💾 Persistencia Dual                   │ (2.1) Proxy Java
         │    ┌───────────────────────────────────────┤                                        │
         │    │                                       │                                        │
         ▼    ▼                                       ▼                                        │
┌─────────────────┐                          ┌──────────────────────────────────┐              │
│                 │      HTTP POST (5)       │         Capa de Datos            │              │
│  Java / Spring  │ ───────────────────────► ├──────────────────────────────────┤              │
│    (Backup)     │ ◄─────────────────────── │ 🔥 Firestore  <──🔄──>  🍃 Mongo  │              │
└─────────────────┘      (6) JSON Resp       └──────────────────────────────────┘              │
         │                                                                                     │
         └─────────────────────────────────────────────────────────────────────────────────────┘
                (5.1) Comunicación FastAPI
```

> [!IMPORTANT]
>
> ### 🛡️ Estrategia de Failover del Lado del Cliente
>
> El sistema no solo tiene redundancia en la base de datos, sino también en la capa de orquestación.
>
> 1. **Prioridad 1 (Node.js):** El Frontend intenta realizar la petición al servidor Express.
> 2. **Prioridad 2 (Java/Spring Boot):** Si el servidor de Node no está disponible (`ERR_CONNECTION_REFUSED`), el cliente conmuta automáticamente al backend de Java.
> 3. **Consistencia:** Ambos backends comparten la misma lógica de persistencia dual y comunicación con FastAPI, garantizando una experiencia sin interrupciones.

### Contrato de API

#### Endpoint

```
POST /sentiment
Content-Type: application/json
```

#### Request

```json
{
  "text": "El hotel fue increíble, las habitaciones muy limpias y el personal muy amable."
}
```

#### Response

```json
{
  "prevision": "Positivo",
  "probabilidad": 0.89
}
```

---

#### Endpoint (Batch)

```
POST /sentiment/batch
Content-Type: application/json
```

#### Request (Batch)

```json
{
  "texts": ["Excelente servicio", "Habitación ruidosa"]
}
```

#### Response (Batch)

```json
{
  "success": true,
  "data": [
    {
      "text": "Excelente servicio",
      "sentiment": "Positivo",
      "confidence": 0.98
    },
    {
      "text": "Habitación ruidosa",
      "sentiment": "Negativo",
      "confidence": 0.92
    }
  ]
}
```

---

#### Endpoint (Export)

```
GET /sentiment/export
```

#### Response (Export)

```json
{
  "success": true,
  "count": 120,
  "data": [
    {
      "text": "Excelente servicio",
      "sentiment": "Positivo",
      "confidence": 0.98,
      "timestamp": "2025-12-21T20:00:00.000Z"
    }
  ]
}
```

---

### Ejemplo de Implementación

```typescript
// Configuración de la URL del API
const API_URL = import.meta.env.VITE_API_URL || "http://localhost:8080";

// Función para analizar sentimiento
const analyzeReview = async (text: string): Promise<AnalysisResult> => {
  const response = await fetch(`${API_URL}/sentiment`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ text }),
  });

  if (!response.ok) {
    throw new Error("Error al analizar el sentimiento");
  }

  return response.json();
};
```

### Modelo de Machine Learning

| Componente       | Tecnología                                         |
| ---------------- | -------------------------------------------------- |
| 📊 Vectorización | TF-IDF (Term Frequency-Inverse Document Frequency) |
| 🤖 Clasificador  | Regresión Logística Multiclase                     |
| 🌍 Idiomas       | Español (España) / Portugués (Brasil)              |
| 📦 Serialización | `sentiment_pipeline_bilingual_multiclass.pkl`      |
| ☕ Backend       | Node.js (Principal) / Java (Respaldo)              |

---

## 📊 Métricas del Modelo

| Métrica                          | Valor  |
| -------------------------------- | ------ |
| 🎯 **Accuracy Global**           | 75.17% |
| 📈 **Precision (Positiva)**      | 0.81   |
| 📊 **Recall (Positiva)**         | 0.81   |
| 📉 **Precision (Negativa)**      | 0.76   |
| 📊 **Recall (Negativa)**         | 0.81   |
| 🟡 **Precision (Neutra)**        | 0.67   |
| 🟡 **Recall (Neutra)**           | 0.63   |
| ⚖️ **F1-Score Promedio (Macro)** | 0.75   |

---

## 👥 Equipo

Proyecto desarrollado como parte de un **Hackathon**.

---

## 📄 Licencia

Este proyecto es de uso académico y demostrativo.

---
