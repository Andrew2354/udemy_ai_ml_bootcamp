# 1. Verifica qué Python usa directamente el ambiente y su versión

C:\Users\andre\Documents\udemy_vscode\env\python.exe -c "import sys; print(sys.executable); print(sys.version)"

# 2. Verifica el prefijo de Python y su instalación base

C:\Users\andre\Documents\udemy_vscode\env\python.exe -c "import sys; print(sys.prefix); print(sys.base_prefix)"

# 3. Comprueba si el ambiente fue creado con venv

Get-Content C:\Users\andre\Documents\udemy_vscode\env\pyvenv.cfg

# 4. Verifica qué Python se ejecuta por defecto en la terminal

python -c "import sys; print(sys.executable)"

# 5. Comprueba si el directorio corresponde a un ambiente Conda

Test-Path C:\Users\andre\Documents\udemy_vscode\env\conda-meta

# 6. Muestra algunos archivos de metadatos de Conda

Get-ChildItem C:\Users\andre\Documents\udemy_vscode\env\conda-meta | Select-Object -First 5

# 7. Muestra los paquetes instalados en el ambiente

C:\Users\andre\Documents\udemy_vscode\env\python.exe -m pip list

# 8. Ejecuta Python específicamente dentro del ambiente Conda

conda run --prefix C:\Users\andre\Documents\udemy_vscode\env python -c "import sys; print(sys.executable)"

# 9. Exporta la configuración completa del ambiente Conda

conda env export --prefix C:\Users\andre\Documents\udemy_vscode\env

# 10. Verifica nuevamente que existe el ambiente Conda

Test-Path C:\Users\andre\Documents\udemy_vscode\env\conda-meta

# 11. Verifica Python y su versión usando conda run

conda run --prefix C:\Users\andre\Documents\udemy_vscode\env python -c "import sys; print(sys.executable); print(sys.version)"

# 12. Exporta el ambiente a environment.yml

conda env export --prefix C:\Users\andre\Documents\udemy_vscode\env > C:\Users\andre\Documents\udemy_vscode\environment.yml

# 13. Exporta el ambiente sin los números específicos de build, haciendo el YAML más portable

conda env export --prefix C:\Users\andre\Documents\udemy_vscode\env --no-builds > C:\Users\andre\Documents\udemy_vscode\environment.yml

# 14. Abre environment.yml para revisarlo

notepad C:\Users\andre\Documents\udemy_vscode\environment.yml

# 15. Entra al nuevo proyecto

cd .\Documents\udemy-heart-disease-project\

# 16. Crea un nuevo ambiente Conda usando el environment.yml

conda env create --prefix .\env -f ..\udemy_vscode\environment.yml

# 17. Comprueba qué Python está usando actualmente la terminal

python -c "import sys; print(sys.executable)"

# 18. Activa el nuevo ambiente Conda mediante su ruta

conda activate C:\Users\andre\Documents\udemy-heart-disease-project\env

# 19. Verifica nuevamente qué Python está usando

python -c "import sys; print(sys.executable)"
