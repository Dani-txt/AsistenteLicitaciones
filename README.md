# 🏛️ Asistente de Cumplimiento Normativo para Licitaciones Públicas

Sistema RAG (Retrieval-Augmented Generation) que permite consultar documentos de licitaciones públicas chilenas en lenguaje natural. El asistente responde únicamente basándose en los documentos cargados, evitando alucinaciones.

---

## 📋 Descripción del proyecto

El sistema carga documentos `.docx` de una licitación (bases y anexos), los fragmenta, los vectoriza con embeddings y los almacena en una base vectorial local (Chroma). Ante cada consulta, recupera los fragmentos más relevantes (usando MMR) y se los entrega a GPT-4o para generar una respuesta fundamentada en el documento.

**Documentos incluidos de ejemplo:**
- `1-RESOLUCION_EXENTA_00017_COMPRIMIDO.docx` — Bases de la licitación
- `2-ANEXO__WORD.docx` — Plantilla de anexos para oferentes

---

## ⚙️ Requisitos previos

- Python 3.9 o superior
- Jupyter Notebook o JupyterLab (`pip install notebook`)
- Una cuenta de GitHub con acceso a **GitHub Models** (gratuito)

---

## 🚀 Instrucciones de instalación paso a paso

### Paso 1: Clonar el repositorio

```bash
git clone https://github.com/Dani-txt/AsistenteLicitaciones.git
cd AsistenteLicitaciones
```

### Paso 2: Crear un entorno virtual (recomendado)

```bash
# Crear el entorno
python -m venv venv

# Activarlo en macOS/Linux
source venv/bin/activate

# Activarlo en Windows
venv\Scripts\activate
```

### Paso 3: Configurar las credenciales (el paso más importante)

Este proyecto usa **GitHub Models** para acceder a GPT-4o. Necesitas un token personal de GitHub.

**3.1 Obtener tu GitHub Token:**
1. Ve a https://github.com/settings/tokens
2. Haz clic en **"Generate new token (classic)"**
3. Dale un nombre descriptivo (ej: `AsistenteLicitaciones`)
4. Activa el scope **`models:read`**
5. Haz clic en **"Generate token"** y copia el valor (solo se muestra una vez)

**3.2 Crear el archivo `.env`:**
```bash
# Copia la plantilla
cp .env.example .env
```

Abre el archivo `.env` con cualquier editor de texto y reemplaza el valor de ejemplo:

```
GITHUB_TOKEN=ghp_TU_TOKEN_AQUI_PEGALO_EN_ESTA_LINEA
GITHUB_BASE_URL=https://models.inference.ai.azure.com
```

> ⚠️ **Importante:** El archivo `.env` nunca se sube a GitHub. Cada persona debe crear el suyo con su propio token.

### Paso 4: Instalar dependencias

Las dependencias se instalan desde la primera celda del notebook. Si prefieres instalarlas antes:

```bash
pip install langchain langchain-community langchain-openai chromadb pypdf docx2txt python-dotenv
```

### Paso 5: Agregar los documentos de licitación

Coloca los archivos `.docx` de la licitación en la **misma carpeta** que el notebook:

```
AsistenteLicitaciones/
├── asistente_licitaciones.ipynb
├── 1-RESOLUCION_EXENTA_00017_COMPRIMIDO.docx   ← aquí
├── 2-ANEXO__WORD.docx                           ← aquí
├── .env                                         ← tu archivo con el token
└── ...
```

### Paso 6: Ejecutar el notebook

```bash
jupyter notebook asistente_licitaciones.ipynb
```

Ejecuta las celdas **en orden** de arriba hacia abajo con `Shift + Enter`.

---

## 🗂️ Estructura del repositorio

```
AsistenteLicitaciones/
│
├── asistente_licitaciones.ipynb   # Notebook principal del proyecto
├── .env.example                   # Plantilla de configuración (copiar a .env)
├── .gitignore                     # Archivos excluidos de Git
└── README.md                      # Este archivo
```

---

## 💡 Cómo usar el asistente

Una vez ejecutadas todas las celdas, puedes hacer consultas llamando a la función `consultar()`:

```python
# Ejemplo de consulta directa
print(consultar("¿Cuál es el plazo máximo de implementación?"))

# Puedes agregar tus propias preguntas al final del notebook
print(consultar("¿Qué garantías debe presentar el oferente?"))
```

**Tipos de preguntas y sus resultados:**

| Tipo de pregunta | Ejemplo | Resultado esperado |
|---|---|---|
| ✅ Específica y técnica | "¿Qué sistemas del Estado se requiere integrar?" | Respuesta precisa con datos del documento |
| ⚠️ Genérica o ambigua | "¿Qué requisitos tiene la licitación?" | Respuesta parcial o superficial |
| ❌ Fuera del contexto | "¿Messi o Ronaldo?" | "No tengo información sobre eso..." |

---

## 🔧 Solución de problemas frecuentes

**Error: `EnvironmentError: Faltan variables de entorno`**
→ Verifica que el archivo `.env` existe en la carpeta del proyecto y que `GITHUB_TOKEN` tiene un valor real.

**Error: `FileNotFoundError` al cargar los `.docx`**
→ Los archivos `.docx` deben estar en la misma carpeta que el notebook.

**Error: `401 Unauthorized` de la API**
→ El token de GitHub es incorrecto o expiró. Genera uno nuevo siguiendo el Paso 3.

**El notebook no encuentra el módulo `dotenv`**
→ Ejecuta la celda de instalación de dependencias (`!pip install ...`) antes que cualquier otra celda.

---

## 🏗️ Arquitectura del pipeline RAG

```
Documentos .docx
      │
      ▼
 Docx2txtLoader         ← Extracción de texto
      │
      ▼
RecursiveCharacterTextSplitter  ← Fragmentación (chunk_size=800, overlap=150)
      │
      ▼
  OpenAIEmbeddings       ← Vectorización (text-embedding-3-small)
      │
      ▼
   ChromaDB              ← Almacenamiento vectorial local
      │
   [Consulta]
      │
      ▼
 MMR Retriever           ← Recuperación (k=4, fetch_k=12, lambda=0.7)
      │
      ▼
  System Prompt          ← Ingeniería de prompt con contexto recuperado
  + Contexto
  + Pregunta
      │
      ▼
   GPT-4o (LLM)          ← Generación de respuesta
      │
      ▼
   Respuesta final
```
