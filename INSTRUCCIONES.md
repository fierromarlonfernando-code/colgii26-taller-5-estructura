# Instrucciones Paso a Paso - COLGII Taller 5

## Fase 1: Análisis del Código Base

### Paso 1: Ejecutar el programa base

1. Abre **MARS Simulator**: `java -jar herramientas/Mars4_5.jar`
2. **File → Open** → selecciona `codigo/programa_base.asm`
3. **Run → Assemble** (Ctrl+F3) para compilar
4. Si hay errores, corrígelos

### Paso 2: Ejecutar y recopilar datos

1. **Tools** → habilita:
   - ☑ **MIPS X-Ray** (ver el datapath animado)
   - ☑ **Instruction Counter** (contar instrucciones)
   - ☑ **Instruction Statistics** (ver estadísticas)

2. **Run → Go** (F5) para ejecutar el programa completo
3. **Captura pantallazos** de:
   - Ventana X-Ray con el datapath
   - Ventana de estadísticas (tipo de instrucciones)
   - Instruction Counter (total de instrucciones)

### Paso 3: Identificar riesgos (hazards)

Analiza el código y completa la tabla:

**Riesgos encontrados en `programa_base.asm`:**

| Línea | Instrucción | Depende De | Tipo | Stalls |
|-------|-------------|-----------|------|--------|
| 33 | `lw $t6, 0($t5)` | (carga) | (base) | 0 |
| 37 | `mul $t7, $t6, $t0` | $t6 | Load-Use | 1 |
| 38 | `addu $t8, $t7, $t1` | $t7 | Raw (Read-After-Write) | 1 |
| ... | ... | ... | ... | ... |

**Explicación:**
- **Load-Use**: Una instrucción `lw` seguida inmediatamente de operación que usa ese registro
- **Raw (Read-After-Write)**: Dependencia real entre dos instrucciones consecutivas
- **Stalls**: Ciclos de parada del pipeline

---

## Fase 2: Crear Versión Optimizada

### Paso 4: Escribir `programa_optimizado.asm`

**Estrategia de optimización:**

El objetivo es **reordenar instrucciones independientes** entre `lw` y `mul` para llenar los "delay slots" del pipeline.

**Instrucciones independientes** que puedes mover:
- `sll $t4, $t3, 2` (calcula desplazamiento del siguiente elemento)
- `addu $t5, $s0, $t4` (calcula dirección del siguiente elemento)
- `addi $t3, $t3, 1` (incremento del contador)

**Código optimizado ejemplo:**

```asm
loop:
    beq $t3, $t2, fin        # Si i == tamano, salir
    
    sll $t4, $t3, 2          # t4 = i * 4
    addu $t5, $s0, $t4       # t5 = dirección de X[i]
    
    lw $t6, 0($t5)           # Leer X[i]
    # ← AQUÍ puedes poner una instrucción independiente
    # ← para evitar el stall Load-Use
    addu $t9, $s1, $t4       # t9 = dirección de Y[i] (INDEPENDIENTE)
    
    mul $t7, $t6, $t0        # t7 = X[i] * A (ahora con menos stall)
    addu $t8, $t7, $t1       # t8 = t7 + B
    
    sw $t8, 0($t9)           # Guardar resultado en Y[i]
    
    addi $t3, $t3, 1         # i = i + 1
    j loop                    # Siguiente iteración

fin:
    li $v0, 10
    syscall
```

**¿Qué hicimos?**
- Movimos `addu $t9, $s1, $t4` (calcular dirección de escritura) **entre** `lw` y `mul`
- Esta instrucción es **independiente**, no usa $t6
- Así el pipeline no está vacío durante el delay slot del `lw`

### Paso 5: Validar que no hay errores de sintaxis

1. Crea el archivo `codigo/programa_optimizado.asm` con tu código
2. En MARS: **File → Open** → `programa_optimizado.asm`
3. **Run → Assemble** (Ctrl+F3)
4. ✅ Debe compilar sin errores

---

## Fase 3: Análisis Comparativo

### Paso 6: Ejecutar programa optimizado

1. Con `programa_optimizado.asm` abierto
2. **Run → Go** (F5)
3. Captura los mismos gráficos:
   - X-Ray
   - Estadísticas
   - Instruction Counter

### Paso 7: Completar tabla comparativa

**Tabla de resultados:**

| Métrica | Código Base | Código Optimizado | Mejora |
|---------|-------------|-------------------|--------|
| **Instrucciones (según MARS)** | ? | ? | - |
| **Stalls por iteración** | ~2-3 | ~0-1 | ↓ 50% |
| **Total de Stalls (8 iter)** | ~16-24 | ~0-8 | ↓ 50% |
| **Ciclos estimados** | Inst + 24 | Inst + 8 | ↓ 67% |
| **CPI (Ciclos/Instrucción)** | ~1.5-2.0 | ~1.0-1.2 | ↓ 40% |

