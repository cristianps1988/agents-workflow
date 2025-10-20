<div align="center">

[🇺🇸 English](./README.md) | [🇪🇸 Español](./README.es.md)

# Flujo de Trabajo de Desarrollo Potenciado por IA con Claude Code

### Construye Aplicaciones de Grado de Producción con IA - De la Manera Correcta

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)

**Una metodología que desarrollé mientras construía una aplicación compleja con asistencia de IA. Utiliza agentes especializados, optimización de contexto y aplicación arquitectónica para mantener consistencia y calidad.**

Este enfoque me ayudó a enfrentar los desafíos de construir software altamente personalizado con IA. Lo comparto aquí por si ayuda a otros trabajando en proyectos similares.

[Comenzar](#requisitos-previos) • [Conceptos Centrales](#conceptos-centrales) • [Guía Paso a Paso](#implementación-paso-a-paso) • [Solución de Problemas](#problemas-comunes-y-soluciones)

---

</div>

## Tabla de Contenidos

1. [Introducción](#introducción)
2. [¿Por qué esta metodología?](#por-qué-esta-metodología)
3. [Requisitos previos](#requisitos-previos)
4. [Conceptos centrales](#conceptos-centrales)
5. [Implementación paso a paso](#implementación-paso-a-paso)
6. [Problemas comunes y soluciones](#problemas-comunes-y-soluciones)
7. [Recursos](#recursos)

---

## Introducción

Esta guía documenta una metodología probada para usar **Claude Code** y **agentes de IA especializados** para construir proyectos de software con:

- **Mayor productividad**: La IA escribe código alineado con tus requisitos y políticas
- **Menos errores**: Los agentes siguen patrones arquitectónicos y restricciones estrictas
- **Mejor mantenibilidad**: Estructura de código y documentación consistente
- **Eficiencia de tokens**: Uso de contexto optimizado para reducir costos y mejorar el rendimiento

El enfoque combina:
- **Agentes especializados**: Agentes específicos de tareas con responsabilidades claras
- **Flujo de trabajo de dos fases**: Planificación (30% de tokens) → Ejecución (70% de tokens)
- **Ingeniería de contexto**: Organización estratégica de documentos y carga perezosa
- **Integración MCP**: Acceso granular a herramientas por agente
- **Protocolo de sesiones**: Registros de solo-agregar para optimización de KV-cache

---

## ¿Por qué esta metodología?

### El problema

Al usar asistentes de IA para construir proyectos complejos, los desafíos comunes incluyen:

1. **Sobrecarga de contexto**: Cargar toda la documentación por adelantado consume tokens excesivos
2. **Resultados inconsistentes**: Los agentes no siguen las convenciones del proyecto de manera consistente
3. **Deriva arquitectónica**: El código no respeta los límites arquitectónicos
4. **Agentes generalistas**: Un agente intentando hacer todo lleva a menor calidad
5. **Pérdida de contexto**: Sin memoria persistente de decisiones entre sesiones

### La solución

Esta metodología aborda estos problemas a través de:

**Optimización de contexto**
- Documento de restricciones críticas (~200 tokens) cargado primero
- Carga perezosa: Buscar secciones específicas en lugar de documentos completos
- El contexto de sesión mantiene decisiones e historial

**Agentes especializados**
- Cada agente maneja UN dominio (UI, base de datos, capa de dominio, etc.)
- Los agentes planifican pero no implementan → permite revisión antes de la ejecución
- Acceso granular a MCP → solo las herramientas necesarias para su tarea específica

**Flujo de trabajo de dos fases**
- Fase 1: El agente crea un plan de implementación detallado
- Fase 2: El agente padre ejecuta el plan paso a paso
- Beneficios: Reutilización de contexto, recuperación de errores, progreso incremental

**Cumplimiento arquitectónico**
- Reglas de dependencia documentadas y aplicadas
- Restricciones críticas previenen anti-patrones comunes
- Estructura de archivos y convenciones de nombres consistentes

### Resultados reales

Esta metodología fue desarrollada y validada construyendo una **aplicación compleja de grado de producción** desde cero con lógica de negocio altamente personalizada y requisitos arquitectónicos específicos.

**Por qué importan los proyectos complejos**: La IA puede construir fácilmente aplicaciones CRUD simples copiando patrones existentes. El verdadero desafío es construir **aplicaciones altamente personalizadas** con:
- Lógica de negocio personalizada específica de tu dominio
- Restricciones arquitectónicas estrictas
- Gestión de estado y flujos de datos complejos
- Arquitecturas multi-capa
- Requisitos específicos de seguridad y validación

**Sin esta metodología**, los asistentes de IA con prompts simples:
- ❌ Se desvían de patrones arquitectónicos
- ❌ Crean código inconsistente entre características
- ❌ Violan reglas de negocio
- ❌ Repiten errores
- ❌ Pierden contexto entre sesiones

**Con esta metodología**, resultados probados:
- ✅ **Reducción de tokens**: ~70% menos uso de contexto vs cargar todo
- ✅ **Consistencia**: Todo el código sigue patrones arquitectónicos a través de 100+ archivos
- ✅ **Trabajo paralelo**: Múltiples agentes pueden planificar diferentes características simultáneamente
- ✅ **Calidad**: Menos bugs, mejor alineación con requisitos complejos
- ✅ **Mantenibilidad**: Patrones claros, decisiones documentadas, registro de auditoría

---

## Requisitos previos

### 1. Instalar Claude Code

Claude Code es la CLI oficial de Anthropic para desarrollo asistido por IA.

**Instalación:**

```bash
# macOS/Linux
brew install anthropics/claude/claude

# O usando npm
npm install -g @anthropic/claude-code

# Verificar instalación
claude --version
```

**Autenticación:**

```bash
# Iniciar sesión con tu cuenta de Anthropic
claude auth login
```

Para instrucciones detalladas de instalación, visita: https://docs.claude.com/claude-code

### 2. Conceptos básicos

Antes de comenzar, deberías entender:

- **Básicos de Git**: Esta metodología usa repositorios git
- **Línea de comandos**: Navegación básica de terminal/shell
- **Tu stack tecnológico**: Framework/bibliotecas que usarás (React, Vue, etc.)
- **Markdown**: La documentación está escrita en formato Markdown

### 3. Tu idea de proyecto

Ten una idea clara de:
- Qué aplicación quieres construir
- Características y funcionalidad principales
- Roles de usuario (si aplica)
- Reglas de negocio críticas

---

## Conceptos centrales

### 1. Gestión de presupuesto de tokens

**Problema**: Contexto grande = costoso + respuestas más lentas

**Solución**: Cargar solo lo necesario, cuando sea necesario

**Estrategia**:
- Siempre cargar `critical-constraints.md` primero (~200 tokens)
- Cargar contexto de sesión si está disponible (~300-500 tokens)
- Usar Grep para buscar secciones específicas (~200 tokens) vs cargar documentos completos (~2000-5000 tokens)
- Evitar cargar documentación completa por adelantado

**Ejemplo de presupuesto de contexto**:
```
Ruta óptima:
  critical-constraints.md:     200 tokens
  contexto de sesión:          300 tokens
  Grep sección específica:     200 tokens
  TOTAL:                       700 tokens ✅

Contexto pesado (evitar):
  Cargar todos los docs:     7,600 tokens ❌
```

### 2. Flujo de trabajo de dos fases (Planificación → Ejecución)

**Fase 1: Planificación (~30% de tokens)**
- El agente especializado investiga el código base
- Crea un plan de implementación detallado
- Guarda el plan en `.claude/plans/{tipo}-{característica}-plan.md`
- Añade resumen al contexto de sesión

**Fase 2: Ejecución (~70% de tokens)**
- El agente padre (o tú) lee el plan
- Implementa paso a paso
- Actualiza el contexto de sesión con el progreso

**Beneficios**:
- Los planes pueden ser revisados antes de la implementación
- Los planes son reutilizables si la ejecución falla
- Planificación paralela: Múltiples agentes planifican diferentes características simultáneamente
- Optimización de KV-cache: Prefijo de contexto estable

### 3. Agentes especializados

**Principio clave**: Un agente, una responsabilidad

En lugar de un agente generalista haciendo todo, crear agentes especializados para:
- **Diseño UI**: Planificar componentes, layouts (sin implementación)
- **Diseño de base de datos**: Schema, migraciones, políticas RLS (sin ejecución)
- **Capa de dominio**: Entidades, repositorios, servicios (sin escribir código)
- **Refactorización**: Analizar anti-patrones, crear plan de migración
- **Investigación**: Auditar UI/UX, accesibilidad, rendimiento

**Beneficios**:
- Mayor calidad (el agente es experto en su dominio)
- Acceso granular a MCP (agente UI obtiene MCP de shadcn, agente DB no lo necesita)
- Ejecución paralela posible
- Clara separación de responsabilidades

### 4. Contexto de sesión de solo-agregar

**Problema**: Sobrescribir archivos de sesión rompe KV-cache (se necesita prefijo estable)

**Solución**: Los archivos de sesión son registros de solo-agregar

Cada agente añade una nueva entrada:
```markdown
---
## [2025-10-19 14:30] ui-architect: Diseño de Formulario de Orden de Trabajo

**Tarea**: Crear formulario para creación de orden de trabajo por admin

**Estado**: ✅ Plan Listo

**Ubicación del plan**: .claude/plans/ui-work-order-form-plan.md

**Decisiones clave**:
- Usar componentes Form de shadcn/ui
- Formulario multi-paso (selección cliente → detalles trabajo → revisión)
- Texto externalizado en work-order-form.text-map.ts

---
```

**Beneficios**:
- Mantiene eficiencia de KV-cache (prefijo estable)
- Registro de auditoría de todas las decisiones
- Contexto para futuros agentes
- Aprendizaje de errores (intentos fallidos documentados)

### 5. Estrategia de configuración MCP

**Problema**: Cargar todos los servidores MCP en todos los agentes desperdicia tokens

**Solución**: Acceso granular a MCP por tipo de agente

**Ejemplo**:
- Agente UI: MCP shadcn (~4.7k tokens) ✅, MCP Playwright ❌
- Agente Base de Datos: MCP Supabase ✅, MCP shadcn ❌
- Agente Auditoría UX: shadcn + Playwright (~20k tokens) ✅

**Configuración**: `.mcp.json` define servidores disponibles, `.claude/settings.local.json` habilita/deshabilita por sesión

### 6. Restricciones críticas primero

**Cada agente debe leer `.claude/knowledge/critical-constraints.md` PRIMERO**

Este documento (~200 tokens) contiene reglas no negociables que previenen errores comunes:

Ejemplo de restricciones:
- ✅ HACER: Usar patrón de repositorio para acceso a datos
- ❌ NO HACER: Importaciones directas de base de datos en componentes
- ✅ HACER: Externalizar todo el texto a mapas de texto
- ❌ NO HACER: Strings hardcodeados en UI

### 7. Aplicación de patrones arquitectónicos

Elegir UNA arquitectura y documentarla claramente:
- Arquitectura Limpia (orientada al dominio)
- Arquitectura Hexagonal
- Arquitectura basada en características
- Arquitectura en capas

**Clave**: Documentar reglas de dependencia y aplicarlas

Ejemplo (Arquitectura Limpia):
```
core/              ← NUNCA importa de capas externas
  ↑
infrastructure/    ← PUEDE importar de core/, NUNCA de features/
  ↑
features/          ← PUEDE importar de core/ e infrastructure/
```

---

## Implementación paso a paso

### Paso 0: Define tu proyecto

Antes de crear cualquier archivo, ten una conversación con Claude Code para generar la documentación de tu proyecto.

**Qué describir**:

1. **Resumen de la aplicación**
   - ¿Qué problema resuelve?
   - ¿Quiénes son los usuarios?
   - ¿Cuáles son las características principales?

2. **Roles de usuario** (si aplica)
   - ¿Qué tipos de usuarios existen?
   - ¿Qué puede hacer cada rol?

3. **Reglas de negocio críticas**
   - ¿Qué validaciones se requieren?
   - ¿Qué transiciones de estado existen?
   - ¿Qué restricciones deben aplicarse?

4. **Stack tecnológico** (si está decidido)
   - Framework frontend
   - Backend/Base de datos
   - Bibliotecas clave

**Ejemplo de prompt**:

```
Quiero construir una [descripción de tu app].

Usuarios: [describe tipos de usuarios y sus capacidades]

Características clave: [lista características principales]

Reglas de negocio: [describe reglas críticas, validaciones, flujos]

Stack tecnológico: [si está decidido]

Por favor crea un documento PROJECT.md que incluya:
- Resumen del producto
- Características y funcionalidad
- Roles de usuario y permisos
- Modelos de datos (entidades de alto nivel)
- Reglas de negocio
- Cualquier otra sección relevante

Hazlo lo suficientemente completo para que un agente de IA pueda entender
el contexto completo de lo que estamos construyendo.
```

**Resultado**: `PROJECT.md` en la raíz de tu repositorio

**Luego**: Pide a Claude Code que extraiga las reglas de negocio esenciales a `.claude/knowledge/business-rules.md` (versión optimizada para agentes)

---

### Paso 1: Crear estructura del proyecto

Crea la estructura de directorios base para la metodología:

```bash
# Crear estructura de directorio .claude
mkdir -p .claude/knowledge
mkdir -p .claude/agents
mkdir -p .claude/tasks
mkdir -p .claude/plans
mkdir -p .claude/reports

# Crear archivos .gitkeep para preservar directorios vacíos
touch .claude/plans/.gitkeep
touch .claude/reports/.gitkeep
```

**Propósito de directorios**:
- `.claude/knowledge/`: Documentación optimizada en tokens para agentes
- `.claude/agents/`: Definiciones de agentes especializados
- `.claude/tasks/`: Archivos de contexto de sesión (registros de solo-agregar)
- `.claude/plans/`: Planes de implementación generados por agentes
- `.claude/reports/`: Reportes de análisis y auditoría

---

### Paso 2: Configurar CLAUDE.md

Crea `CLAUDE.md` en la raíz de tu repositorio. Este es el **punto de entrada** para todos los agentes de IA.

**Propósito**:
- Primer archivo que leen los agentes (~800 tokens máximo)
- Apunta a restricciones críticas
- Explica el flujo de trabajo y protocolos
- Referencia a otra documentación

**Plantilla**: Ver `CLAUDE.md` en este repositorio

**Secciones clave** (personaliza para tu proyecto):

1. **Resumen del proyecto** (2-3 frases)
2. **Instrucciones críticas**
   - SIEMPRE leer `.claude/knowledge/critical-constraints.md` primero
   - Verificar archivo de contexto de sesión si se proporciona session_id
   - Seguir protocolo de flujo de trabajo para nuevas características
3. **Resumen del flujo de trabajo**
   - Cuándo usar agentes vs implementación directa
   - Cómo crear contextos de sesión
   - Dónde se almacenan los planes
4. **Mapa de documentación**
   - Enlaces a documentos de conocimiento
   - Cuándo usar cada documento
5. **Notas de configuración MCP**
   - Qué servidores MCP están disponibles
   - Cómo habilitar/deshabilitar

**⚠️ Importante**: Mantén este archivo bajo 1000 tokens. Se carga en cada invocación de agente.

---

### Paso 3: Crear restricciones críticas

Crea `.claude/knowledge/critical-constraints.md`

**Propósito**:
- Reglas no negociables (~200 tokens)
- Cargadas por CADA agente PRIMERO
- Previene anti-patrones comunes

**Qué incluir**:

1. **Reglas arquitectónicas** (5-10 reglas críticas)
   - Reglas de dependencia
   - Qué NO hacer
   - Qué patrones usar SIEMPRE

2. **Estándares de código** (obligatorios)
   - Patrones de importación
   - Convenciones de nombres
   - Reglas de organización de archivos

3. **Patrones de siempre-hacer**
   - Prácticas requeridas
   - Verificaciones obligatorias

**Plantilla**: Ver `.claude/knowledge/critical-constraints.md`

**Ejemplo de estructura**:

```markdown
# Restricciones críticas

**Reglas que DEBEN seguirse en todo el código. ~200 tokens.**

---

## 1. [Tu regla crítica #1]

❌ **NUNCA**: [Qué no hacer]
✅ **SIEMPRE**: [Qué hacer en su lugar]

**Ejemplo**:
[Ejemplo de código mostrando el patrón correcto]

---

## 2. [Tu regla crítica #2]
...
```

**✅ Elementos obligatorios**:
- Debe ser conciso (~200 tokens total)
- Debe usar formato ❌/✅ para claridad
- Debe incluir ejemplos de código
- Debe cubrir tus reglas arquitectónicas más críticas

---

### Paso 4: Configurar servidores MCP

Crea `.mcp.json` en la raíz de tu repositorio.

**Propósito**: Definir servidores MCP disponibles para agentes

**¿Qué son los servidores MCP?**
Los servidores del Protocolo de Contexto de Modelo proporcionan herramientas a agentes (ej. componentes UI, acceso a base de datos, automatización de navegador).

**Plantilla**: Ver `.mcp.json`

**Ejemplo**:

```json
{
  "mcpServers": {
    "tu-nombre-herramienta": {
      "command": "npx",
      "args": ["nombre-paquete", "comando"]
    }
  }
}
```

**Servidores MCP comunes**:
- `shadcn`: Biblioteca de componentes UI (~4.7k tokens)
- `@playwright/mcp`: Automatización de navegador (~14k tokens)
- `supabase`: Acceso a base de datos
- Servidores personalizados para tus herramientas específicas

**Estrategia**:
- Definir TODOS los servidores disponibles en `.mcp.json`
- Habilitar/deshabilitar por sesión usando `.claude/settings.local.json`
- Solo habilitar lo que el agente actual necesita

**Ejemplo `.claude/settings.local.json`**:
```json
{
  "enabledMcpjsonServers": ["shadcn"],
  "enableAllProjectMcpServers": false
}
```

**Optimización de tokens**:
- Trabajo UI: Habilitar solo MCPs relacionados con UI
- Trabajo de base de datos: Habilitar solo MCPs de base de datos
- Auditoría UX: Habilitar MCPs de UI + automatización de navegador

---

### Paso 5: Define tu arquitectura

Crea `.claude/knowledge/architecture-patterns.md`

**Propósito**: Documentar tu arquitectura elegida y aplicar consistencia

**Qué incluir**:

1. **Elección de arquitectura**
   - Qué patrón arquitectónico estás usando
   - Por qué lo elegiste
   - Diagrama o descripción de alto nivel

2. **Definiciones de capas**
   - De qué es responsable cada capa
   - Qué contiene cada capa
   - Ejemplos de archivos en cada capa

3. **Reglas de dependencia** (CRÍTICO)
   - Qué puede importar de qué
   - Qué está prohibido
   - Representación visual

4. **Patrones y prácticas**
   - Patrón de repositorio
   - Patrón de servicio
   - Patrón presenter (si aplica)
   - Estrategia de gestión de estado

5. **Ejemplos de código**
   - Ejemplos de antes/después
   - Patrones correctos vs incorrectos

**Plantilla**: Ver `.claude/knowledge/architecture-patterns.md`

**Ejemplo (Arquitectura Limpia)**:

```markdown
# Patrones de arquitectura

## Regla de dependencia

```
core/              ← Agnóstico del framework, NUNCA importa de capas externas
  ↑
infrastructure/    ← Implementaciones, PUEDE importar de core/
  ↑
features/          ← Capa UI, PUEDE importar de core/ e infrastructure/
  ↑
app/               ← Configuración de app, PUEDE importar de todas las capas
```

## Capas

### Capa de dominio (core/)
**Responsabilidad**: Lógica de negocio, agnóstica del framework

**Contiene**:
- Entidades: `core/entities/{nombre}.entity.ts`
- Interfaces de repositorio: `core/repositories/{nombre}-repository.interface.ts`
- Servicios: `core/services/{nombre}-service.ts`

**Reglas**:
- ❌ NO importaciones de frameworks (React, Supabase, etc.)
- ✅ TypeScript/JavaScript puro
- ✅ Agnóstico de plataforma

### Capa de infraestructura
**Responsabilidad**: Implementaciones externas

**Contiene**:
- Implementaciones de API: `infrastructure/api/{nombre}-api.ts`
- Implementaciones de repositorio (implementa interfaces de core/)

**Reglas**:
- ✅ PUEDE importar de core/
- ✅ PUEDE usar bibliotecas externas (Supabase, Axios, etc.)
- ❌ NUNCA importar de features/ o app/

[Continuar con otras capas...]
```

**Nota**: Adapta a TU arquitectura elegida. Esto es solo un ejemplo.

---

### Paso 6: Documentar reglas de negocio

Ya creaste esto en el Paso 0 cuando Claude Code lo extrajo de PROJECT.md.

**Archivo**: `.claude/knowledge/business-rules.md`

**Propósito**:
- Lógica de negocio esencial (~500-2000 tokens)
- Versión optimizada de PROJECT.md
- Los agentes leen esto vía Grep para secciones específicas

**Qué incluir**:

1. **Roles de usuario y permisos**
   - Qué puede/no puede hacer cada rol
   - Reglas de control de acceso

2. **Estados y transiciones de entidades**
   - Estados válidos para tus entidades
   - Transiciones permitidas
   - Quién puede cambiar estados

3. **Reglas de validación**
   - Campos requeridos
   - Validaciones de formato
   - Restricciones de negocio

4. **Reglas de visibilidad de datos**
   - Qué datos puede ver cada rol
   - Reglas de filtrado
   - Restricciones de privacidad

**Plantilla**: Ver `.claude/knowledge/business-rules.md`

---

### Paso 7: Configurar estrategia de contexto

Crea `.claude/knowledge/context-strategy.md`

**Propósito**: Enseñar a los agentes cómo cargar contexto eficientemente

**Qué incluir**:

1. **Árbol de decisión**
   - Cuándo cargar qué documentos
   - Cómo elegir entre lectura completa vs Grep
   - Presupuestos de tokens para diferentes escenarios

2. **Estrategia de carga perezosa**
   - Enfoque Grep-primero
   - Lecturas dirigidas con offset/limit
   - Cuándo cargar documentos completos (raramente)

3. **Ejemplos de presupuesto de contexto**
   - Rutas óptimas (~500-1000 tokens)
   - Rutas pesadas a evitar (~5000+ tokens)

4. **Conteos de tokens de documentos**
   - Tabla de referencia de todos los docs y sus tamaños

**Plantilla**: Ver `.claude/knowledge/context-strategy.md`

---

### Paso 8: Definir convenciones de estructura de archivos

Crea `.claude/knowledge/file-structure.md`

**Propósito**: Documentar convenciones de nombres y organización de archivos

**Qué incluir**:

1. **Convenciones de nombres de archivos**
   - Componentes
   - Páginas
   - Hooks
   - Servicios
   - Utilidades
   - Tests

2. **Estructura de directorios**
   - Dónde va cada tipo de archivo
   - Estrategia de agrupación (por característica, por tipo, etc.)

3. **Patrones de importación**
   - Importaciones absolutas vs relativas
   - Ordenamiento de importaciones
   - ¿Archivos barrel? (¿usar o evitar?)

**Plantilla**: Ver `.claude/knowledge/file-structure.md`

---

### Paso 9: Documentar stack tecnológico

Crea `.claude/knowledge/tech-stack.md`

**Propósito**: Referencia para versiones, comandos y uso de bibliotecas

**Qué incluir**:

1. **Tecnologías principales**
   - Framework y versión
   - Lenguaje y versión
   - Herramienta de construcción

2. **Dependencias clave**
   - Bibliotecas UI
   - Gestión de estado
   - Obtención de datos
   - Manejo de formularios
   - Enrutamiento
   - Testing

3. **Herramientas de desarrollo**
   - Gestor de paquetes (npm, pnpm, yarn)
   - Linter/Formateador
   - Framework de testing

4. **Referencia de comandos**
   - Instalar dependencias
   - Ejecutar servidor dev
   - Construir producción
   - Ejecutar tests
   - Formatear código

**Plantilla**: Ver `.claude/knowledge/tech-stack.md`

---

### Paso 10: Crear tu primer agente especializado

**Dos plantillas disponibles**:

1. **`.claude/agents/specialized-agent-template.md`** - Plantilla genérica con instrucciones
2. **`.claude/agents/ui-architect.md`** - Ejemplo de agente UI (personaliza para tu stack)

**Para crear un nuevo agente**:

1. **Copia una plantilla**: Comienza con `specialized-agent-template.md`
2. **Llena el frontmatter**:
   ```yaml
   ---
   name: tu-nombre-agente
   description: Descripción de una línea. Crea planes de implementación para que el agente padre ejecute.
   model: sonnet
   color: cyan
   ---
   ```
3. **Reemplaza los placeholders** con tu contenido específico
4. **Mantenlo conciso**: ~100-150 líneas (no 500+)
5. **Guarda en**: `.claude/agents/tu-nombre-agente.md`

**Qué personalizar**:

1. **Identidad del Agente**
   - Nombre y especialización
   - Qué hace / Qué no hace

2. **Flujo de Misión** (5 pasos - mantén estos)
   1. Leer contexto de sesión
   2. Investigar código base
   3. Diseñar [dominio del agente]
   4. Crear plan
   5. Añadir al contexto

3. **Restricciones del Proyecto** (CRÍTICO)
   - Tus reglas arquitectónicas
   - Tus elecciones tecnológicas
   - Tus convenciones de nombres

4. **Herramientas Permitidas**
   - Qué servidores MCP
   - Qué operaciones de archivo

5. **Plantilla de Plan**
   - Estructura específica al dominio de este agente

**✅ Elementos OBLIGATORIOS** (mantén en TODOS los agentes):
- Frontmatter (name, description, model, color)
- Flujo de Misión de 5 pasos
- Debe leer `critical-constraints.md` primero
- Debe crear plan en `.claude/plans/`
- Debe añadir (no sobrescribir) al contexto de sesión
- NO debe implementar código directamente
- Sección de Reglas (numeradas, claras)

**Vista general de estructura de plantilla**:

---

### Paso 11: Registrar tus agentes en CLAUDE.md

**⚠️ PASO CRÍTICO**: Después de crear tus agentes especializados, **debes registrarlos en CLAUDE.md** para que el agente padre sepa que existen y pueda invocarlos.

**Por qué es importante**:
- El agente padre lee CLAUDE.md para descubrir agentes especializados disponibles
- Sin registro, el agente padre no sabrá a qué agentes puede delegar
- Así es como funciona la orquestación automática

**Cómo registrar agentes**:

1. **Abre `CLAUDE.md`** en la raíz de tu repositorio

2. **Encuentra la sección "Available agents in this project"** (alrededor de la línea 40-50)

3. **Agrega tus agentes a la lista**:

```markdown
**Available agents in this project:**
- **UI/Frontend** → `.claude/agents/ui-architect.md`
- **Database** → `.claude/agents/database-engineer.md`
- **Business Logic** → `.claude/agents/domain-architect.md`
- **Refactoring** → `.claude/agents/migration-agent.md`
```

**⚠️ Importante**:
- Actualiza esta lista cada vez que crees un nuevo agente
- Mantén el formato consistente: `**{Tipo de Tarea}** → {ruta-al-archivo-agente}`
- Usa tipos de tarea claros para que el agente padre sepa cuándo usar cada agente

**Ejemplo de descubrimiento del agente padre**:

Cuando dices: "Implementa la vista de inicio de sesión"

El agente padre:
1. Lee CLAUDE.md
2. Ve "UI/Frontend → .claude/agents/ui-architect.md"
3. Entiende que tiene un especialista en UI disponible
4. Invoca ese agente para crear el plan de UI

Sin este registro, el agente padre intentaría hacer todo por sí mismo en lugar de delegar a especialistas.

---

### Paso 12: Configurar protocolo de sesiones

Crea `.claude/tasks/README.md`

**Propósito**: Explicar el protocolo de sesiones de solo-agregar

**Qué incluir**:

1. **Concepto de sesión**
   - Para qué son las sesiones
   - Por qué solo-agregar (optimización de KV-cache)
   - Cuándo crear sesiones

2. **Ciclo de vida de sesión**
   - Crear una sesión
   - Sesiones activas
   - Completar sesiones
   - Archivar sesiones antiguas

3. **Formato de entrada**
   - Campos requeridos
   - Valores de estado
   - Presupuesto de tokens por entrada

4. **Mejores prácticas**
   - Qué incluir
   - Qué omitir
   - Cómo mantener entradas concisas

**Plantilla**: Ver `.claude/tasks/README.md`

**También crear**: `.claude/tasks/context_session_template.md`

---

### Paso 13: Flujo de trabajo - Juntando todo

Ahora que toda la configuración está en su lugar, aquí está el flujo de trabajo completo para construir características:

#### El flujo de trabajo de orquestación automática

La belleza de esta metodología es que **el agente padre orquesta todo automáticamente**. No necesitas crear archivos de sesión manualmente ni decidir qué agentes especializados invocar.

**Tu flujo de trabajo simple**:

**1. Describe lo que quieres**

Simplemente dile a Claude Code qué característica quieres implementar:

```
Implementa la vista de inicio de sesión.

Recuerda que tienes sub-agentes especializados que puedes usar si es necesario.
Recuerda que seguimos un marco de trabajo estricto.
```

**¡Eso es todo!** El agente padre automáticamente:

1. **Crea el archivo de sesión** con un session_id único
2. **Analiza la solicitud** y determina qué agentes especializados se necesitan
3. **Invoca agentes especializados** (arquitecto UI, ingeniero de base de datos, etc.)
4. **Recibe los planes** de los agentes especializados
5. **Actualiza el contexto de sesión** con resúmenes de planes (solo-agregar)
6. **Ejecuta los planes** paso a paso
7. **Actualiza el contexto de sesión** después de cada fase completada (solo-agregar)

**Ejemplo de lo que sucede detrás de escena**:

```
Tú: "Implementa la vista de inicio de sesión"

Agente Padre:
  ↓
  Crea: .claude/tasks/context_session_1729012345.md
  ↓
  Invoca: agente ui-architect
  ↓
  Recibe: .claude/plans/ui-login-view-plan.md
  ↓
  Actualiza: contexto de sesión con resumen del plan
  ↓
  Ejecuta: Paso 1 del plan (crear componente LoginForm)
  ↓
  Actualiza: contexto de sesión (Paso 1 completado)
  ↓
  Ejecuta: Paso 2 del plan (crear lógica de validación)
  ↓
  Actualiza: contexto de sesión (Paso 2 completado)
  ↓
  ... continúa hasta que la característica esté completa
  ↓
  Actualiza: contexto de sesión (Característica completada)
```

**Recordatorios clave para incluir en tus prompts**:

1. **"Recuerda que tienes sub-agentes especializados que puedes usar si es necesario"**
   - Recuerda al agente padre delegar a especialistas

2. **"Recuerda que seguimos un marco de trabajo estricto"**
   - Asegura que el agente siga el flujo de trabajo de dos fases
   - Crea planes antes de la implementación
   - Actualiza el contexto de sesión apropiadamente

**⚠️ Importante: Implementa características incrementalmente**

**NO** intentes construir toda tu aplicación en un solo prompt:
```
❌ "Construye la aplicación completa de gestión de tareas con autenticación,
    dashboard, operaciones CRUD y reportes"
```

**SÍ** implementa características una por una:
```
✅ "Implementa la vista de inicio de sesión"
✅ "Implementa el flujo de registro de usuario"
✅ "Implementa la vista de lista de tareas"
✅ "Implementa la funcionalidad de creación de tareas"
```

**¿Por qué incremental?**
- Mejor calidad por característica
- Más fácil de revisar y probar
- Más fácil de arreglar si algo sale mal
- Contextos de sesión más mantenibles
- Mejor eficiencia de tokens

#### Para cambios triviales

Omite el flujo de trabajo para cambios simples:
- Correcciones de typos
- Ajustes menores de estilo
- Actualizaciones de documentación
- Correcciones simples de bugs

Simplemente dile a Claude Code directamente qué arreglar:
```
Corrige el typo en el texto del botón de inicio de sesión
```

---

### Paso 14: Iterar y mejorar

A medida que uses esta metodología:

**1. Rastrear qué funciona**
- Notar patrones que mejoran la calidad
- Documentar configuraciones exitosas de agentes
- Actualizar critical-constraints.md con nuevas reglas

**2. Optimizar uso de tokens**
- Monitorear qué documentos consumen más tokens
- Refinar context-strategy.md
- Extraer secciones frecuentemente usadas a documentos más pequeños

**3. Refinar agentes**
- Mejorar instrucciones de agentes basado en resultados
- Añadir restricciones para errores comunes
- Ajustar acceso MCP según sea necesario

**4. Actualizar documentación**
- Mantener business-rules.md actualizado con nuevas reglas
- Actualizar architecture-patterns.md cuando los patrones evolucionen
- Mantener tech-stack.md con actualizaciones de versiones

**5. Archivar sesiones antiguas**
- Mover sesiones completadas a archive/ después de 30 días
- Previene sobrecarga de contexto

---

## Problemas comunes y soluciones

### Problema 1: El agente no sigue restricciones críticas

**Problema**: El agente viola reglas arquitectónicas o patrones críticos

**Causas**:
- No leyó critical-constraints.md primero
- Restricciones no suficientemente explícitas
- Instrucciones conflictivas en diferentes docs

**Soluciones**:
1. **En CLAUDE.md**, hacer lectura de critical-constraints.md la PRIMERA instrucción
2. Añadir ejemplos explícitos a critical-constraints.md (❌ vs ✅)
3. Usar lenguaje fuerte: "DEBE", "NUNCA", "SIEMPRE"
4. Añadir restricción a la definición del propio agente
5. Revisar todos los docs por guía conflictiva

---

### Problema 2: Uso excesivo de tokens

**Problema**: Consumo de contexto demasiado alto, respuestas lentas, costos altos

**Causas**:
- Cargar documentos completos en lugar de buscar
- Todos los servidores MCP habilitados simultáneamente
- Archivos de contexto de sesión demasiado grandes
- Docs de conocimiento no optimizados

**Soluciones**:

1. **Optimizar configuración MCP**
2. **Usar Grep en lugar de lecturas completas**
3. **Comprimir docs de conocimiento**
4. **Mantener entradas de sesión concisas**
5. **Monitorear tamaño de CLAUDE.md**

---

### Problema 3: Los agentes no siguen el flujo de trabajo

**Problema**: Los agentes implementan código directamente en lugar de crear planes

**Soluciones**:
1. **En definición de agente**, ser extremadamente explícito
2. **En herramientas permitidas**, restringir Write
3. **En misión**, enfatizar salida

---

### Problema 4: Pérdida de contexto entre sesiones

**Problema**: Nuevas invocaciones de agentes no tienen contexto de trabajo previo

**Soluciones**:
1. **Siempre proporcionar session_id**
2. **Mejorar calidad de entrada de sesión**
3. **Enlazar planes en sesión**
4. **En CLAUDE.md**, instruir a agentes a verificar por sesión

---

### Problema 5: Arquitectura inconsistente

**Problema**: El código no sigue patrones arquitectónicos consistentemente

**Soluciones**:
1. **Añadir diagrama visual de dependencias**
2. **Añadir ejemplos de antes/después**
3. **Añadir a restricciones críticas**

---

### Problema 6: Planes demasiado genéricos

**Problema**: Los planes de agentes carecen de detalle necesario para implementación

**Soluciones**:
1. **Definir secciones requeridas del plan** en definición de agente
2. **Proporcionar ejemplo de plan** en definición de agente
3. **En misión**, enfatizar investigación

---

## Recursos

### Documentación oficial

- **Docs de Claude Code**: https://docs.claude.com/claude-code
- **Documentación MCP**: https://modelcontextprotocol.io
- **Ingeniería de prompts de Anthropic**: https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering

### Esta metodología

- **CLAUDE.md**: Punto de entrada para agentes de IA
- **.claude/knowledge/**: Documentación optimizada en tokens
- **.claude/agents/**: Definiciones de agentes especializados
- **.claude/tasks/**: Archivos de contexto de sesión

### Lectura recomendada

- **Clean Architecture** por Robert C. Martin (si usas Arquitectura Limpia)
- **Domain-Driven Design** por Eric Evans (para dominios complejos)
- **Guía de ingeniería de contexto de Anthropic**: Estrategias de optimización de tokens

### Comunidad

- Comparte tu experiencia con esta metodología
- Contribuye mejoras vía pull requests
- Reporta problemas o sugiere mejoras

---

## Próximos pasos

1. **Comenzar con el Paso 0**: Define tu proyecto con Claude Code
2. **Seguir Pasos 1-13**: Configurar la metodología completa
3. **Crear tu primer agente**: Comenzar con un agente especializado
4. **Construir una característica**: Usar el flujo de trabajo para implementar algo real
5. **Iterar**: Refinar basado en tu experiencia

Recuerda: Esto es una **metodología**, no un framework rígido. Adáptala a las necesidades de tu proyecto mientras mantienes los principios centrales:

- ✅ Eficiencia de tokens mediante carga perezosa
- ✅ Agentes especializados con responsabilidades claras
- ✅ Flujo de trabajo de dos fases (planificación → ejecución)
- ✅ Contextos de sesión de solo-agregar
- ✅ Restricciones críticas aplicadas
- ✅ Patrones arquitectónicos documentados

---

## Autor

<div align="center">

**Cristian Camilo Peña**
Ingeniero de Software

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Conectar-blue?style=flat&logo=linkedin)](https://www.linkedin.com/in/cristiancamilopena/)
[![GitHub](https://img.shields.io/badge/GitHub-Seguir-181717?style=flat&logo=github)](https://github.com/cristianps1988)

</div>

Construí esta metodología mientras trabajaba en un proyecto complejo donde los prompts simples de IA no eran suficientes. Después de mucho ensayo y error, encontré enfoques que funcionaron bien para mantener consistencia y calidad en el desarrollo asistido por IA.

Este es mi intento de documentar lo que funcionó para mí. No es perfecto, y estoy seguro de que hay mejores formas de hacer algunas cosas. Lo comparto por si a otros les resulta útil para sus propios proyectos.

**No dudes en contactarme:**
- Conéctate en [LinkedIn](https://www.linkedin.com/in/cristiancamilopena/)
- Sígueme en [GitHub](https://github.com/cristianps1988)

Me encantaría escuchar sobre tus experiencias - qué funcionó, qué no, y cómo lo adaptaste para tus necesidades.

---

## Licencia

Este proyecto está licenciado bajo la Licencia MIT - ver el archivo [LICENSE](LICENSE) para detalles.

Eres libre de:
- ✅ Usar esta metodología en proyectos personales o comerciales
- ✅ Modificar y adaptar a tus necesidades
- ✅ Compartir y distribuir
- ✅ Crear trabajos derivados

---

## Contribuir

¡Las contribuciones son bienvenidas! Si tienes mejoras, sugerencias o encontraste problemas:

1. Abre un issue para discutir cambios propuestos
2. Haz fork del repositorio
3. Crea tu rama de característica
4. Envía un pull request

---

## Agradecimientos

Esta metodología fue desarrollada a través de experiencia práctica construyendo aplicaciones del mundo real con Claude Code y desarrollo asistido por IA.

Agradecimientos especiales a:
- Anthropic por Claude Code y la plataforma de IA Claude
- Los principios de ingeniería de prompts y optimización de contexto de las guías de Anthropic
- La comunidad de desarrolladores por compartir prácticas de desarrollo asistido por IA

---

**¡Feliz construcción con IA! 🚀**
