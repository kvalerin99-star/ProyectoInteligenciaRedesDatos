# Plataforma Inteligente para Clasificación y Atención de Incidentes de Red

Proyecto desarrollado para el curso de **Inteligencia en Redes de Datos**.

La solución implementa una plataforma para apoyar la clasificación y atención de incidentes de red mediante la integración de un modelo de inteligencia artificial local, recuperación de procedimientos técnicos y consulta de información operativa de la red.

## Objetivo

Desarrollar un prototipo capaz de analizar incidentes de red utilizando diferentes fuentes de información para generar:

- Categoría del incidente
- Prioridad
- Diagnóstico
- Evidencia
- Acción recomendada

La plataforma busca servir como herramienta de apoyo para el administrador de red.

## Tecnologías utilizadas

### Qwen2.5:3b
Modelo de lenguaje utilizado para analizar los incidentes y generar la respuesta final.

### Ollama
Permite ejecutar el modelo Qwen2.5:3b localmente.

### RAG

**RAG (Retrieval-Augmented Generation)** permite recuperar información relevante antes de generar la respuesta.

En este proyecto se utiliza una base de conocimiento formada por procedimientos técnicos relacionados con:

- Rendimiento
- Conectividad
- DNS
- Autenticación

### FastMCP

**MCP (Model Context Protocol)** permite que la aplicación utilice herramientas para consultar información externa.

El servidor FastMCP implementado dispone de herramientas para:

- Listar dispositivos
- Consultar dispositivos
- Consultar métricas
- Buscar tiquetes

### PostgreSQL

Base de datos utilizada para almacenar información relacionada con:

- Dispositivos de red
- Métricas
- Tiquetes

## Arquitectura

El funcionamiento general de la plataforma es:

```text
                 ┌── RAG ──> Procedimientos técnicos
                 │
Incidente ───────┤
                 │
                 └── MCP ──> PostgreSQL
                              │
                              ├── Dispositivos
                              ├── Métricas
                              └── Tiquetes
                 │
                 ▼
          Qwen2.5:3b + Ollama
                 │
                 ▼
          Respuesta final
                 │
        ┌────────┼─────────┐
        ▼        ▼         ▼
    Categoría Diagnóstico Acción recomendada
```

## Funcionamiento

Cuando la plataforma recibe un incidente:

1. Se recibe la descripción del problema.
2. RAG busca un procedimiento técnico relacionado.
3. FastMCP consulta información almacenada en PostgreSQL.
4. Se recuperan datos como CPU, memoria y latencia.
5. La información se proporciona a Qwen2.5:3b.
6. El modelo genera la clasificación y diagnóstico.
7. Finalmente se presenta una acción recomendada.

## Ejemplo

Incidente utilizado durante las pruebas:

```text
SW-CORE-01 presenta lentitud.
Los usuarios reportan problemas de rendimiento.
```

Ejemplo de métricas recuperadas mediante MCP:

```text
CPU: 92.3 %
Memoria: 81.5 %
Latencia: 145.7 ms
```

La plataforma utiliza estos datos como evidencia para apoyar el análisis realizado por Qwen2.5:3b.

## Evaluación

El prototipo fue evaluado utilizando **8 incidentes**, distribuidos entre cuatro categorías:

| Categoría | Casos |
|---|---:|
| Rendimiento | 2 |
| Conectividad | 2 |
| DNS | 2 |
| Autenticación | 2 |
| **Total** | **8** |

### Resultados finales

- Casos evaluados: **8**
- Clasificaciones correctas: **8/8**
- Precisión obtenida en los casos evaluados: **100 %**
- Latencia promedio: **14.62 segundos**
- CPU promedio: **44.52 %**
- Memoria promedio del proceso: **961.62 MB**

La precisión indicada corresponde únicamente al conjunto de ocho casos utilizado durante la evaluación del prototipo.

## Limitaciones encontradas

Durante las pruebas se identificaron algunas limitaciones.

El componente RAG puede recuperar un procedimiento que no sea el más adecuado para determinado incidente. Esto indica que la estrategia de recuperación semántica todavía puede mejorarse.

También se identificó que la ejecución local del modelo mediante CPU presenta una latencia mayor que la que podría obtenerse utilizando aceleración mediante GPU.

## Posibles mejoras

Como trabajo futuro se propone:

- Ampliar la base de procedimientos técnicos.
- Incorporar más casos de prueba.
- Mejorar la estrategia de recuperación del RAG.
- Implementar un umbral de similitud.
- Evaluar modelos adicionales.
- Utilizar aceleración mediante GPU.
- Incorporar más métricas de infraestructura.
- Mejorar el sistema de priorización de incidentes.

## Ejecución

Entrar al proyecto:

```bash
cd ~/proyecto_incidentes_red
```

Activar el entorno virtual:

```bash
source .venv/bin/activate
```

Ejecutar la plataforma:

```bash
python src/plataforma.py
```

## Ejecutar evaluación

Para ejecutar los ocho casos de prueba:

```bash
python src/evaluar.py
```

Al finalizar se muestran la precisión y las métricas de rendimiento obtenidas durante la evaluación.

## Estructura general

```text
proyecto_incidentes_red/
│
├── documentos/
│   ├── autenticacion.txt
│   ├── conectividad.txt
│   ├── dns.txt
│   └── rendimiento.txt
│
├── resultados/
│   └── evaluacion.csv
│
├── src/
│   ├── crear_rag.py
│   ├── evaluar.py
│   ├── plataforma.py
│   └── servidor_mcp.py
│
├── requirements.txt
├── .gitignore
└── README.md
```

## Conclusión

El proyecto demuestra la integración de un modelo de lenguaje local con RAG, MCP y una base de datos PostgreSQL para apoyar el análisis de incidentes de red.

La combinación de procedimientos técnicos y datos operativos permite proporcionar al modelo mayor contexto para generar una clasificación, diagnóstico y acción recomendada.

Los resultados obtenidos muestran la viabilidad del prototipo, aunque todavía existen oportunidades de mejora principalmente en la recuperación de información mediante RAG, el número de casos de evaluación y el rendimiento de la inferencia local.
