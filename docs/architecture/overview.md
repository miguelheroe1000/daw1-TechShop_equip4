# Arquitectura de l'aplicació

> Document: `projecte/docs/architecture/overview.md`

## 1. Visió general de l'arquitectura

Tota l'aplicació resideix en un **únic fitxer** (`projecte/Main.java`) i utilitza **classes declarades implícitament** (*Implicitly Declared Classes and Instance Main Methods*, JEP 512 de Java 25).

Això vol dir que no cal escriure cap declaració `class` ni el clàssic `public static void main(String[] args)`: el compilador genera la classe de manera implícita i tots els mètodes i variables del fitxer en formen part. D'aquesta manera, el projecte es manté simple i fàcil de seguir, adequat a un context didàctic.

## 2. Estructura interna de `Main.java` (taula dels 9 blocs del fitxer)

| Bloc | Contingut | Elements principals |
|:----:|-----------|---------------------|
| 1 | Capçalera i importacions | `Files`, `Path`, `Scanner`, `Random`, `static java.lang.IO.*` |
| 2 | Paleta de colors ANSI i *badges* | `RST`, `BLD`, `DIM`, `BCK`, `RED`, `GRN`, `YLW`, `BLU`, `PUR`, `CYN`, `WHT`, `BRED`, `BGRN`, `BYLW`; `BADGE_ACTIU`, `BADGE_BAIXA`, `BADGE_EN_ESTOC`, `BADGE_BAIX`, `BADGE_ESGOTAT` |
| 3 | Estructures de dades en memòria | Vectors paral·lels de capacitat `50` |
| 4 | Mètode principal i panell de control | `main`, `mostrarCapcaleraPrincipal`, `mostrarResumGeneral`, `mostrarMenuPrincipal` |
| 5 | Mètodes d'interfície | `titol`, `nota`, `ok`, `error`, `avis`, `pregunta`, `opcio`, `dada`, `capcalera`, `mostrarTargetes`, `mostrarTaula` |
| 6 | Mòdul de clients | `menuClients`, `llistarClients`, `gestionarClient`, `modificarClient`, `eliminarClient`, `donarAltaClient` |
| 7 | Mòdul de productes i estoc | `menuProductes`, `llistarProductes`, `gestionarProducte`, `modificarProducte`, `reposarEstoc`, `eliminarProducte`, `donarAltaProducte` |
| 8 | Mòdul de vendes | `menuVendes`, `mostrarEstadistiquesVendes`, `llistarVendes`, `cercarVendes`, `registrarVenda`, `imprimirTiquet` |
| 9 | Cerca per ID, alta als vectors, persistència i dades demo | `cercarIndex...`, `afegir...`, `guardarDadesFitxer`, `carregarDadesFitxer`, `inicialitzarDadesBase` |

## 3. Decisions de disseny clau

- **Aïllament dels colors ANSI:** els codis `\u001B[...]` només s'utilitzen dins dels mètodes d'interfície (i `imprimirTiquet` / `llistarProductes` per a la barra). Això evita desquadraments en la resta del codi.
- **Càlcul d'amplada visible en `mostrarTaula`:** s'usa `.replaceAll("\u001B\\[[;\\d]*m", "").length()` per descomptar els codis invisibles de les etiquetes abans d'omplir amb espais.
- **Lectura d'entrada:** `pregunta(text)` i `opcio(text)` només imprimeixen el missatge; la lectura es fa localment amb `scanner.nextLine().trim()`.

## 4. Tecnologies utilitzades i restriccions didàctiques

**Tecnologies:**

- Java 25 LTS
- `java.lang.IO`
- `java.util.Scanner`
- `java.nio.file.Files` i `Path`
- `java.lang.Math` i `java.util.Random`
- ANSI + Unicode
- Git + GitHub
- Markdown + Mermaid

**Taula dels elements del llenguatge permesos i prohibits:**

| Element | Estat | Observació |
|---------|:-----:|------------|
| Mètodes i variables en classe implícita (JEP 512) | Permès | Tot el codi viu en `Main.java` |
| Vectors (arrays) | Permès | Estructures paral·leles de capacitat `50` |
| `Scanner`, `Files`, `Path`, `Math`, `Random` | Permès | Entrada, persistència, càlculs i dades aleatòries |
| `class` | Prohibit | Es fa servir la classe declarada implícitament |
| `ArrayList` | Prohibit | Es substitueix per vectors |
| `HashMap` | Prohibit | Es substitueix per cerca per ID sobre vectors |
| *Streams* | Prohibit | Es treballa amb bucles tradicionals |
| *Lambdes* | Prohibit | Es treballa amb mètodes ordinaris |
