# HyperHDR Configuration & Boot Fix for CachyOS / Arch Linux

[![Language: English](https://img.shields.io/badge/Language-English-blue.svg)](#english)
[![Idioma: Español](https://img.shields.io/badge/Idioma-Español-red.svg)](#español)

Welcome to this public repository dedicated to optimizing and automating **HyperHDR** on **CachyOS** (or any Arch Linux-based distribution). 

If you are facing serial port detection issues on startup, or want to integrate the application seamlessly into your desktop environment, you will find complete, step-by-step guides here.

---

<a name="english"></a>
## 🇬🇧 English: Repository Overview

This repository contains standalone, production-ready documentation to set up and fix HyperHDR Ambilight systems.

### 🌟 Key Features Covered
* **Wayland & PipeWire Screen Capture:** Fully compatible with modern Wayland desktop sessions. HyperHDR captures your screen seamlessly via native PipeWire portals without performance overhead.
* **Automatic USB Boot Fix:** Solves the common race condition where `ttyUSB0` or `ttyACM0` requires manual replugging or terminal group updates after every reboot.
* **KDE Plasma Launcher Integration:** Adds HyperHDR to your desktop applications menu, bypassing the interactive execution prompts.

### 📂 Repository Structure
* `en/README.md` — Detailed step-by-step technical guide in English.
* `es/GUIA.md` — Guía técnica detallada paso a paso en Español.
* `images/` — Screenshots and visual configuration examples.

👉 **[Go to the English Setup Manual](./en/README.md)**

---

<a name="español"></a>
## 🇪🇸 Español: Descripción del Repositorio

Este repositorio contiene documentación independiente lista para implementar y solucionar problemas de sistemas Ambilight con HyperHDR.

### 🌟 Características Clave Cubiertas
* **Captura de pantalla en Wayland y PipeWire:** Totalmente compatible con las sesiones de escritorio modernas de Wayland. HyperHDR captura la pantalla sin problemas a través de los portales nativos de PipeWire sin impacto en el rendimiento.
* **Solución Automática al Arranque USB:** Resuelve la condición de carrera (*race condition*) común donde el puerto `ttyUSB0` o `ttyACM0` requiere desconectarse manualmente o actualizar los grupos en la terminal tras cada reinicio.
* **Integración en el Lanzador de KDE Plasma:** Añade HyperHDR al menú de aplicaciones de tu escritorio, evitando el cuadro de diálogo flotante que pregunta cómo ejecutar el binario.

### 📂 Estructura del Repositorio
* `en/README.md` — Detailed step-by-step technical guide in English.
* `es/GUIA.md` — Guía técnica detallada paso a paso en Español.
* `images/` — Capturas de pantalla y ejemplos visuales de configuración.

👉 **[Ir al Manual de Configuración en Español](./es/GUIA.md)**

---
*Maintained by the community. Feel free to open an Issue or a Pull Request if you want to add support for other environments!*
