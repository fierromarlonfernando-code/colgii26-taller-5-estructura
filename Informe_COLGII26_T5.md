# Informe de Laboratorio: Optimización de Pipeline MIPS

**Nombre del Estudiante:** Fierro Marlon Fernando  
**Fecha:** 31 de agosto de 2026  
**Asignatura:** Estructura de Computadores - Homologación Colombia  
**Taller:** 5 - Optimización de Pipeline en Procesadores MIPS

**Enlace del repositorio en GitHub:** https://github.com/fierromarlonfernando-code/colgii26-taller-5-estructura

---

## 1. Análisis del Código Base

### 1.1 Descripción General

El programa calcula: **Y[i] = A × X[i] + B** para un vector de 8 elementos usando MIPS.

**Datos iniciales:**
- Vector X: [1, 2, 3, 4, 5, 6, 7, 8]
- Constante A: 3
- Constante B: 5
- Tamaño: 8 elementos

**Salida esperada:**
- Y[0] = 3×1 + 5 = 8
- Y[1] = 3×2 + 5 = 11
- Y[2] = 3×3 + 5 = 14
- ... Y[7] = 3×8 + 5 = 29

### 1.2 Ejecución y Estadísticas (Código Base)

#### Resultados de MARS:

**Instruction Statistics:**
| Métrica | Valor | Porcentaje |
|---------|-------|-----------|
| **Total Instrucciones** | 94 | 100% |
| ALU | 25 | 27% |
| Jump | 8 | 9% |
| Branch | 9 | 10% |
| Memory | 19 | 20% |
| Other | 33 | 35% |

**Instruction Counter:**
| Tipo | Cantidad | Porcentaje |
|------|----------|-----------|
| R-type | 41 | 43% |
| I-type | 45 | 47% |
| J-type | 8 | 8% |

### 1.3 Identificación de Riesgos (Hazards)

Se identificaron **dos riesgos principales** en el bucle:

| Línea | Instrucción Causante | Instrucción Afectada | Tipo de Riesgo | Ciclos de Parada | Explicación |
|-------|----------------------|----------------------|-----------------|-----------------|-------------|
| 33 | `lw $t6, 0($t5)` | `mul $t7, $t6, $t0` | **Load-Use** | 1 | El `mul` usa $t6 inmediatamente después del `lw`. Necesita esperar a que el dato llegue de memoria |
| 37 | `mul $t7, $t6, $t0` | `addu $t8, $t7, $t1` | **Raw (Read-After-Write)** | 1 | El `addu` usa $t7 que acaba de producir `mul`. Dependencia de datos directa |

**Análisis Teórico de Ciclos (Código Base):**

```
Bucle (8 iteraciones):
  - Instrucciones por iteración: ~11-12
  - Stalls por iteración: ~2 (Load-Use + Raw dependency)
  - Total ciclos por iteración: ~13-14
  - Total iteraciones: 8
  
Estimación de ciclos teóricos:
  - Instrucciones: 94
  - Stalls (8 iter × 2 stalls): 16
  - Ciclos estimados: 94 + 16 = 110 ciclos
  - CPI (Ciclos/Instrucción): 110/94 = 1.17
```

---

## 2. Optimización Propuesta

### 2.1 Estrategia de Optimización

**Objetivo:** Eliminar o reducir los stalls intercalando instrucciones **independientes** entre el `lw` y el `mul`.

**Instrucción movida:**
```asm
addu $t9, $s1, $t4    # Calcula dirección de Y[i]
```

Esta instrucción es **completamente independiente** porque:
- ✅ No depende de $t6 (que viene del `lw`)
- ✅ No depende de $t7 (que viene del `mul`)
- ✅ Solo usa $s1 (cargado en inicialización) y $t4 (calculado en `sll`)

### 2.2 Código Optimizado

**Bucle original (con riesgos):**
```asm
loop:
    beq $t3, $t2, fin
    sll $t4, $t3, 2
    addu $t5, $s0, $t4
    
    lw $t6, 0($t5)           # ← Carga
    mul $t7, $t6, $t0        # ← Stall: espera $t6
    addu $t8, $t7, $t1       # ← Stall: espera $t7
    
    addu $t9, $s1, $t4       # Cálculo de dirección (independiente)
    sw $t8, 0($t9)
    
    addi $t3, $t3, 1
    j loop
```

**Bucle optimizado (sin riesgos):**
```asm
loop:
    beq $t3, $t2, fin
    sll $t4, $t3, 2
    addu $t5, $s0, $t4
    
    lw $t6, 0($t5)           # ← Carga
    addu $t9, $s1, $t4       # ← INTERCALADA: independiente, sin stall
    mul $t7, $t6, $t0        # ← Ya hay algo en el pipeline
    addu $t8, $t7, $t1
    
    sw $t8, 0($t9)
    
    addi $t3, $t3, 1
    j loop
```

### 2.3 Justificación Técnica

**¿Por qué funciona esta optimización?**

1. **Pipeline de 5 etapas típico (IF, ID, EX, MEM, WB):**
   - `lw` necesita 2 ciclos adicionales (MEM toma tiempo)
   - Sin intercalar: `mul` en EX espera a que $t6 esté listo → **STALL**
   - Con intercalar: `addu` independiente va en EX mientras `lw` está en MEM → **Sin STALL**

2. **Eliminación del Load-Use Hazard:**
   - En el código original hay **1 ciclo de parada** entre `lw` y `mul`
   - Al intercalar `addu $t9, $s1, $t4`, llenamos ese delay slot
   - Resultado: **0 stalls adicionales**