*Nota: Estos son valores teóricos. MARS es un simulador funcional, no temporal, así que verás el mismo número de instrucciones pero el análisis teórico muestra la mejora.*

---

## Fase 4: Redactar Informe

### Paso 8: Completar `Informe_COLGII26_T5.md`

Usa como plantilla `Plantilla_Informe.md` y completa:

1. **Nombre del estudiante**
2. **Análisis del Código Base**
   - Inserta capturas de MARS
   - Tabla de hazards
   - Análisis teórico

3. **Optimización Propuesta**
   - Inserta tu código optimizado
   - Inserta capturas de MARS para el optimizado
   - **Justificación técnica**: explica qué instrucción moviste y por qué

4. **Comparativa de Resultados**
   - Llena la tabla con datos reales/estimados

5. **Conclusiones**
   - ¿Qué aprendiste sobre pipelines?
   - ¿Es siempre posible eliminar todos los stalls?
   - Impacto de la segmentación en programación de bajo nivel

---

## Fase 5: Subir a GitHub

### Paso 9: Crear repositorio en GitHub

1. Ve a https://github.com/new
2. **Nombre**: `colgii26-taller-5-estructura`
3. **Descripción**: "Taller 5: Optimización de Pipeline en MIPS - Homologación Colombia"
4. ☑ **Agregar README** (lo copiaremos)
5. ☑ **Agregar .gitignore** → Python (para evitar archivos basura)
6. **Create repository**

### Paso 10: Subir archivos locales

**En PowerShell (en Windows):**

```powershell
# Ir a la carpeta del proyecto
cd "C:\Users\Usuario\Downloads\colgii26-taller-5-estructura"

# Inicializar Git (si no está hecho)
git init

# Agregar el repositorio remoto
git remote add origin https://github.com/fierromarlonfernando-code/colgii26-taller-5-estructura.git

# Agregar todos los archivos
git add .

# Hacer commit
git commit -m "Initial commit: Taller 5 estructura MIPS - base y documentación"

# Subir a GitHub
git push -u origin main
```

**Si pide contraseña:**
- Usa un **Personal Access Token** en lugar de contraseña
- Obtén uno aquí: https://github.com/settings/tokens

---

## Fase 6: Entregas Finales

### Paso 11: Última revisión

Antes de entregar, verifica:

- [ ] `codigo/programa_base.asm` → Ejecuta sin errores
- [ ] `codigo/programa_optimizado.asm` → Ejecuta sin errores
- [ ] `Informe_COLGII26_T5.md` → Completo con capturas
- [ ] Carpeta `capturas/` → Tiene screenshots de MARS
- [ ] Carpeta `docs/` → Tiene toda la documentación
- [ ] Carpeta `transcripciones/` → Tiene las transcripciones
- [ ] `README.md` → Actualizado
- [ ] Todo commiteado a GitHub

### Paso 12: Enviar enlace de entrega

Tu repositorio debe estar en:
```
https://github.com/fierromarlonfernando-code/colgii26-taller-5-estructura
```

---

## 🆘 Solución de Problemas

### MARS no abre o hay error al cargar .jar

```bash
# Asegúrate de tener Java instalado
java -version

# Si no lo tienes, descargalo de:
# https://www.oracle.com/java/technologies/downloads/
```

### No veo las estadísticas en MARS

1. **Tools** → Tick en los checkbox:
   - ☑ MIPS X-Ray
   - ☑ Instruction Counter  
   - ☑ Instruction Statistics

2. Ejecuta el programa (**Run → Go**)

### Mi código optimizado no compila

1. Revisa la sintaxis de las instrucciones MIPS
2. Ver `docs/Referencia_Instrucciones_MIPS.pdf`
3. Asegúrate de usar registros válidos ($t0-$t9, $s0-$s7, etc.)

### ¿Cuánto debo optimizar?

**Mínimo esperado:**
- Identificar al menos 2 riesgos (hazards)
- Reordenar código para eliminar al menos 1 riesgo
- Mostrar mejora teórica en CPI

---

## 📚 Referencias Rápidas

- **Riesgos en Pipelines**: Ver `docs/Conceptos_Basicos_Laboratorio.pdf`
- **Instrucciones MIPS**: Ver `docs/Referencia_Instrucciones_MIPS.pdf`
- **Cómo usar MARS**: Ver `docs/Tutorial_MARS.pdf`

---

**¡Éxito en tu taller! 🚀**
