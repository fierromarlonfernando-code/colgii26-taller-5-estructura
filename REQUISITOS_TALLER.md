# Laboratorio 1: Simulación y Optimización de un Programa en un Procesador Escalar Segmentado

## Preparación para el Laboratorio

Para realizar esta práctica se requiere utilizar MARS (MIPS Assembler and Runtime Simulator), que es un IDE para MIPS. El simulador se proporciona como un archivo Executable Jar File (Mars4_5.jar) que se encuentra en la carpeta de herramientas.

### Características de MARS

- Interfaz gráfica de usuario con control "apuntar y hacer clic" y editor integrado.
- Valores de registro y memoria fácilmente editables, similares a los de una hoja de cálculo.
- Visualización de valores en hexadecimal o decimal.
- Modo de línea de comandos para prueba y evaluación de programas.
- Registros de coma flotante y coprocesador 1 y 2.
- Herramientas estándar de visualización y edición a nivel de bits de registros de coma flotante de 32 bits.
- Ejecución en un solo paso a velocidad variable.
- Utilidad "Tool" para el control MIPS de dispositivos simulados.

### Documentación Incluida

Los siguientes documentos son necesarios para la realización del laboratorio:

- Conceptos Básicos del Laboratorio: Arquitecturas Segmentadas (PDF)
- Guía de Laboratorio: Optimización en Procesadores Segmentados (PDF)
- Plantilla Informe (Markdown)
- Tutorial MARS (PDF)
- Guía Rápida de Instrucciones MIPS Assembler (PDF)
- Archivo con el código Assembler (programa_base.asm)

## Conceptos Previos

### Assembler

El lenguaje Assembler es un lenguaje de programación de bajo nivel que se utiliza para escribir programas que se ejecutan directamente en un computador o en otros dispositivos electrónicos. Los programas escritos en ensamblador son traducidos a lenguaje de máquina, que es el lenguaje que entiende el procesador del computador.

A diferencia de los lenguajes de programación de alto nivel, como C++ o Java, que se enfocan en la abstracción y la simplificación del proceso de programación, el ensamblador es un lenguaje de programación muy cercano a la arquitectura del procesador. Esto significa que los programas escritos en ensamblador son muy eficientes y rápidos, ya que se aprovechan al máximo las capacidades del procesador y se pueden controlar todos los detalles del hardware. Los archivos de este lenguaje tienen la extensión *.asm.

## Descripción del Laboratorio

En esta práctica se trabaja con un lenguaje de programación de bajo nivel llamado Assembler, siguiendo cada uno de los pasos propuestos en la guía del laboratorio. El objetivo es identificar riesgos de datos (hazards) en un programa MIPS y proponer optimizaciones para reducir las paradas de pipeline.

## Entrega del Laboratorio

Todos los lineamientos de entrega deben cumplirse para la revisión y evaluación de la actividad.

### Requisitos de Entrega

Lo primero que se debe realizar antes de hacer el envío de la actividad es cargar los dos scripts de la actividad en formato *.asm (programa_base y programa_optimizado) en GitHub y compartir el enlace del repositorio en el documento de entrega.

**Nomenclatura del repositorio:** El nombre del repositorio debe seguir el patrón: nombre-apellido-estructura-computadores-act01

**Independencia de archivos:** Cada uno de los scripts debe ser presentado en archivos independientes.

**Código comentado:** Cada una de las líneas debe estar comentada con la respectiva descripción de qué realiza cada instrucción.

**Importante:** Para que sea evaluada la actividad, los scripts deben estar cargados en GitHub y la actividad debe ser presentada utilizando la Plantilla_Informe en formato PDF.

### Nota sobre Trabajo Grupal

Si la actividad se realiza de forma grupal, todos los integrantes deben publicar el enlace a su respectivo perfil en GitHub e incluir los enlaces al final del informe.

## Rúbrica de Evaluación

| Criterio de Evaluación | Descripción | Porcentaje |
|---|---|---|
| Análisis del código base | Identificación de riesgos y explicación técnica | 20% |
| Optimización del código | Aplica técnicas de optimización eliminando stalls | 30% |
| Resultados y métricas | Tablas comparativas con datos correctos del cálculo | 20% |
| Justificación técnica | Explicación técnica del proceso de optimización aplicado | 20% |
| Formato y presentación | Utiliza plantilla informe y carga código en GitHub | 10% |

---

**Total:** 100%
