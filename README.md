# Semántica

**Conversión inteligente de documentos a EPUB, con procesamiento local y análisis semántico.**

Semántica es un proyecto de software orientado a transformar documentos, especialmente PDF, en libros electrónicos EPUB con una estructura más útil para la lectura digital.

En lugar de limitarse a extraer texto y convertirlo directamente, el proyecto busca identificar la estructura semántica del documento —títulos, encabezados, párrafos, listas y otros elementos— para generar un EPUB más organizado y legible.

La filosofía del proyecto es priorizar el procesamiento local: aprovechar los recursos del ordenador del usuario, evitar límites artificiales de uso y mantener el control sobre los documentos procesados.

> **Estado del proyecto:** desarrollo activo y experimental. La disponibilidad de las funciones depende del componente y de su estado de implementación.

## Objetivos

- **Conversión de documentos:** transformar PDF y texto extraído en libros electrónicos EPUB.
- **Estructura semántica:** identificar títulos, subtítulos, párrafos, listas, pies de imagen y contenido irrelevante.
- **Procesamiento local:** utilizar los recursos disponibles en el equipo del usuario siempre que sea posible.
- **Reproducibilidad:** facilitar la ejecución y validación de los procesos de conversión.
- **Extensibilidad:** separar el motor de conversión de las interfaces que lo utilizan.
- **Procesamiento por lotes:** sentar las bases para convertir múltiples documentos y carpetas de manera eficiente.

## Filosofía de diseño

### Local primero

El procesamiento principal está pensado para ejecutarse en el equipo del usuario. El rendimiento dependerá del tamaño y la calidad de los documentos, del método de extracción y de los recursos disponibles.

Este enfoque busca evitar que el uso habitual dependa de una cuota de conversiones o de un servicio remoto.

### Conversión orientada a la estructura

Un PDF no siempre contiene una representación fiable de la estructura de un libro. Los saltos de línea, las columnas, los encabezados repetidos y las páginas escaneadas pueden dificultar una conversión convencional.

Semántica busca interpretar el contenido antes de generar el EPUB, combinando reglas de procesamiento y detección semántica cuando corresponda.

### Separación de componentes

El proyecto distingue el motor de procesamiento de las interfaces de usuario. Esto permite desarrollar diferentes formas de utilizar el mismo sistema sin duplicar la lógica central de conversión.

## Componentes del proyecto

El repositorio contiene, entre otros, los siguientes componentes:

| Componente | Responsabilidad |
|---|---|
| `apps/web` | Aplicación web desarrollada con Vite. |
| `semantica_wasm` | Componente relacionado con el procesamiento mediante WebAssembly y la integración del modelo de detección semántica. |

Esta organización permite mantener la interfaz web separada del componente encargado del procesamiento.

### Detección semántica con Laya

El proyecto integra el trabajo de detección semántica de Laya para clasificar fragmentos del documento según su función estructural.

La aplicación web utiliza un modelo ONNX preparado para su ejecución en el navegador. Los archivos del modelo y su manifiesto deben estar disponibles en la ruta pública esperada para que la carga funcione correctamente.

La disponibilidad del modelo es independiente de que la aplicación web compile correctamente: ambos aspectos deben verificarse durante la construcción y el despliegue.

## Tecnologías

Entre las tecnologías utilizadas por el proyecto se encuentran:

- **TypeScript y JavaScript** para el desarrollo de la aplicación.
- **Vite** para el servidor de desarrollo y la construcción de la aplicación web.
- **WebAssembly (WASM)** para componentes de procesamiento compatibles con el navegador.
- **ONNX** para la ejecución del modelo de detección semántica.
- **GitHub Actions y GitHub Pages** para la integración continua y la publicación de la aplicación web.

La selección concreta de tecnologías y dependencias puede evolucionar conforme avance el desarrollo.

## Requisitos de desarrollo

Para trabajar con el repositorio se necesita:

- Git.
- Node.js y npm compatibles con las dependencias del proyecto.
- Acceso a los repositorios y recursos requeridos por los componentes.
- Conexión a Internet durante la instalación inicial de dependencias y la descarga de los recursos que no estén incluidos localmente.

