@echo off
rem ===========================================================================
rem AstroTune 1.5beta - build helper
rem DCS 2026(c) for LOCK-ON GREECE by =GR= Astr0
rem
rem Put this next to AstroTune_1_5beta.py and double-click it.
rem It finds Python, installs PyInstaller if needed, lays out the folders the
rem Inno script expects, and builds build\AstroTune.exe.
rem
rem Messages are in English on purpose: the Windows console mangles Greek
rem depending on the active code page, and a build script must stay readable.
rem ===========================================================================
setlocal
cd /d "%~dp0"
title AstroTune 1.5beta - build

echo.
echo ============================================================
echo AstroTune 1.5beta - build
echo ============================================================
echo folder: %CD%
echo.

rem ---------------------------------------------------------------- Python --
set "PY="
py -3 --version >nul 2>&1
if not errorlevel 1 set "PY=py -3"

if not defined PY (
python --version >nul 2>&1
if not errorlevel 1 set "PY=python"
)

if not defined PY (
echo [X] Python was not found.
echo Install Python 3.10 or newer, 64-bit, from python.org
echo and tick "Add python.exe to PATH" during setup.
goto :fail
)

for /f "delims=" %%v in ('%PY% --version 2^>^&1') do echo [ok] %%v

rem ------------------------------------------------------------ the script --
set "SRC="
if exist "AstroTune_1_5beta.py" set "SRC=AstroTune_1_5beta.py"
if not defined SRC (
for %%f in ("AstroTune*1*5*beta*.py") do set "SRC=%%~nxf"
)
if not defined SRC (
echo [X] No AstroTune 1.5beta .py file found in this folder.
echo Copy AstroTune_1_5beta.py next to this .bat and run it again.
goto :fail
)
echo [ok] source: %SRC%

rem ------------------------------------------------------- PyInstaller --
%PY% -m PyInstaller --version >nul 2>&1
if errorlevel 1 (
echo [..] PyInstaller is missing - installing it now...
%PY% -m pip install --upgrade pyinstaller
if errorlevel 1 (
echo [X] pip could not install PyInstaller.
goto :fail
)
)
for /f "delims=" %%v in ('%PY% -m PyInstaller --version 2^>^&1') do echo [ok] PyInstaller %%v

rem ----------------------------------------------------------- folders --
if not exist "docs" mkdir "docs"
if not exist "dist" mkdir "dist"

rem The manual belongs in docs\ - move it there if it is sitting loose.
if exist "AstroTune_Manual.html" (
if not exist "docs\AstroTune_Manual.html" (
move /y "AstroTune_Manual.html" "docs\" >nul
echo [ok] moved AstroTune_Manual.html into docs\
)
)

rem Icon - checked in the folder first, then in assets\. Without it the exe gets
rem the generic PyInstaller icon, and only the shortcuts look right.
set "ICO="
if exist "astrotune.ico" set "ICO=--icon astrotune.ico"
if not defined ICO if exist "assets\astrotune.ico" set "ICO=--icon assets\astrotune.ico"
if defined ICO (
echo [ok] icon found
) else (
echo [!!] astrotune.ico not found - the exe will use the default icon
)

rem ------------------------------------------------------------- build --
echo.
echo [..] Building build\AstroTune.exe - this takes a minute...
echo.
%PY% -m PyInstaller --noconfirm --onefile --noconsole --name AstroTune ^
--distpath build --workpath "build\tmp" --specpath build %ICO% ^
"%SRC%"
if errorlevel 1 (
echo [X] PyInstaller reported an error.
goto :fail
)

if not exist "build\AstroTune.exe" (
echo [X] PyInstaller finished but build\AstroTune.exe is not there.
goto :fail
)

rem ------------------------------------------------- what Inno needs --
echo.
echo ============================================================
echo Done. What the Inno script needs:
echo ============================================================
call :check "build\AstroTune.exe"
call :check "docs\AstroTune_Manual.html"
call :check "README.md"
echo.
echo Next: open AstroTune_Setup.iss with Inno Setup and press Build.
echo The finished installer lands in dist\
echo.
echo Tip: run build\AstroTune.exe once before making the installer.
echo Press "Detect this PC" and check it finds your card and options.lua.
echo.
pause
exit /b 0

:check
if exist %1 (
echo [ok] %~1
) else (
echo [!!] MISSING: %~1
)
goto :eof

:fail
echo.
echo ************************************************************
echo Build failed. Nothing was installed or changed.
echo ************************************************************
echo.
pause
exit /b 1
