@echo off
 
set SYSML_VERSION="0.38.0"
 
echo --- Step 1: Testing Conda installation ---
where conda
if errorlevel 1 (
    echo Conda is not installed. Please install Conda and re-run.
    goto :error
)
call conda --version || goto :error
 
echo --- Step 2: Testing Java installation ---
where java
if errorlevel 1 (
    echo Java is not installed. Please install Java and re-run.
    goto :error
)
call java -version || goto :error
 
echo --- Step 3: Installing Jupyter SysML kernel and dependencies ---
call jupyter kernelspec remove sysml -f >nul 2>&1
call conda install "jupyter-sysml-kernel=0.35.0" python=3.* jupyterlab=2.* graphviz=2.* nodejs=14.* -c conda-forge -y || goto:error
 
echo --- Step 4: Installing JupyterLab SysML extension ---
call jupyter labextension uninstall @systems-modeling/jupyterlab-sysml
call jupyter labextension install "@systems-modeling/jupyterlab-sysml@%SYSML_VERSION%" || goto:error
 
 
echo --- Step 5: Customizing JupyterLab ---
call python "%~dp0\install.py" || goto:error
 
echo --- Step 6: Running Jupyter environment ---
echo To launch JupyterLab you can now run ^"jupyter lab^" from Command Prompt.
echo Re-running this script is not necessary and will re-install the environment.
echo(
echo(
cmd /k
goto :EOF
 
:error
echo Failed with error #%errorlevel%.
pause
exit /b %errorlevel%