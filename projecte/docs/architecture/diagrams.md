# Diagramas de Arquitectura y Modelo de Datos

## 1. Arquitectura de Main.java

```mermaid
flowchart TB
    subgraph UI["1. INTERFAZ DE USUARIO (UI)"]
        A["Colores, mensajes, tablas y tarjetas"]
    end

    subgraph APP["2. NAVEGACIÓN Y NEGOCIO (APP)"]
        B["Menú principal"]
        C["Módulo Clientes"]
        D["Módulo Productos"]
        E["Módulo Ventas"]
        B --> C
        B --> D
        B --> E
    end

    subgraph DATA["3. DATOS EN MEMORIA (DATA)"]
        F["Vectores de Clientes"]
        G["Vectores de Productos"]
        H["Vectores de Ventas"]
    end

    subgraph IO["4. PERSISTENCIA (IO)"]
        I["Cargar y guardar datos"]
        J[("dades_botiga.txt")]
        I <--> J
    end

    APP --> UI
    APP --> DATA
    DATA --> IO
```

## 2. Modelo Entidad-Relación

```mermaid
erDiagram
    CLIENT {
        int clientIds PK
        String clientNoms
        String clientEmails
        String clientTelefons
        boolean clientActius
    }
    PRODUCTE {
        int producteIds PK
        String producteNoms
        String producteCategories
        double productePreus
        int producteEstoc
    }
    VENDA {
        int vendaIds PK
        int vendaClientIds FK
        int vendaProducteIds FK
        int vendaQuantitats
        double vendaTotals
        String vendaDates
    }
```

## 3. Descripción

- UI: interfaz de terminal y presentación de información.
- APP: navegación y módulos de clientes, productos y ventas.
- DATA: almacenamiento mediante vectores paralelos.
- IO: inicialización y persistencia en archivos.

Los módulos y la persistencia están pendientes de implementación en las etapas posteriores.
