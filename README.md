# Open-Core-Lenovo-Yoga-C740-14IML
Version estable y probada de Open Core para Lenovo Yoga C740 14IML | Dual Boot

Este repositorio es una copia de [OpenCore-EFI-Laptop-Lenovo-Yoga-C740-14IML](https://github.com/ThrRip/OpenCore-EFI-Laptop-Lenovo-Yoga-C740-14IML) con algunas instrucciones extras para resolver problemas que surgieron en mi instalacion y para hacer un dualboot (Windows y macOS) **gracias @ThrRip**
## Especificaciones de la laptop usada
| Dispositivo | Modelo |
| -- | -- |
| Laptop | Lenovo Yoga C740-14IML |
| Motherboard | Lenovo (Desconocida) |
| CPU | Intel Core i5-10210U |
| RAM | SK Hynix 8G*2 2667 MHz |
| Almacenamiento | WD Green SN350 1TB M.2 NVMe SSD |
| GPU | Intel Graphics UHD 630 |
| Chip de Audio | Realtek ALC285 & Intel SST |
| Ethernet (External) | Realtek USB GbE Family Controller |
| Wireless (Wi-Fi) | Intel Wi-Fi 6 AX201 160MHz |
| Pantalla | Lenovo Display FHD 14" |

## Problemas
### Aún existe
- La pantalla táctil no funciona
  > Funcionó en versiones de macOS anteriores a macOS Monterey al cambiar de `SSDT-XOSI` a `SSDT-GPI0`, agregar parches XOSI, parchear `VoodooI2CPCIController.cpp` y después de dormir.
- El micrófono incorporado no funciona
### Resuelto
<details>
<summary>Expandir/Contraer</summary>
<br>
  
_Ordenados por fecha de descubrimiento, de más nuevos a más antiguos._

- La aplicación Preferencias del sistema falla (o se congela o muestra el error "No se pudo cargar... el panel de preferencias") al intentar abrir los siguientes paneles:
  -Siri
  - Accesibilidad
  - La red
  - Bluetooth
  - Ratón
  > Se corrigió eliminando `IntelBluetoothInjector.kext` y agregando `BlueToolFixup.kext`, crédito a [instrucciones adicionales para usuarios de Monterey](https://openintelwireless.github.io/IntelBluetoothFirmware/FAQ.html#what-additional-steps-should-i-do-to-make-bluetooth-work-on-macos-monterey-and-newer) por [OpenIntelWireless](https://github.com/OpenIntelWireless).
- Arranque del sistema operativo extremadamente lento
  > Misma causa y solución que el bloqueo de las Preferencias del Sistema.
- El chip de audio no pudo conducir
  > AppleALC.kext con `layout-id` 61 está bien.
- No se puede ingresar al instalador de macOS 11.0.1 (ya sea una actualización en el sistema o una actualización USB)
  > Tienes que establecer el valor de DVMT preasignado por **parchar BIOS** (configurarlo en `config.plist` para OpenCore no funcionará). Consulte [aquí](https://zhuanlan.zhihu.com/p/266400995) para obtener instrucciones y **gracias @MJYINMC**.
- Atascado en `apfs_module_start... Causa de apagado anterior...' al iniciar el instalador
  > No hay problemas con los archivos EFI, resueltos después de actualizar el instalador USB.
- Atascado en `indicadores de IOG... Generación del informe SMC como... IOPPF...` al iniciar el instalador
  > Resuelto después de reparar los SSDT, agregar algunas extensiones de kernel, etc.
- Atascado en `[Final de configuración PCI, puentes 2, dispositivos 20]` al iniciar el instalador
  > Resuelto después de reordenar los SSDT.

</details>

## Instrucciones para resolver algunos problemas
### Parcheando BIOS

<details>
  <summary>Expandir/Contraer</summary>
<br>
  
**DVMT**

Resulta que esta laptop viene con un DVMT (Tecnología de Memoria de Video Dinámica) de 32M, pero nosotros necesitamos 64M para arrancar macOS

**CFG Lock**

Tambien esta laptop viene con un bloqueo de cfg para la administración de energía nativa, por lo que tendremos que desactivarlo

**Archivos Necesarios**

[Descargar Aqui](https://www.mediafire.com/file/sn8fn4mz5lrh97t/InsydeH2OUVE.zip/file)

1. Descomprimir el archivo zip en la raiz del disco local
2. Abrir Teclado en Pantalla o conectar teclado externo (puede que tu teclado no funcione cuando edites la bios)
3. Ejecutar WDFInst.exe como administrador
4. Ejecutar H2OUVE-W-GUIx64.exe como administrador
5. Ir a File y despues a Load Runtime 

    ![](https://pic2.zhimg.com/v2-4f370d88328cd58460c4cb931de90451_r.jpg)

6. Hacer click en Variable como se muestra en la imagen 

    ![](https://pic3.zhimg.com/80/v2-aba9975a6df3819d82e7e7a9916eb32e_1440w.webp)

7. Buscar SaSetup en la segunda columna de la izquierda, marque la casilla pequeña y haga doble clic

    ![](https://pic4.zhimg.com/80/v2-4875bb44f1a782262fead4210da2f877_1440w.webp)
  
**Si no puede encontrarlo, no haga clic al azar. Si comete un error, no podrá iniciar la máquina. Solo cálmese y busque varias veces, y podrá encontrarlo.**

8. Luego bloquee las coordenadas, sabemos que SaSetup modifica el dvmt, su dirección de desplazamiento es 0x107, lo que significa 100 filas y 07 columnas, el valor inicial de este lugar es 01, necesitamos cambiarlo a 02, después de la modificación, presione Entrar , como se muestra en la figura
  
    ![](https://pic1.zhimg.com/80/v2-d01efabdd91a33f4af40ad831aa0dd10_1440w.webp)

9. De la misma forma seleccionamos CpuSetup, desbloqueamos el cfg lock, y cambiamos 01 en la fila 03 y columna 0E a 00, como se muestra en la figura
  
    ![](https://pic4.zhimg.com/80/v2-fa91af8964e651c54d7f64825b99dd57_1440w.webp)
  
10. Después de realizar los cambios, haga clic en el archivo y guarde.
  
    ![](https://pic1.zhimg.com/80/v2-6d2bb0fde9edb09d772b980dbc73cad0_1440w.webp)

Hasta ahora, se han modificado dvmt y cfg lock, y ya podremos arrancar macOS
  
  **Notas**
  
  Si por alguna razon modificas algo mal en la bios, tranquilo, puedes aplicar esta solución
  
  1. Entra a la bios presionando F2 varias veces al iniciar la laptop (Puede que no veas nada y la pantalla no encienda)
  2. Cuando la pantalla este en negro o entres a la bios, presiona F9 para restablecerla
  3. Presiona F10 para guardar cambios
  4. Listo, ahora intenta todos los pasos de nuevo al pie de la letra
  
  Si te interesa, me base en esta guia para los pasos [click aqui](https://zhuanlan.zhihu.com/p/266400995) (usa Google Traductor)
 

</details>

### Reset NVRAM

En el repositorio original, no se incluye esta opción, por lo que tuve que agregarla en este repositorio

Esta opción me fue muy util ya que me resulto que mi laptop no daba imagen, y lo unico que tuve que hacer es dar click a la derecha y darle enter antes de que entre a Windows y asi resetear la NVRAM, asimismo podras entrar a macOS

### Creando medio de instalacion de macOS

Para crear el medio de instalacion use dos dispisitivos de almacenamiento*

**Necesarios**

- Imagen RAW de Olarila [Click aqui](https://www.olarila.com/topic/6278-hackintosh-and-macintosh-olarila-vanilla-images-macos-installer/)
- Balena etcher [Click aqui](https://www.balena.io/etcher)
- USB de minimo 16 GB para macOS (USB 1)
- USB para EFI (USB 2)

**Instrucciones**
1. Copiar la carpeta EFI a la USB 2
2. Flashear la imagen RAW de olarila en la USB 1
3. Bootear con la USB 2 y selecionar la USB de Olarila en el menu de Open Core

### Instalando macOS

<details>
  <summary>Expandir/Contraer</summary>
  <br>
  
  1. Entrar a la utilidad de discos
  2. En visualizacion darle a mostrar todos los dispositivos
  3. Borrar el disco donde instalara macOS
      > - Si tiene un disco HDD seleccionar el formato Mac OS Plus (con registro)
      > - Si tiene un disco SDD seleccionar el fomrato APFS
  4. En esquema seleccionar Mapa de particiones GUID
  5. Salir de la utilidad de discos
  6. Entrar a Instalar macOS
  7. Seleccionar el disco que acabas de borrar
  
</details>


### Dual Boot

<details>
<summary>Expandir/Contraer</summary>
<br>

**Necesarios**

- Instalador de Windows 
  - [Windows 10](https://www.microsoft.com/software-download/windows10)
  - [Windows 11](https://www.microsoft.com/software-download/windows11)

**Instrucciones**

1. Entrar a macOS
2. Agregar una particion en el disco donde instalaras Windows con formato exfat
3. En el menu de Open Core seleccionar Windows
4. Entrar a la instalacion de Windows y seleccionar la opción de Personalizada: instalar solo Windows (avanzado)

    ![](https://www.softzone.es/app/uploads-softzone.es/2020/03/Actualizar-o-instalar-Windows-10-desde-cero.jpg)
    
5. Seleccionar la particion que creaste anteriormente para Windows y eliminarla

    ![](https://www.softzone.es/app/uploads-softzone.es/2020/03/Elegir-disco-y-crear-particiones-para-instalar-Windows-768x576.jpg)
    
6. Salir del instalador
7. Presionar "Reparar el equipo"
8. Ir al cmd y seguir las siguientes instrucciones
    - diskpart
    - list disk
    - select disk 0 (remplazar por el numero de disco en el que instalara windows)
    - create partition efi size=100
    - format quick fs=fat32 label="System"
    - assign letter="S"
    - create partition msr size=16
    - create partition primary
    - format quick fs=ntfs label="Windows"
    - assign letter="W"
    - list volume
    - exit
    - Si creo el instalador en Windows o eligio Windows 10
        - dism /Get-WimInfo /WimFile:D:\sources\install.esd (replace D with USB drive letter)
        - dism /Apply-Image /ImageFile:D:\sources\install.esd /Index:1 /ApplyDir:W:\ (replace D with USB drive letter)
        - bcdboot W:\windows /s S: /f UEFI
    - Si creo el instalador en macOS o eligio Windows 11
        - dism /Get-WimInfo /WimFile:D:\Sources\install.wim (replace D with USB drive letter)
        - dism /Apply-Image /ImageFile:D:\Sources\install.wim /Index:1 /ApplyDir:W:\ (replace D with USB drive letter)
        - bcdboot W:\windows /s S: /f UEFI

Video de YouTube en el que me base: [Click aqui](https://youtu.be/ztxHRGdX0Sw)

</details>
