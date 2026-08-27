# Reparación del Arranque USB para HyperHDR en CachyOS / Arch Linux

Una guía definitiva para solucionar el problema de detección del puerto serie de los LEDs Ambilight (`ttyUSB0` / `ttyACM0`) al arrancar el sistema en CachyOS y Arch Linux, incluyendo la instalación limpia y su integración estética en el escritorio KDE Plasma.

## El Problema Técnico

Por defecto, el puerto serie que utilizan los controladores de LEDs (Arduino/ESP) pertenece al grupo `uucp`. En sistemas modernos basados en Arch, ocurre una condición de carrera (*race condition*) durante el arranque: el puerto de hardware se inicializa antes de que los privilegios de grupo de tu usuario se hayan asignado por completo al entorno gráfico.

Esto provoca que HyperHDR no reconozca los LEDs al encender el PC, obligando a los usuarios a desconectar y volver a conectar el cable USB o a refrescar manualmente los grupos mediante comandos en la terminal tras cada reinicio.

## La Solución

En lugar de depender de asignaciones de grupo estáticas y rígidas que fallan en el arranque, esta solución aprovecha la etiqueta moderna `uaccess` de `systemd`. Esto transfiere dinámicamente los permisos de lectura y escritura de la interfaz USB al usuario que tenga el control de la sesión de escritorio activa en ese instante.


## Guía Paso a Paso

### Paso 1: Descarga e Instalación Limpia de HyperHDR

Para evitar instaladores conflictivos y mantener el sistema limpio, estructuraremos la aplicación directamente en tu carpeta de usuario:

1. Ve a la página oficial de [HyperHDR Releases en GitHub](https://github.com/awawa-dev/HyperHDR/releases) y descarga la última versión para Linux (el archivo empaquetado `.tar.gz`, por ejemplo, `HyperHDR-...-Linux-x86\_64.tar.gz`).

2. Abre tu gestor de archivos o terminal y extrae el contenido del paquete.

3. Mueve la carpeta extraída a tu directorio personal y renómbrala de forma limpia para que la ruta del ejecutable quede exactamente así:

```
/home/TU\_USUARIO/HyperHDR/bin/hyperhdr
```

### Paso 2: Crear el Acceso Directo Estético en KDE Plasma

Para integrarlo perfectamente en el sistema operativo y evitar que CachyOS te pregunte "qué hacer con este archivo ejecutable" cada vez que haces clic en él, crearemos un lanzador nativo:

1. Haz clic derecho en el menú de inicio de CachyOS (KDE Plasma) y selecciona **Editar aplicaciones...** (esto abrirá el *Editor del menú de KDE*).

2. Selecciona una categoría (por ejemplo, *Juegos* o *Herramientas*), haz clic en **Nuevo elemento** en la barra superior y asígnale el nombre `LedsHDR`.

3. En el campo **Programa**, introduce la ruta exacta a tu binario (puedes usar el botón de la carpeta de la derecha para buscarlo):

```
/home/TU\_USUARIO/HyperHDR/bin/hyperhdr
```

4. Haz clic en el botón **Guardar** en la esquina superior derecha del editor.

5. *¡Listo!* Ahora aparecerá con su icono en el buscador de aplicaciones de Plasma y se ejecutará al instante con un solo clic de forma directa.

### Paso 3: Desactivar Interferencias de Accesibilidad (Braille)

El servicio en segundo plano para pantallas Braille (`brltty`) intercepta de forma muy agresiva los chips controladores USB-a-Serie durante la inicialización del kernel. Debemos enmascarar y detener este servicio para evitar que secuestre el puerto de tus LEDs:

```
sudo systemctl stop brltty-udev.service  
sudo systemctl mask brltty-udev.service  
sudo systemctl stop brltty.service  
sudo systemctl disable brltty.service
```

### Paso 4: Añadir el Usuario al Grupo de Hardware

Asegura tu perfil de usuario dentro del grupo de comunicación estándar de dispositivos de comunicación:

```
sudo usermod -aG uucp $USER
```

### Paso 5: Desplegar la Regla de udev Definitiva con uaccess

Crea un conjunto de reglas personalizado que elimine las barreras de lectura/escritura (`0666`) y etiquete el nodo del dispositivo serie para su asignación inmediata al asiento gráfico activo:

```
sudo echo 'KERNEL=="ttyUSB\[0-9\]\*", SUBSYSTEM=="tty", TAG+="uaccess", MODE="0666"' | sudo tee /etc/udev/rules.d/99-leds.rules
```

*(Nota: Si en el futuro cambias de controlador y tu hardware se registra como un dispositivo ACM en lugar de USB, simplemente cambia `ttyUSB\[0-9\]\*` por `ttyACM\[0-9\]\*` en el comando anterior).*

### Paso 6: Recargar el Gestor de Dispositivos

Aplica las nuevas directivas directamente en el kernel en ejecución de forma inmediata:

```
sudo udevadm control --reload-rules && sudo udevadm trigger
```

### Paso 7: Reiniciar el Ordenador

Realiza un reinicio limpio del sistema para asegurar que todos los límites de usuario y asignaciones de asientos gráficos se asienten permanentemente:

```
sudo reboot
```

Al volver a encender el PC, ejecuta `LedsHDR` desde tu menú de aplicaciones. HyperHDR se enlazará a tu puerto `ttyUSB0` automáticamente sin necesidad de scripts secundarios, terminales ni desconexiones físicas de cables.

