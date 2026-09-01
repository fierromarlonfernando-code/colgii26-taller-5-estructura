# 📤 Instrucciones para Subir a GitHub

Tu repositorio local está listo. Ahora sigue estos pasos para subirlo a GitHub.

---

## 🔑 Paso 1: Crear Personal Access Token (Recomendado)

GitHub requiere autenticación. Lo más seguro es usar un **Personal Access Token** en lugar de contraseña.

1. Ve a: https://github.com/settings/tokens
2. Click en **"Generate new token"** → **"Generate new token (classic)"**
3. Dale un nombre: `COLGII-Token`
4. Selecciona permisos:
   - ☑ `repo` (acceso completo a repositorios)
   - ☑ `gist`
5. Click **"Generate token"**
6. **COPIA el token** (no podrás verlo de nuevo)
7. **Guarda en un lugar seguro** (puede ser un archivo de notas)

---

## 📌 Paso 2: Crear Repositorio en GitHub

1. Ve a: https://github.com/new
2. Rellena:
   - **Repository name**: `colgii26-taller-5-estructura`
   - **Description**: `Taller 5: Optimización de Pipeline en MIPS - Homologación Colombia`
   - **Visibility**: Elige **Public** (para que vea el profesor) o **Private** (solo tú)
3. **DO NOT** seleccionar "Add a README" (ya lo tenemos)
4. **DO NOT** seleccionar ".gitignore" (ya lo tenemos)
5. Click **"Create repository"**

---

## ⬆️ Paso 3: Conectar Repositorio Local con GitHub

Abre **PowerShell** en la carpeta del proyecto:

```powershell
cd "C:\Users\Usuario\Downloads\colgii26-taller-5-estructura"
```

Luego ejecuta estos comandos:

### 3a. Agregar URL del repositorio remoto

```powershell
git remote add origin https://github.com/fierromarlonfernando-code/colgii26-taller-5-estructura.git
```

### 3b. Renombrar rama a `main` (si es necesario)

```powershell
git branch -M main
```

### 3c. Verificar que todo está bien

```powershell
git remote -v
```

Deberías ver algo como:
```
origin  https://github.com/fierromarlonfernando-code/colgii26-taller-5-estructura.git (fetch)
origin  https://github.com/fierromarlonfernando-code/colgii26-taller-5-estructura.git (push)
```

---

## 🔐 Paso 4: Subir a GitHub (Primera Vez)

```powershell
git push -u origin main
```

**Si pide autenticación:**
- **Usuario**: `fierromarlonfernando-code`
- **Contraseña**: Usa el **Personal Access Token** que copiaste en el Paso 1

---

## ✅ Paso 5: Verificar que se subió correctamente

1. Ve a: https://github.com/fierromarlonfernando-code/colgii26-taller-5-estructura
2. Deberías ver todos los archivos:
   - ✅ `README.md`
   - ✅ `INSTRUCCIONES.md`
   - ✅ `codigo/programa_base.asm`
   - ✅ `docs/` (con PDFs)
   - ✅ `transcripciones/` (con VTT)
   - ✅ Etc.

---

## 🔄 Paso 6: Subidas Posteriores

Cada vez que hagas cambios locales (agregar código optimizado, informe, capturas):

```powershell
# Agregar cambios
git add .

# Hacer commit
git commit -m "Tu mensaje descriptivo aquí"

# Subir a GitHub
git push origin main
```

**Ejemplos de mensajes de commit:**

```powershell
# Cuando agregues el código optimizado
git commit -m "Add: programa_optimizado.asm con reordenamiento de instrucciones"

# Cuando agregues el informe
git commit -m "Add: Informe completo con análisis de hazards y capturas MARS"

# Cuando agregues capturas
git commit -m "Add: Capturas de pantalla de MARS para código base y optimizado"
```

---

## 🔗 Tu Enlace Final

Una vez subido, tu repositorio estará en:

```
https://github.com/fierromarlonfernando-code/colgii26-taller-5-estructura
```

**Este es el enlace que debes enviar a tu profesor.**

---

## 🆘 Solución de Problemas

### Error: "fatal: Not a git repository"

```powershell
# Asegúrate de estar en la carpeta correcta
cd "C:\Users\Usuario\Downloads\colgii26-taller-5-estructura"

# Verifica que existe .git
ls -la | findstr ".git"
```

### Error: "fatal: remote origin already exists"

```powershell
# Si ya habías agregado origin, remuévelo primero
git remote remove origin

# Luego agrega de nuevo
git remote add origin https://github.com/fierromarlonfernando-code/colgii26-taller-5-estructura.git
```

### Error: "Authentication failed"

- Usa el **Personal Access Token**, no tu contraseña
- Si olvidas el token, crea uno nuevo en https://github.com/settings/tokens

### Error: "The remote repository does not exist"

- Verifica que creaste el repositorio en GitHub correctamente
- Asegúrate de usar el nombre exacto: `colgii26-taller-5-estructura`

---

## 💡 Tips Útiles

### Ver estado de los cambios

```powershell
git status
```

### Ver historial de commits

```powershell
git log --oneline
```

### Ver cambios no commiteados

```powershell
git diff
```

### Deshacer cambios (si algo sale mal)

```powershell
# Descartar cambios en un archivo
git checkout -- archivo.asm

# Deshacer último commit (pero mantener cambios)
git reset --soft HEAD~1
```

---

## 📋 Checklist Final

- [ ] Personal Access Token creado y guardado
- [ ] Repositorio creado en GitHub (privado o público)
- [ ] `git remote add origin` ejecutado
- [ ] `git push -u origin main` ejecutado sin errores
- [ ] Repositorio visible en GitHub.com
- [ ] Todos los archivos presentes en GitHub
- [ ] Enlace funcionando: https://github.com/fierromarlonfernando-code/colgii26-taller-5-estructura

---

**¡Listo! Tu taller está en GitHub. Ahora solo completa el código optimizado y las capturas de MARS.** 🚀
