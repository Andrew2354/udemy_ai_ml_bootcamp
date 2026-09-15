# Crear y recrear un ambiente Conda por prefijo

Esta guía documenta cómo identificar un ambiente Python existente, exportarlo a `environment.yml` y utilizarlo para crear un nuevo ambiente Conda en otro proyecto.

---

## 1. Identificar qué tipo de ambiente tengo

Supongamos que el ambiente existente está en:

```powershell
C:\Users\andre\Documents\udemy_vscode\env
```

### Ver qué Python está utilizando

```powershell
C:\Users\andre\Documents\udemy_vscode\env\python.exe -c "import sys; print(sys.executable); print(sys.version)"
```

Ejemplo:

```text
C:\Users\andre\Documents\udemy_vscode\env\python.exe
3.13.7 | packaged by Anaconda, Inc. | ...
```

Esto confirma que el ejecutable Python pertenece a ese directorio.

### Comprobar si es un `venv`

Los ambientes creados con `venv` normalmente tienen un archivo:

```text
pyvenv.cfg
```

Comprobar:

```powershell
Test-Path C:\Users\andre\Documents\udemy_vscode\env\pyvenv.cfg
```

Si devuelve:

```text
False
```

no significa por sí solo que sea Conda, pero indica que no es un `venv` convencional.

También se puede comprobar directamente:

```powershell
Get-Content C:\Users\andre\Documents\udemy_vscode\env\pyvenv.cfg
```

Si el archivo no existe, PowerShell mostrará un error.

### Comprobar si es un ambiente Conda

La señal más importante es la carpeta:

```text
conda-meta
```

Comprobar:

```powershell
Test-Path C:\Users\andre\Documents\udemy_vscode\env\conda-meta
```

Si devuelve:

```text
True
```

es una fuerte confirmación de que el directorio es un ambiente Conda.

También se puede inspeccionar:

```powershell
Get-ChildItem C:\Users\andre\Documents\udemy_vscode\env\conda-meta | Select-Object -First 5
```

Si aparecen archivos como:

```text
numpy-....json
pandas-....json
python-....json
```

el ambiente está administrado por Conda.

### Comprobar el prefijo de Python

```powershell
C:\Users\andre\Documents\udemy_vscode\env\python.exe -c "import sys; print(sys.prefix); print(sys.base_prefix)"
```

En este caso ambos apuntaban a:

```text
C:\Users\andre\Documents\udemy_vscode\env
```

Esto es consistente con el uso de un ambiente Conda creado directamente en ese directorio.

---

# 2. Diferencia entre Conda, venv y Pipenv

Una forma rápida de identificar el ambiente:

| Tipo     | Indicador típico                                                         |
| -------- | ------------------------------------------------------------------------ |
| `venv`   | `pyvenv.cfg`                                                             |
| Conda    | `conda-meta\`                                                            |
| Pipenv   | `Pipfile` + ambiente administrado por Pipenv                             |
| Anaconda | Distribución/gestor Conda; los ambientes también contienen `conda-meta\` |

Importante:

> `Anaconda` es la distribución que tienes instalada. El ambiente específico que utilizamos aquí es un **ambiente Conda creado por prefijo**.

No se debe confundir:

```text
C:\Users\andre\anaconda3
```

con:

```text
C:\Users\andre\Documents\udemy_vscode\env
```

El primero es la instalación/base de Anaconda.

El segundo es el ambiente Conda del proyecto.

---

# 3. Verificar los paquetes instalados

Podemos comprobar qué paquetes tiene el ambiente:

```powershell
C:\Users\andre\Documents\udemy_vscode\env\python.exe -m pip list
```

También podemos consultar los paquetes administrados por Conda:

```powershell
conda list --prefix C:\Users\andre\Documents\udemy_vscode\env
```

---

# 4. Exportar el ambiente existente

Una vez confirmado que el ambiente es Conda, exportarlo a un archivo `environment.yml`.

```powershell
conda env export --prefix C:\Users\andre\Documents\udemy_vscode\env > C:\Users\andre\Documents\udemy_vscode\environment.yml
```

Esto genera un archivo parecido a:

```text
name: C:\Users\andre\Documents\udemy_vscode\env

channels:
  - defaults

dependencies:
  - python=3.13.7
  - numpy=2.3.3
  - pandas=2.3.2
  - scikit-learn=1.7.2
  ...

