# Instal·lació i execució

> Document: `projecte/docs/installation.md`

## 1. Requisits del sistema

- **JDK 25 o superior** (imprescindible perquè les classes implícites i `java.lang.IO` són estàndard sense `--enable-preview`).
- Terminal amb codificació **UTF-8** i suport ANSI, d'almenys **95 columnes** d'amplària.

## 2. Comprovació i instal·lació de Java 25

Per comprovar la versió de Java instal·lada, executa l'ordre:

```bash
java --version
```

Si no tens Java 25, pots instal·lar-lo segons el teu sistema operatiu:

- **SDKMAN!** (Linux/macOS): consulta les versions disponibles amb `sdk list java` i instal·la la de Java 25 de Temurin amb `sdk install java <VERSIÓ_25>-tem`.
- **winget** (Windows):

  ```powershell
  winget install EclipseAdoptium.Temurin.25.JDK
  ```

- **Homebrew** (macOS):

  ```bash
  brew install --cask temurin@25
  ```

## 3. Descàrrega i execució (sempre des de l'arrel `intermodular/`)

```bash
git clone <URL_DEL_REPOSITORI>
cd intermodular
java projecte/Main.java
```

> [!IMPORTANT]
> Cal executar l'aplicació des de `intermodular/` i no des de dins de `projecte/`, perquè la ruta relativa `"projecte/dades_botiga.txt"` funçione.