3. **Instrucción siguiente aprovecha el resultado:**
   - El `mul` ahora puede comenzar sin esperar
   - La dependencia `mul → addu` sigue siendo normal (1 stall), pero se superpone mejor

### 2.4 Ejecución del Código Optimizado en MARS

#### Resultados de MARS:

**Instruction Statistics:**
| Métrica | Valor | Porcentaje |
|---------|-------|-----------|
| **Total Instrucciones** | 94 | 100% |
| ALU | 25 | 27% |
| Jump | 8 | 9% |
| Branch | 9 | 10% |
| Memory | 19 | 20% |
| Other | 33 | 35% |

**Instruction Counter:**
| Tipo | Cantidad | Porcentaje |
|------|----------|-----------|
| R-type | 41 | 43% |
| I-type | 45 | 47% |
| J-type | 8 | 8% |

**Nota:** MARS es un simulador **funcional**, no temporal. Por eso el número de instrucciones es idéntico. En un procesador real, los ciclos serían diferentes.

---

## 3. Análisis Comparativo Teórico

### 3.1 Tabla de Comparativa (Estimaciones Teóricas)

| Métrica | Código Base | Código Optimizado | Mejora |
|---------|-------------|-------------------|--------|
| **Instrucciones totales** | 94 | 94 | - (sin cambio) |
| **Stalls por iteración** | 2 | 1 | ↓ 50% |
| **Total Stalls (8 iteraciones)** | 16 | 8 | ↓ 50% |
| **Ciclos totales estimados** | 110 | 102 | ↓ 7.3% |
| **CPI (Ciclos/Instrucción)** | 1.17 | 1.09 | ↓ 6.8% |

**Cálculo detallado:**

**Código Base:**
```
- Instrucciones: 94
- Load-Use stalls: 8 (1 por iteración)
- Raw dependency stalls: 8 (1 por iteración)
- Total stalls: 16
- Ciclos: 94 + 16 = 110
- CPI: 110 / 94 = 1.17
```

**Código Optimizado:**
```
- Instrucciones: 94
- Load-Use stalls: 0 (intercalamos addu)
- Raw dependency stalls: 8 (aún necesario)
- Total stalls: 8
- Ciclos: 94 + 8 = 102
- CPI: 102 / 94 = 1.09
```

### 3.2 Interpretación de Resultados

✅ **Mejoras logradas:**

1. **Reducción de stalls:** De 16 a 8 → **50% menos paradas**
2. **Reducción de ciclos:** De 110 a 102 → **8 ciclos menos**
3. **Mejora de CPI:** De 1.17 a 1.09 → **6.8% más eficiente**

**¿Por qué no eliminamos todos los stalls?**
- La dependencia `mul → addu` sigue siendo obligatoria
- No hay suficientes instrucciones independientes para llenar ese slot
- Sería necesario un algoritmo completamente diferente (desenrollado, vectorizado, etc.)

---

## 4. Impacto de la Segmentación en Programación de Bajo Nivel

### 4.1 Conclusiones

**1. La segmentación es una espada de doble filo:**
- ✅ **Ventaja:** Permite ejecutar múltiples instrucciones simultáneamente
- ❌ **Desventaja:** Crea dependencias y riesgos que ralentizan el código

**2. Necesidad de optimización manual en ensamblador:**
- Los compiladores modernos pueden hacer esto, pero no siempre optimizan perfectamente
- En código crítico de bajo nivel, reordenar instrucciones manualmente puede mejorar significativamente

**3. No siempre es posible eliminar todas las paradas:**
- Hay dependencias **verdaderas (RAW)** que son inevitables
- Solo podemos mitigarlas reordenando código independiente
- En algunos casos, técnicas como **speculative execution** o **out-of-order execution** son necesarias

**4. La longitud del pipeline importa:**
- Pipelines más largos (15-20 etapas en CPUs modernas) tienen más oportunidades de reorden
- Pero también tienen más riesgos potenciales

### 4.2 Lecciones Aprendidas

- ✅ Entender el datapath y el pipeline es crucial para optimizar código de bajo nivel
- ✅ Reordenar instrucciones independientes puede reducir stalls significativamente
- ✅ No todas las mejoras teóricas se ven en simuladores funcionales (MARS)
- ✅ En procesadores reales, esta optimización daría speedup de ~7%

---

## 5. Anexos

### 5.1 Archivos Incluidos

- `codigo/programa_base.asm` - Código original sin optimizar
- `codigo/programa_optimizado.asm` - Código con intercalación de instrucciones
- `docs/` - Documentación y referencias
- `capturas/` - Screenshots de MARS (Statistics, Counter, X-Ray)
- `transcripciones/` - Transcripciones de clases

### 5.2 Referencias

- MARS MIPS Simulator: https://courses.missouristate.edu/kenvollmar/mars/
- MIPS Instruction Set Reference: Ver `docs/Referencia_Instrucciones_MIPS.pdf`
- Pipeline Concepts: Ver `docs/Conceptos_Basicos_Laboratorio.pdf`

---

**Conclusión Final:**

La optimización propuesta demuestra cómo un profundo entendimiento de la arquitectura de pipelines permite escribir código más eficiente. Aunque MARS no simula los ciclos reales, el análisis teórico muestra una **mejora de 7% en ciclos totales y 50% de reducción en stalls**. En aplicaciones reales con millones de instrucciones, esta optimización tendría un impacto significativo en rendimiento.

**Fin del informe.**

---

*Último actualizado: 31 de agosto de 2026*
