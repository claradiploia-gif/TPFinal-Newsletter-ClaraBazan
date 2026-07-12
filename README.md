# TPFinal-Newsletter-ClaraBazan
Este repositorio contiene el proceso de n8n sobre la automatización de creacion de newletter. Autor: Clara Bazán

# Sistema Automatizado de Creación y Distribución de Newsletters — VISION 360

Este proyecto es un ecosistema de automatización desarrollado en **n8n** que integra Inteligencia Artificial (**Google Gemini**) y herramientas de Google Workspace (**Sheets, Drive y Gmail**). Su objetivo es generar boletines informativos corporativos de alta autoridad y distribuirlos de forma masiva a una base de clientes (CRM) tras una curación humana intermedia.

---

## Arquitectura del Sistema y Tecnologías
* **n8n:** Motor principal de orquestación y automatización de flujos.
* **Google Gemini (3-Flash):** IA encargada del procesamiento, cruce de variables y redacción del contenido en HTML limpio.
* **Google Sheets:** Actúa como base de datos en dos partes: gestión de tópicos/bibliografía y CRM de clientes.
* **Google Drive:** Almacenamiento en la nube para los borradores en carpetas segregadas (`Para aprobar` / `Aprobados`).
* **Gmail:** Cliente de correo electrónico para notificaciones internas y envíos masivos (*White-label*).

---

## Estructura de las Planillas (Google Sheets)
Para que el flujo funcione, la planilla `Topicos para Newletter` debe contar con las siguientes pestañas y columnas:
1. **TOPICOS:** Columnas `Topic`, `Estado` (Pendiente/Creado), `Date`.
2. **LINKS:** Columnas `ColumnaDeLinks`, `Estado` (Usar).
3. **BIBLIOGRAFÍA:** Columnas `ColumnaDeBibliografia`, `Estado` (Usar).
4. **CRM:** Columnas `Email`, `Estado` (Pendiente/finalizado), `Fecha de envio`.

---

## Configuración e Instalación

### 1. Clonar el Flujo en n8n
1. Crea un nuevo flujo de trabajo (Workflow) vacío en tu instancia de n8n.
2. Copia el código JSON del archivo `Newsletter.json` de este repositorio.
3. Pégalo directamente sobre el lienzo de n8n.

### 2. Configurar Credenciales
Debes vincular tus propias cuentas y APIs en los siguientes nodos:
* **Google Sheets y Drive:** Configura tu cuenta mediante OAuth2 y reemplaza los IDs de las planillas y carpetas por los tuyos.
* **Google Gemini:** Introduce tu API Key de Google AI Studio.
* **Gmail:** Vincula la cuenta corporativa de salida de correos.

---

## Funcionamiento de los Flujos

### Flujo A: Generación y Curación (Bajo Demanda)
* Se activa manualmente mediante el nodo `Crear newsletter Manual`.
* Recopila la información de las planillas, Gemini redacta el HTML corporativo (usando la paleta de colores `#152943` y `#85b643`).
* Un nodo de JavaScript nativo (`Code`) empaqueta el contenido en un archivo binario `.html` cronológico y lo sube a Drive.
* Envía un correo de alerta al administrador para su revisión.

### Flujo B: Distribución Masiva (Automatizado)
* Se dispara de forma programada mediante el `Schedule Trigger`.
* Descarga el HTML validado desde la carpeta de archivos aprobados en Drive.
* Filtra los clientes pendientes en el CRM y les despacha el newsletter en formato HTML puro.
* Actualiza de manera selectiva la columna `Estado` a `finalizado` y estampa la fecha de envío local en la fila correspondiente de Google Sheets.

---

## Notas de Desarrollo (JavaScript en el Nodo Code)
El flujo incluye un script en Node.js/JavaScript que maneja la conversión de strings a buffers binarios para Google Drive:
- Extrae de forma segura el texto de la estructura JSON de Gemini mediante rutas alternativas de contingencia.
- Utiliza `Buffer.from(htmlContent, 'utf8')` para preparar el paquete binario final.

---
