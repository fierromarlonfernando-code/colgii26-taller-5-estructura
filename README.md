# COLGII Taller 5: Optimización de Pipeline en Procesadores MIPS

**Curso:** Estructura de Computadores  
**Homologación:** Título Universidad de Colombia  
**Taller:** 5 - Optimización de Pipeline MIPS  
**Objetivo:** Identificar y eliminar riesgos de datos en código MIPS optimizando el pipeline de procesadores.

---

## 📁 Estructura del Repositorio

```
colgii26-taller-5-estructura/
├── README.md                          # Este archivo
├── Plantilla_Informe.md              # Plantilla para el informe
├── INSTRUCCIONES.md                   # Instrucciones detalladas
├── codigo/                            # Código MIPS
│   ├── programa_base.asm             # Código base (sin optimizar)
│   └── programa_optimizado.asm       # Código optimizado (por completar)
├── docs/                              # Documentación
│   ├── Guia_Laboratorio.pdf
│   ├── Conceptos_Basicos_Laboratorio.pdf
│   ├── Referencia_Instrucciones_MIPS.pdf
│   └── Tutorial_MARS.pdf
├── transcripciones/                   # Transcripciones de clases
│   ├── 594860.vtt
│   └── 595411.vtt
├── herramientas/                      # Simulador MARS
│   └── Mars4_5.jar
└── capturas/                          # Capturas de pantalla de MARS (por añadir)
    ├── base_xray.png
    ├── base_stats.png
    ├── optimizado_xray.png
    └── optimizado_stats.png
```

---

## 🎯 Objetivo del Taller

El programa calcula: **Y[i] = A × X[i] + B** para un vector de 8 elementos.

**Problemas a identificar:**
- Riesgos **Load-Use**: `lw` → `mul` (dependencia de lectura)
- Riesgos de **dependencias consecutivas**
- **Paradas de pipeline** en la ejecución

**Solución:**
- Reordenar instrucciones para eliminar hazards
- Mover instrucciones independientes entre `lw` y `mul`

---

## 🚀 Cómo Ejecutar

### 1. Abrir MARS Simulator

```bash
java -jar herramientas/Mars4_5.jar
```

### 2. Cargar y ejecutar programa base

1. **File → Open** → Seleccionar `codigo/programa_base.asm`
2. **Run → Assemble** (o Ctrl+F3)
3. **Run → Go** (o F5) para ejecutar

### 3. Visualizar estadísticas

En el menú **Tools**, habilita:
- ✅ **MIPS X-Ray** (Datapath animado)
- ✅ **Instruction Counter** (Contador total)
- ✅ **Instruction Statistics** (Desglose por tipo)

---

## 📊 Análisis Requerido

### Tabla 1: Identificar Riesgos

| Instrucción | Línea | Tipo de Riesgo | Ciclos de Parada |
|-------------|-------|----------------|------------------|
| `lw $t6` | 33 | Load-Use | 1 |
| `mul $t7` | 37 | Dependencia | 1 |
| ... | ... | ... | ... |

### Tabla 2: Comparativa Base vs Optimizado

| Métrica | Base | Optimizado | Mejora |
|---------|------|-----------|--------|
| Instrucciones Totales | ? | ? | ? |
| Stalls (Paradas) | ? | ? | ? |
| Ciclos Estimados | ? | ? | ? |
| CPI (Ciclos/Instrucción) | ? | ? | ? |

---

## ✏️ Optimización Propuesta

**Estrategia:** Reordenar instrucciones para llenar los "slots vacíos" entre `lw` y operaciones que dependan del valor cargado.

**Ejemplo (sin garantizar es correcto):**

```asm
# Original (con hazards)
lw $t6, 0($t5)          # Carga X[i]
mul $t7, $t6, $t0       # Usa inmediatamente X[i] - STALL

# Optimizado (intercalar instrucciones independientes)
lw $t6, 0($t5)          # Carga X[i]
addi $t3, $t3, 1        # Incremento independiente
mul $t7, $t6, $t0       # Usa X[i] (sin stall extra)
```

---

## 📝 Entrega

1. **`codigo/programa_optimizado.asm`** - Código MIPS optimizado
2. **`Informe_COLGII26_T5.md`** - Informe completado con:
   - Capturas de MARS X-Ray y estadísticas
   - Tabla de hazards identificados
   - Análisis de mejora
   - Conclusiones
3. **`capturas/`** - Screenshots de la ejecución
4. **Commit todo a GitHub**

---

## 🔧 Instalación de MARS (si es necesario)

**Requisitos:** Java Runtime Environment (JRE) 8 o superior

```bash
# Descargar MARS (si no lo tienes)
# https://courses.missouristate.edu/kenvollmar/mars/download.htm

# Ejecutar directamente
java -jar Mars4_5.jar
```

---

## 📚 Recursos Útiles

- **Tutorial MARS**: Ver `docs/Tutorial_MARS.pdf`
- **Guía Laboratorio**: Ver `docs/Guia_Laboratorio.pdf`
- **Referencia MIPS**: Ver `docs/Referencia_Instrucciones_MIPS.pdf`
- **Transcripciones de Clase**: Ver `transcripciones/`

---

## ✅ Checklist de Entrega

- [ ] Código base ejecuta correctamente en MARS
- [ ] Identificadas todas las dependencias de datos
- [ ] Código optimizado creado y sin errores de sintaxis
- [ ] Código optimizado ejecuta correctamente en MARS
- [ ] Capturas de pantalla de MARS agregadas
- [ ] Tablas completadas (hazards, comparativa)
- [ ] Informe redactado (justificación técnica)
- [ ] Todo commiteado a GitHub
- [ ] README visible en la rama principal

---

## 📧 Notas

- Asegúrate de que **MARS ejecute sin errores** antes de capturar pantallas
- Compara ciclos **teóricos** (base + stalls) vs optimizados
- Documenta **qué instrucción moviste y por qué**
- El objetivo es **reducir paradas (stalls)**, no eliminarlas completamente

---

**Última actualización:** 2026-08-31  
**Estado:** Por completar optimización
