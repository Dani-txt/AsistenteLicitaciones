# 🏛️ Asistente de Cumplimiento Normativo para Licitaciones Públicas

Sistema RAG  que permite consultar documentos de licitaciones públicas chilenas en lenguaje natural. El asistente responde únicamente basándose en los documentos cargados, evitando alucinaciones.

---

## Estructura del repositorio

AsistenteLicitaciones/
│
├── AsistenteLicitaciones.ipynb              # Notebook principal del proyecto
├── 1-RESOLUCION_EXENTA_00017_COMPRIMIDO.docx  # Bases de la licitación
├── 2-ANEXO__WORD.docx                       # Plantilla de anexos para oferentes
├── .env.example                             # Plantilla de credenciales (copiar a .env)
├── .gitignore                               # Archivos excluidos de Git
└── README.md                                # Este archivo

---

## Descripción del proyecto

El sistema carga los documentos `.docx` de una licitación, los fragmenta, los vectoriza con embeddings y los almacena en una base vectorial local (ChromaDB). Ante cada consulta, recupera los fragmentos más relevantes y se los entrega a GPT-4o para generar una respuesta fundamentada exclusivamente en los documentos.

**Documentos incluidos:**
- `1-RESOLUCION_EXENTA_00017_COMPRIMIDO.docx` — Bases y resolución de la licitación
- `2-ANEXO__WORD.docx` — Plantilla de anexos que deben completar los oferentes

---

## Instrucciones de ejecución

### Opción A — Google Colab (recomendado)

1. Sube el archivo `AsistenteLicitaciones.ipynb` a Google Colab
2. Sube también los dos archivos `.docx` a la carpeta del entorno de Colab
3. Agrega tus credenciales en **Secrets** (ícono 🔑 en el panel izquierdo):
   - `GITHUB_TOKEN` → tu token personal de GitHub
   - `GITHUB_BASE_URL` → `https://models.inference.ai.azure.com`
4. Ejecuta las celdas en orden con `Shift + Enter`

**¿Cómo obtener el token de GitHub?**
Ve a https://github.com/settings/tokens → *Generate new token (classic)* → activa el scope `models:read` → copia el valor generado.

---

### Opción B — VSCode / entorno local

**1. Clonar el repositorio**

git clone https://github.com/Dani-txt/AsistenteLicitaciones.git
cd AsistenteLicitaciones


**2. Crear un entorno virtual**

# Crear
python -m venv venv

# Activar en macOS/Linux
source venv/bin/activate

# Activar en Windows
venv\Scripts\activate


**3. Instalar dependencias**

pip install langchain langchain-community langchain-openai chromadb pypdf docx2txt python-dotenv


**4. Configurar credenciales**

Copia la plantilla y completa con tus valores:

cp .env.example .env


Abre `.env` y reemplaza el token de ejemplo:

GITHUB_TOKEN=ghp_TU_TOKEN_AQUI
GITHUB_BASE_URL=https://models.inference.ai.azure.com


> ⚠️ El archivo `.env` nunca se sube a GitHub — ya está protegido por `.gitignore`.

**5. Ejecutar el notebook**

jupyter notebook AsistenteLicitaciones.ipynb


Ejecuta las celdas en orden con `Shift + Enter`.
