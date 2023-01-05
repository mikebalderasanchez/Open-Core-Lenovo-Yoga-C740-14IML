# Open-Core-Lenovo-Yoga-C740-14IML
Version estable y probada de Open Core para Lenovo Yoga C740 14IML | Dual Boot
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