prefix: C:\Users\andre\Documents\udemy_vscode\env
```

---

# 5. Preferir `--no-builds` para compartir el environment

Para recrear el ambiente en otro directorio o proyecto, es recomendable exportarlo sin los números de build específicos:

```powershell
conda env export --prefix C:\Users\andre\Documents\udemy_vscode\env --no-builds > C:\Users\andre\Documents\udemy_vscode\environment.yml
```

Esto genera un archivo más portable.

Por ejemplo, en lugar de:

```yaml
numpy=2.3.3=py313h050da96_0
```

tendremos:

```yaml
numpy=2.3.3
```

El archivo puede abrirse para revisarlo:

```powershell
notepad C:\Users\andre\Documents\udemy_vscode\environment.yml
```

---

# 6. Crear el nuevo proyecto

Por ejemplo:

```powershell
cd C:\Users\andre\Documents
```

y entrar al nuevo proyecto:

```powershell
cd .\udemy-heart-disease-project\
```

La estructura quedará:

```text
udemy-heart-disease-project/
│
├── env/
│
└── ...
```

---

# 7. Crear el nuevo ambiente Conda

Usando el `environment.yml` del proyecto anterior:

```powershell
conda env create --prefix .\env -f ..\udemy_vscode\environment.yml
```

### ¿Qué significa?

```text
conda env create
```

Crea un nuevo ambiente.

```text
--prefix .\env
```

Indica exactamente dónde crear el ambiente.

En este caso:

```text
C:\Users\andre\Documents\udemy-heart-disease-project\env
```

```text
-f ..\udemy_vscode\environment.yml
```

Indica qué archivo usar para definir los paquetes.

---

# 8. Verificar que el ambiente fue creado

Comprobar que existe `conda-meta`:

```powershell
Test-Path .\env\conda-meta
```

Debe devolver:

```text
True
```

También se puede comprobar directamente el Python:

```powershell
.\env\python.exe -c "import sys; print(sys.executable); print(sys.version)"
```

Debe mostrar algo parecido a:

```text
C:\Users\andre\Documents\udemy-heart-disease-project\env\python.exe
3.13.7 | packaged by Anaconda, Inc. | ...
```

Esta es una de las verificaciones más importantes.

---

# 9. Activar el ambiente

Un ambiente creado con `--prefix` se activa utilizando su ruta:

```powershell
conda activate C:\Users\andre\Documents\udemy-heart-disease-project\env
```

Después verificar:

```powershell
python -c "import sys; print(sys.executable)"
```

Debería aparecer:

```text
C:\Users\andre\Documents\udemy-heart-disease-project\env\python.exe
```

## Si sigue apareciendo Anaconda base

Si después de:

```powershell
conda activate C:\Users\andre\Documents\udemy-heart-disease-project\env
```

esto:

```powershell
python -c "import sys; print(sys.executable)"
```

sigue mostrando:

```text
C:\Users\andre\anaconda3\python.exe
```

**no asumir que el ambiente está activo correctamente.**

Comprobar primero:

```powershell
conda info --envs
```

y:

```powershell
where.exe python
```

También comprobar:

```powershell
$env:CONDA_PREFIX
```

Debería mostrar:

```text
C:\Users\andre\Documents\udemy-heart-disease-project\env
```

---

# 10. Verificación recomendada

Antes de comenzar a trabajar, ejecutar:

```powershell
python -c "import sys; print('Python:', sys.executable); print('Version:', sys.version)"
```

y:

```powershell
conda info --envs
```

y:

```powershell
$env:CONDA_PREFIX
```

Finalmente:

```powershell
python -m pip list
```

Con esto podemos confirmar:

1. Qué Python estamos utilizando.
2. Qué versión de Python tenemos.
3. Qué ambiente Conda está activo.
4. Qué paquetes están instalados.

---

# 11. Configurar VS Code

Después de crear el ambiente, VS Code debe utilizar:

```text
C:\Users\andre\Documents\udemy-heart-disease-project\env\python.exe
```

En VS Code:

```text
Ctrl + Shift + P
```

Buscar:

```text
Python: Select Interpreter
```

y seleccionar el Python dentro de:

```text
udemy-heart-disease-project\env
```

También se puede verificar desde una terminal integrada:

```powershell
python -c "import sys; print(sys.executable)"
```

Si VS Code está correctamente configurado, debe devolver:

```text
C:\Users\andre\Documents\udemy-heart-disease-project\env\python.exe
```

---

# 12. Comandos principales para repetir el proceso

## Identificar el ambiente existente

```powershell
C:\Users\andre\Documents\udemy_vscode\env\python.exe -c "import sys; print(sys.executable); print(sys.version)"
```

```powershell
Test-Path C:\Users\andre\Documents\udemy_vscode\env\pyvenv.cfg
```

```powershell
Test-Path C:\Users\andre\Documents\udemy_vscode\env\conda-meta
```

## Exportar

```powershell
conda env export --prefix C:\Users\andre\Documents\udemy_vscode\env --no-builds > C:\Users\andre\Documents\udemy_vscode\environment.yml
```

## Crear el nuevo ambiente

Desde el nuevo proyecto:

```powershell
conda env create --prefix .\env -f ..\udemy_vscode\environment.yml
```

## Activar

```powershell
conda activate C:\Users\andre\Documents\udemy-heart-disease-project\env
```

## Verificar

```powershell
python -c "import sys; print(sys.executable); print(sys.version)"
```

```powershell
$env:CONDA_PREFIX
```

```powershell
where.exe python
```

---

# 13. Flujo recomendado para futuros proyectos

La próxima vez que quiera reutilizar un ambiente Conda:

```text
1. Identificar el ambiente original
        ↓
2. Confirmar que contiene conda-meta
        ↓
3. Exportar environment.yml
        ↓
4. Crear el nuevo proyecto
        ↓
5. Ejecutar conda env create --prefix .\env
        ↓
6. Activar el ambiente
        ↓
7. Verificar sys.executable
        ↓
8. Seleccionar el intérprete en VS Code
```

Los tres comandos de diagnóstico más importantes son:

```powershell
Test-Path .\env\conda-meta
```

```powershell
$env:CONDA_PREFIX
```

```powershell
python -c "import sys; print(sys.executable)"
```

Si el último devuelve:

```text
...\mi-proyecto\env\python.exe
```

estamos utilizando el Python correcto del proyecto.
