# ¿Qué es un proceso JVM?

Un **proceso JVM** es un **proceso del sistema operativo que ejecuta una Java Virtual Machine (JVM).**

Formalmente:

- El SO (Linux / Windows) crea un proceso

- Ese proceso ejecuta el binario de Java

- Dentro de ese proceso vive una **JVM**

- La JVM ejecuta **bytecode Java**

En Spark:

- Driver = 1 **proceso JVM**

- Cada Executor = 1 proceso JVM

# ¿Qué es la JVM exactamente?

**Java Virtual Machine** es:

- Un entorno de ejecución

- Que corre bytecode (``.class``)

- Independiente del sistema operativo

# Arquitectónicamente, una JVM incluye:

## 1. Class Loader

- Carga clases en memoria

- Maneja classpaths

## 2. Runtime Data Areas

- Heap

- Stack

- Metaspace

- Native Memory

## 3. Execution Engine

- Intérprete

- JIT Compiler (Just-In-Time)

## 4. Garbage Collector

- Maneja memoria automáticamente

# ¿Por qué Spark usa JVM?

Spark está escrito principalmente en **Scala**, que compila a **bytecode JVM**.

Ventajas técnicas:

- Portabilidad

- Alto rendimiento (JIT)

- Ecosistema maduro

- Buen manejo de memoria off-heap (Tungsten)

# Proceso JVM vs Thread (muy importante)

## Proceso JVM

- Tiene su propio heap

- Memoria aislada

- Se comunica con otros procesos vía red / IPC

## Threads dentro de la JVM

- Comparten heap

- Usan múltiples cores

- En Spark:

    - Un executor JVM corre **múltiples tasks en paralelo usando threads**

Ejemplo:

``` scss

Executor JVM
 ├── Thread (task 1)
 ├── Thread (task 2)
 ├── Thread (task 3)

 ```

# JVM en Spark (detalles)

## Driver JVM

- Ejecuta:

    - SparkContext

    - DAG Scheduler

    - Task Scheduler

    - Corre el código del usuario (PySpark vía bridge)

## Executor JVM

- Ejecuta:

    - Tasks

    - Operadores físicos

## Maneja:

- Shuffle

- Cache

- Memoria

## ¿Y PySpark?

Aunque se escriba el código en Python:

- El Driver real sigue siendo una JVM

- Existe un Python process aparte

- Comunicación:

    - Python ↔ JVM vía Py4J

``` nginx

Python Driver
   ↕ Py4J
JVM Driver

```

Los executors:

- JVM para ejecución

- Python worker solo si la task usa código Python

# Resumen

- **Proceso JVM** = proceso del SO ejecutando una Java Virtual Machine

- En Spark:

    - Driver = 1 JVM
 
    -Executor = 1 JVM
- Cada JVM:

    - tiene heap propio

    - corre múltiples threads

    - PySpark no elimina la JVM