El consumo de memoria, almacenamiento y CPU dependerá del documento procesado y del modelo utilizado.

## Instalación y desarrollo

### 1. Clonar el repositorio

```bash
git clone https://github.com/SemanticaBook/semantica_web.git
cd semantica_web
```

### 2. Instalar las dependencias

```bash
npm ci
```

### 3. Iniciar el entorno de desarrollo

```bash
npm run dev
```

Vite mostrará en la terminal la dirección local donde se puede abrir la aplicación.

### 4. Construir para producción

```bash
npm run build
```

Los archivos generados se colocan en el directorio `dist`, que es el artefacto utilizado para publicar la aplicación web.

**Nota:** los comandos anteriores corresponden al repositorio de la aplicación web. La preparación del modelo y otros recursos del componente WASM se realiza por separado.

## Preparación del modelo

El componente `semantica_wasm` dispone del comando `model:download` para preparar los archivos del modelo a partir del checkpoint oficial.

Desde el directorio correspondiente:

```bash
npm run model:download
```

Antes de publicar, es necesario comprobar que el proceso de construcción copie los archivos requeridos al directorio público de la aplicación y que estén incluidos en el artefacto final.

En particular, la ruta esperada por la aplicación para el modelo es:

```text
/models/laya-multilingual/manifest.json
```

Si este archivo no se encuentra disponible en el despliegue, la carga del modelo fallará aunque la interfaz web se haya compilado correctamente.

## Publicación

La aplicación web está preparada para utilizar GitHub Actions y GitHub Pages como mecanismo de construcción y despliegue.

El flujo de publicación debe:

1. Obtener el código fuente.
2. Instalar las dependencias.
3. Construir la aplicación.
4. Incluir los recursos estáticos y los archivos del modelo necesarios.
5. Publicar el resultado generado.

Una publicación correcta requiere tanto una compilación satisfactoria como la presencia de todos los recursos que la aplicación necesita en tiempo de ejecución.

## Alcance y limitaciones

Semántica se encuentra en desarrollo. La calidad de la conversión depende de las características de los documentos de entrada, de la extracción de texto y de la precisión de la detección semántica.

Entre los aspectos que deben contemplarse se encuentran:

- PDF con texto seleccionable frente a documentos escaneados que requieren OCR.
- Documentos con columnas, tablas, encabezados y pies de página repetidos.
- Clasificaciones semánticas ambiguas.
- Tamaño y disponibilidad de los modelos utilizados.
- Diferencias de rendimiento entre dispositivos.
- Validación de la estructura y legibilidad del EPUB generado.

La ejecución local reduce la necesidad de enviar documentos a un servicio remoto, pero la privacidad final también depende de los recursos externos y de las operaciones que utilice cada componente.

## Hoja de ruta

Las siguientes líneas de trabajo representan objetivos de evolución del proyecto, no necesariamente funciones ya disponibles:

- [ ] Consolidar la conversión de documentos a EPUB.
- [ ] Mejorar la clasificación semántica y el tratamiento de documentos complejos.
- [ ] Verificar la distribución y carga de los modelos en la aplicación web publicada.
- [ ] Añadir validación y métricas de calidad de las conversiones.
- [ ] Mejorar la gestión de errores y el seguimiento del progreso.
- [ ] Desarrollar el procesamiento por lotes para colecciones de documentos.
- [ ] Ampliar las interfaces disponibles para utilizar el motor de conversión.

## Contribuciones

Las contribuciones, propuestas y reportes de errores son bienvenidos.

Antes de enviar cambios, procura que la aplicación compile correctamente y que las modificaciones relacionadas con los modelos incluyan la comprobación de los recursos necesarios en tiempo de ejecución.

## Licencia

La licencia del proyecto y las licencias de sus dependencias y modelos deben consultarse en los archivos correspondientes de cada repositorio. Los recursos de terceros pueden estar sujetos a condiciones independientes.

