# Proyecto: Retrofit macOS Tahoe (Estética OS X Mavericks)

Proporcionar una documentación quirúrgica y un plan de acción a largo plazo para romper las barreras de diseño cerradas de macOS Tahoe y transformarlo visualmente para emular la estética de **OS X Mavericks (Skeuomorfismo, bordes duros, texturas Aqua)** o, como plan B operativo, establecer un sistema minimalista con un tono industrial/retro y un flujo de trabajo ultra-cómodo.

Dado que esto no se completará en un día, este documento actuará como la Biblia técnica de referencia para el proyecto.

---

## Filosofía de Trabajo "Sandbox Real"
Dado que jugar con la raíz del sistema (Signed System Volume) en macOS moderno puede causar un desplome total (Kernel Panics, bootloops), **JAMÁS** trabajaremos en el disco de tu uso diario. Utilizaremos el hardware real del Hackintosh, pero segregando los volúmenes a nivel lógico (APFS).

---

## 🛠️ Inventario de Herramientas (El Toolchain)

Para vencer el código cerrado de Apple, inyectaremos herramientas que operan a nivel de scripting de sistema o de binarios en memoria:

| Herramienta | Propósito en el Proyecto Retro |
| :--- | :--- |
| **OpenCore (config.plist)** | Control absoluto antes del boot. Lo usaremos para desactivar la Integridad del Sistema (SIP). |
| **csrutil / Terminal** | Herramientas nativas para romper el "SSV" (Signed System Volume) y permitir que macOS nos deje reescribir los `.icns` core del sistema en modo lectura/escritura. |
| **SketchyBar** | Reemplazo total de la barra de menú. Permite dibujar una barra opaca grisácea retro en lugar de las píldoras flotantes modernas, además de admitir fuentes clásicas y customizar la opacidad individual. |
| **Yabai / Scripting Additions** | Creado para Tiling, lo usaremos por su inyección: permite *apagar las sombras modernas (Window Shadows)* y *eliminar/manipular esquinas redondeadas* de las aplicaciones. |
| **cDock (o Spacebar)** | Software residente para intentar devolver el "estante 3D" del dock de Mavericks. Si las APIs de Tahoe lo bloquean, usaremos widgets o forks compatibles para generar la ilusión óptica del dock reflectante. |
| **Mousecape** | Motor que sobre-escribe en memoria los cursores de sistema. Lo usaremos para inyectar un pack de punteros Mac OS X clásicos. |
| **IconChamp / Pictogram** | Gestores de temas. Útiles para forzar el cambio de íconos en apps de la Mac App Store que son resistentes al cambio tradicional (Cmd+i). |

---

## 🗺️ Roadmap de Ejecución (Fases Clínicas)

### Hito 1: La Cirugía de Aislamiento APFS (El "Tahoe Lab")
*Objetivo: Tener un entorno donde romper cosas sea gratis y seguro.*

1. **Creación del Volumen:** Entrar en la Utilidad de Discos de tu instalación actual. Seleccionar el contenedor principal APFS y hacer clic en `+ Volumen`. Llamarlo **"Tahoe Lab"**.
2. **Instalación:** Descargar el instalador de macOS Tahoe de nuevo. Ejecutarlo y elegir instalarlo específicamente en "Tahoe Lab".
3. **Mapeo UEFI:** Al reiniciar, el bootloader OpenCore detectará automáticamente ambos volúmenes. Tu día a día (Tahoe Main) y tu laboratorio (Tahoe Lab) compartirán los mismos *kexts* y el mismo soporte de CPU/Gráfica, por lo que el laboratorio tendrá máxima fluidez de hardware.

### Hito 2: Caída de los Escudos de Seguridad
*Objetivo: Debemos operar en el Laboratorio (Tahoe Lab), así que estas reglas aplican para esa partición al arrancar su perfil en OpenCore.*

1. **Desactivar SIP:** En OpenCore, ajustar la variable `csr-active-config` a un valor permisivo (`FF0F0000`). Esto habilita *Yabai* y la inyección sin firmas.
2. **Desactivar SSV:** Al iniciar en *Tahoe Lab*, abrir la Terminal y ejecutar:
   ```bash
   sudo csrutil authenticated-root disable
   ```
3. Reiniciar el sistema. El volumen de raíz ya no exigirá la firma criptográfica pesada de Apple, lo que abre la puerta a la Fase 3.

### Hito 3: La Cirugía Visual (Iconos y Fuente)
*Objetivo: Alterar los cimientos visuales. Reemplazo del "San Francisco" / "Big Sur flat design".*

1. **El Dump de Mavericks:** Descargar el `CoreTypes.bundle` original del instalador de Mavericks y aislar los `.icns`.
2. **Re-montando el Núcleo:**
   - Iniciar en *modo recuperación* si es necesario o montar el volumen en vivo vía Terminal usando LiveFS (`mount -uw /`).
   - Navegar a `/System/Library/CoreServices/CoreTypes.bundle/Contents/Resources/`.
   - Reemplazar despiadadamente los `.icns` aburridos (Papelera, Finder, carpetas de sistema, iconos genéricos de disco) por los Aqua pesados en 3D.
   - Ejecutar el comando de snapshot `bless` o dejar que se cree un nuevo snapshot escribible para consolidar los cambios permanentes.
3. **Fuentes del Sistema:** Identificar perfiles CSS/Mac de sobreescritura (scripts comunitarios) que remapean "San Francisco" a **"Lucida Grande"** a nivel de motor de texto de WebKit / CoreText.

### Hito 4: Modificación Interfaz Funcional (UI y Ventanas)
*Objetivo: Controlar cómo reacciona el sistema operativo a los clicks y ventanas.*

1. **MenuBar Retro:** Instalar `Sketchybar`. Apagar el menú nativo de Tahoe en Configuración. Escribir configuraciones locales de sketchybar (`~/.config/sketchybar/`) importando assets visuales descargados (batería esmeralda, wifi sólido, letras Lucida).
2. **El Dock:** Comprobar viabilidad de `cDock` en Tahoe. Si falla (dado que Apple rescribe SkyLight continuamente), crear un panel inferior usando `Übersicht` o una métrica personalizada para aparentar ese borde 3D en la base de la pantalla.
3. **Rectitud (Yabai):** Instalar `yabai`. Activar las "Scripting Additions" (`sudo yabai --install-sa`). Forzar comandos de inyección para intentar destruir las sobras inmensas y desactivar las curvas, buscando que caiga en una estética de "ventana tosca/industrial" y pragmática.

---

**Nota:** Este documento pertenece al repositorio `mavericks-legacy` y marca nuestra ruta crítica de proyecto a largo plazo.
