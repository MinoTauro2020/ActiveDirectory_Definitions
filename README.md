# 🛡️ Lugares de Almacenamiento de Datos Sensibles en Active Directory

En un entorno de Active Directory (AD), existen varios lugares donde se almacenan contraseñas, credenciales, hashes y otros datos sensibles. A continuación, se detallan los principales componentes y sus ubicaciones:

## 1. 📂 NTDS.dit (Active Directory Database)
- **Ubicación**: `C:\Windows\NTDS\NTDS.dit`
- **Descripción**: 
  - Es la base de datos principal de Active Directory.
  - Contiene información sobre todos los objetos del dominio, incluidas las cuentas de usuario y sus hashes de contraseñas (NTLM y Kerberos).

## 2. 🧠 LSASS (Local Security Authority Subsystem Service)
- **Ubicación**: Proceso en memoria (`lsass.exe`)
- **Descripción**: 
  - LSASS gestiona la política de seguridad en el sistema.
  - Almacena en memoria credenciales como hashes de contraseñas y tickets Kerberos de los usuarios autenticados.
  - Herramientas como **Mimikatz** pueden extraer esta información si se tiene acceso al proceso.

## 3. 🔐 SAM (Security Account Manager)
- **Ubicación**: `C:\Windows\System32\config\SAM`
- **Descripción**: 
  - Contiene los hashes de las contraseñas de cuentas locales del sistema.
  - Al acceder a este archivo, se pueden extraer los hashes NTLM de las cuentas locales.

## 4. 🗂️ SYSVOL
- **Ubicación**: `C:\Windows\SYSVOL`
- **Descripción**: 
  - Carpeta compartida que almacena políticas de grupo y scripts de inicio de sesión.
  - Las políticas de grupo pueden incluir configuraciones sensibles, como contraseñas en texto claro para cuentas de servicios.

## 5. 🛠️ Registry (Registros de Windows)
- **Ubicación**:
  - `HKLM\SAM`: Contiene datos cifrados del archivo SAM.
  - `HKLM\SYSTEM`: Contiene las claves de cifrado que pueden usarse para descifrar el SAM.
  - `HKLM\SECURITY`: Puede contener credenciales relacionadas con LSA.
- **Descripción**: 
  - Las credenciales y claves de cifrado se almacenan en el Registro de Windows y pueden ser utilizadas para descifrar los hashes de contraseñas.

## 6. 💾 Cache de Credenciales (Cached Credentials)
- **Ubicación**: `HKLM\SECURITY\Cache`
- **Descripción**: 
  - Windows almacena en caché los hashes de contraseñas de los usuarios que han iniciado sesión localmente.
  - Permite el inicio de sesión sin conexión, pero estos hashes pueden ser extraídos y atacados offline.

## 7. 🎟️ KERBEROS Tickets
- **Ubicación**: Almacenados temporalmente en la memoria (administrados por LSASS)
- **Descripción**: 
  - Los tickets de servicio (TGS) y de autenticación (TGT) son utilizados por Kerberos para autenticar a los usuarios en la red.
  - Pueden ser explotados en ataques como "Pass-the-Ticket".

## 8. 📝 GPP (Group Policy Preferences)
- **Ubicación**: `C:\Windows\SYSVOL\domain\Policies\{PolicyGUID}\Machine\Preferences\`
- **Descripción**: 
  - Anteriormente, las GPP permitían almacenar contraseñas en archivos XML en texto claro o cifradas con una clave fácilmente reversible.
  - Aunque esta función fue deshabilitada, algunos sistemas antiguos aún pueden ser vulnerables.

## 9. 🔐 Windows Credential Manager
- **Ubicación**: 
  - A través de la interfaz gráfica en `Control Panel > User Accounts > Credential Manager`.
  - Los datos se almacenan en el registro de Windows bajo `HKLM\SECURITY` y `HKCU\Security`.
- **Descripción**: 
  - Almacena credenciales para aplicaciones y servicios, incluidas las contraseñas de redes, sitios web y recursos compartidos.
  - Estas credenciales pueden ser utilizadas por herramientas de post-explotación para obtener acceso a otros sistemas.

## 10. 💻 RDP (Remote Desktop Protocol)
- **Ubicación**:
  - Las credenciales RDP se almacenan en el Windows Credential Manager.
  - Las conexiones RDP pueden guardar credenciales en la caché local o en el archivo `Default.rdp`.
- **Descripción**:
  - Si se configuran conexiones RDP con "Guardar credenciales", estas se almacenan y pueden ser extraídas por atacantes con acceso al sistema.
  - El archivo `Default.rdp` se encuentra generalmente en `C:\Users\[Username]\Documents` y puede contener credenciales guardadas.

## 11. 🧳 DPAPI (Data Protection API)
- **Ubicación**: Las claves maestras se almacenan en `C:\Users\[Username]\AppData\Roaming\Microsoft\Protect\[User SID]\`.
- **Descripción**: 
  - DPAPI se utiliza para proteger datos sensibles, como contraseñas guardadas, certificados y claves privadas.
  - Las claves maestras utilizadas para cifrar y descifrar estos datos están protegidas por la contraseña del usuario, pero si un atacante obtiene acceso a estas claves, puede descifrar los datos protegidos por DPAPI.

## 12. 🛡️ LSA Secrets
- **Ubicación**: Almacenados en el Registro de Windows bajo `HKLM\SECURITY\Policy\Secrets`.
- **Descripción**: 
  - LSA Secrets son utilizados para almacenar información sensible como credenciales de cuentas de servicio y contraseñas de administrador local.
  - Un atacante con acceso a estos secretos puede extraer credenciales para acceder a otros sistemas.

## 13. 🔑 Master Key
- **Ubicación**: `C:\Users\[Username]\AppData\Roaming\Microsoft\Crypto\RSA\[User SID]\`.
- **Descripción**: 
  - La Master Key es utilizada por DPAPI para cifrar y descifrar datos sensibles.
  - Si un atacante puede acceder a la Master Key, puede potencialmente descifrar los datos protegidos por DPAPI en ese perfil de usuario.

## 14. 💼 Credential Guard
- **Ubicación**: Protegido en un entorno aislado por hardware, conocido como VSM (Virtual Secure Mode).
- **Descripción**: 
  - Credential Guard es una característica de seguridad en Windows que aísla credenciales de NTLM y Kerberos para evitar su robo.
  - Funciona en un entorno virtualizado, dificultando el acceso a las credenciales incluso si el sistema es comprometido.

## 15. 🔐 SAMBA Credentials (para entornos mixtos Windows/Linux)
- **Ubicación**: `C:\etc\samba\smb.conf` (en sistemas Linux/UNIX)
- **Descripción**: 
  - En entornos mixtos con Samba, se pueden almacenar credenciales en archivos de configuración.
  - Es importante proteger estos archivos, ya que pueden contener contraseñas de usuario o información de dominio.

## 16. 📦 Backup de NTDS.dit
- **Ubicación**: Depende de la configuración del administrador, pero generalmente se almacenan en medios externos o en ubicaciones específicas del servidor.
- **Descripción**: 
  - Los backups de `NTDS.dit` contienen la misma información sensible que la base de datos activa.
  - Si un backup no está adecuadamente protegido, un atacante podría restaurarlo en otro entorno para extraer información sensible.

## 17. 📊 Event Logs (Registros de eventos)
- **Ubicación**: `C:\Windows\System32\winevt\Logs\`
- **Descripción**: 
  - Los registros de eventos de Windows pueden contener información sensible, como intentos fallidos de inicio de sesión, acceso a recursos, y más.
  - Un atacante puede usar esta información para obtener pistas sobre credenciales o vulnerabilidades en el sistema.

## 18. 🗄️ Cached RDP Credentials
- **Ubicación**: `C:\Users\[Username]\AppData\Local\Microsoft\Terminal Server Client\Cache`
- **Descripción**: 
  - Las credenciales de RDP pueden ser almacenadas en caché en el cliente, lo que podría permitir a un atacante reutilizarlas si obtiene acceso al sistema.
  - Es crucial asegurarse de que estas credenciales no se guarden en sistemas compartidos o inseguros.

## 19. 🕵️ Network Capture Files (.pcap, .cap)
- **Ubicación**: Depende de la herramienta utilizada (por ejemplo, Wireshark puede guardar en cualquier ubicación).
- **Descripción**: 
  - Los archivos de captura de red pueden contener credenciales transmitidas sin cifrar o información que puede ser utilizada para recrear sesiones.
  - Es esencial proteger estos archivos para evitar que un atacante los use para obtener credenciales.

## 20. 🧪 Offline Domain Join Blob
- **Ubicación**: `C:\Windows\Provisioning\Packages\`
- **Descripción**: 
  - El "Offline Domain Join Blob" es un archivo que contiene información cifrada necesaria para unir una máquina a un dominio sin conexión.
  - Si este archivo se compromete, un atacante podría utilizarlo para unir dispositivos no autorizados al dominio.
## 21. 📱 Cached Domain Logons
- **Ubicación**: `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\`
- **Descripción**: 
  - Windows permite a los usuarios iniciar sesión en un dominio incluso si el controlador de dominio no está disponible, utilizando credenciales en caché.
  - Estas credenciales en caché se pueden atacar para intentar recuperar hashes de contraseñas de dominio.

## 22. 🎛️ Group Policy Objects (GPOs)
- **Ubicación**: `C:\Windows\SYSVOL\domain\Policies\{PolicyGUID}\`
- **Descripción**: 
  - Las GPOs pueden contener configuraciones de seguridad, scripts, y otras configuraciones que pueden incluir información sensible, como scripts de inicio de sesión que contienen contraseñas en texto claro.
  - Es importante asegurarse de que las GPOs no contengan información sensible sin la debida protección.

## 23. 🔒 Encrypted File System (EFS) Keys
- **Ubicación**: `C:\Users\[Username]\AppData\Roaming\Microsoft\Crypto\RSA\[User SID]\`
- **Descripción**: 
  - Las claves EFS se utilizan para cifrar archivos en NTFS. Si estas claves se comprometen, los archivos cifrados pueden ser descifrados por un atacante.
  - La protección de estas claves es crucial para mantener la seguridad de los archivos cifrados en el sistema.

## 24. 📇 Windows Vault (Credentials Vault)
- **Ubicación**: `C:\Users\[Username]\AppData\Local\Microsoft\Vault\`
- **Descripción**: 
  - Windows Vault es otra ubicación donde se pueden almacenar credenciales de red y de aplicaciones.
  - Un atacante con acceso a estos archivos puede extraer credenciales almacenadas y utilizarlas para acceder a otros recursos.

## 25. 🗳️ BitLocker Recovery Information
- **Ubicación**: `Active Directory` (si está configurado) o en un archivo de texto guardado por el usuario.
- **Descripción**: 
  - BitLocker puede almacenar la clave de recuperación en Active Directory, lo que permite a los administradores desbloquear unidades cifradas.
  - Si un atacante obtiene acceso a esta clave, podría descifrar los datos protegidos por BitLocker.

## 26. 📤 Print Spooler
- **Ubicación**: `C:\Windows\System32\spool\PRINTERS\`
- **Descripción**: 
  - El Print Spooler de Windows puede ser una fuente inadvertida de información sensible, ya que las tareas de impresión pueden contener documentos confidenciales.
  - Exploits que abusan del servicio de Print Spooler han sido utilizados para ataques de escalada de privilegios (e.g., PrintNightmare).

## 27. 🛠️ System Volume Information
- **Ubicación**: `C:\System Volume Information\`
- **Descripción**: 
  - Esta carpeta contiene puntos de restauración del sistema y otros datos del sistema.
  - En algunos casos, podría contener versiones anteriores de archivos que pueden incluir datos sensibles que un atacante podría intentar restaurar o leer.

## 28. 🖥️ Scheduled Tasks
- **Ubicación**: `C:\Windows\System32\Tasks\`
- **Descripción**: 
  - Las tareas programadas pueden ejecutarse con credenciales almacenadas que, si se configuran incorrectamente, pueden ser explotadas por un atacante para ejecutar tareas con privilegios elevados.
  - Es esencial revisar regularmente las tareas programadas para asegurar que no contengan credenciales sensibles o que no sean usadas maliciosamente.

## 29. 🔧 LAPS (Local Administrator Password Solution)
- **Ubicación**: La contraseña se almacena en un atributo confidencial del objeto de la computadora en Active Directory.
- **Descripción**: 
  - LAPS gestiona las contraseñas de las cuentas de administrador local en cada máquina del dominio, almacenando la contraseña en AD con cifrado.
  - Aunque está diseñado para mejorar la seguridad, es importante proteger el acceso a este atributo, ya que un atacante con privilegios suficientes podría obtener las contraseñas.

## 30. 🧰 Credential Dumping Tools Artifacts
- **Ubicación**: Dependiendo de la herramienta, en varias ubicaciones temporales o en memoria.
- **Descripción**: 
  - Herramientas de volcado de credenciales como Mimikatz, hashdump, y otras pueden dejar rastros o artefactos en el sistema.
  - Es importante monitorear y detectar la presencia de estas herramientas para prevenir que un atacante obtenga credenciales sensibles.

## 31. 📁 Recycle Bin (Papelera de reciclaje de AD)
- **Ubicación**: Almacenado dentro de la base de datos de Active Directory (`NTDS.dit`).
- **Descripción**: 
  - La Papelera de reciclaje de AD permite la recuperación de objetos eliminados.
  - Aunque es una función de recuperación, los objetos eliminados, que podrían contener información sensible, pueden ser restaurados si no se gestionan adecuadamente.

## 32. 🛠️ Windows Installer (.msi y .msp files)
- **Ubicación**: Varios, típicamente en `C:\Windows\Installer\`.
- **Descripción**: 
  - Los archivos MSI y MSP pueden contener información de configuración, incluidas credenciales codificadas o datos sensibles si no se implementan correctamente.
  - Es importante asegurarse de que los paquetes de instalación no incluyan información confidencial sin protección.

## 33. 🌐 Internet Explorer/Edge Saved Passwords
- **Ubicación**: Almacenadas en `Credential Manager` y también en el perfil del usuario.
- **Descripción**: 
  - Las contraseñas guardadas por los navegadores Internet Explorer o Edge pueden ser extraídas si se tiene acceso al sistema.
  - Esto incluye contraseñas para sitios web y redes, lo que puede comprometer aún más la seguridad del usuario.

## 34. 🖨️ Spoolsv.exe (Servicio de Cola de Impresión)
- **Ubicación**: En ejecución en memoria (`spoolsv.exe`).
- **Descripción**: 
  - El servicio de cola de impresión maneja las solicitudes de impresión y puede exponer información sensible en un ataque.
  - Exploits dirigidos a este servicio, como "PrintNightmare", han demostrado cómo las configuraciones incorrectas pueden ser explotadas.

## 35. 🔄 Offline Files (Archivos sin conexión)
- **Ubicación**: `C:\Windows\CSC\`
- **Descripción**: 
  - Los archivos sin conexión permiten a los usuarios acceder a archivos de red incluso cuando no están conectados.
  - Estos archivos pueden contener datos sensibles que se almacenan localmente y podrían ser accesibles a través de ataques offline.

## 36. 📦 Software Distribution Folder
- **Ubicación**: `C:\Windows\SoftwareDistribution\`
- **Descripción**: 
  - Esta carpeta contiene archivos relacionados con la actualización de Windows y podría almacenar temporalmente credenciales o datos sensibles si no se gestiona correctamente.
  - Aunque no es común, la manipulación o el acceso a esta carpeta podría potencialmente exponer información.

## 37. 🗝️ RAS/VPN Credentials
- **Ubicación**: En el Registro o dentro del `Credential Manager`.
- **Descripción**: 
  - Las credenciales usadas para conexiones RAS (Remote Access Service) o VPN (Virtual Private Network) pueden estar almacenadas localmente.
  - Si no se protegen adecuadamente, un atacante podría usar estas credenciales para obtener acceso remoto a la red.

## 38. 📂 Shadow Copies
- **Ubicación**: `C:\System Volume Information\`
- **Descripción**: 
  - Las copias sombra permiten la recuperación de versiones anteriores de archivos, lo que puede incluir datos sensibles.
  - Un atacante que acceda a las copias sombra podría recuperar versiones anteriores de archivos que contengan información sensible.

## 39. 🔍 Prefetch Files
- **Ubicación**: `C:\Windows\Prefetch\`
- **Descripción**: 
  - Los archivos de prefetch contienen información sobre las aplicaciones ejecutadas, incluidas las credenciales almacenadas o temporales.
  - Aunque son principalmente para mejorar el rendimiento del sistema, estos archivos pueden proporcionar pistas sobre las actividades y credenciales usadas.

## 40. 🛑 Windows Error Reporting (WER)
- **Ubicación**: `C:\ProgramData\Microsoft\Windows\WER\`
- **Descripción**: 
  - WER almacena informes de errores que pueden contener datos de procesos, incluidas credenciales si se genera un volcado de memoria.
  - La explotación de estos informes de errores podría exponer información sensible sobre el sistema y las aplicaciones.

## 41. 🧩 System State Backups
- **Ubicación**: Generalmente almacenados en ubicaciones específicas de backup o medios externos.
- **Descripción**: 
  - Los backups del estado del sistema incluyen el registro, la base de datos de Active Directory, y otros datos críticos.
  - Un atacante que obtenga acceso a estos backups podría restaurarlos y extraer información sensible, como contraseñas y hashes de contraseñas.

## 42. 🗂️ Roaming Profiles
- **Ubicación**: Generalmente en un servidor de archivos compartido, `\\[servidor]\Profiles\[Username]\`.
- **Descripción**: 
  - Los perfiles móviles permiten que los usuarios accedan a su perfil desde cualquier máquina dentro del dominio.
  - Estos perfiles pueden contener credenciales, configuraciones sensibles, e incluso archivos temporales que podrían exponer información si no se gestionan adecuadamente.

## 43. 📄 IIS Configuration Files
- **Ubicación**: `C:\inetpub\wwwroot\` y `C:\Windows\System32\inetsrv\config\`.
- **Descripción**: 
  - Los archivos de configuración de IIS (Internet Information Services) pueden contener credenciales de acceso a bases de datos o a otros recursos.
  - Estos archivos deben ser revisados y asegurados para evitar la exposición de credenciales.

## 44. 🌐 Windows Communication Foundation (WCF) Configuration
- **Ubicación**: Archivos de configuración como `web.config` y `app.config` en aplicaciones .NET.
- **Descripción**: 
  - Los servicios WCF pueden estar configurados para almacenar credenciales y otras configuraciones sensibles en archivos de configuración.
  - La falta de cifrado o protección de estos archivos puede llevar a la exposición de información confidencial.

## 45. 📊 Data Protection API (DPAPI) Master Keys Backup
- **Ubicación**: Pueden estar almacenados en dispositivos USB, discos externos, o en ubicaciones de red.
- **Descripción**: 
  - Los backups de claves maestras de DPAPI son críticos para la recuperación de datos cifrados.
  - Si un atacante obtiene acceso a estas claves, podría descifrar todos los datos protegidos por DPAPI para ese usuario o sistema.

## 46. 🗝️ PKI (Public Key Infrastructure) Certificates and Keys
- **Ubicación**: `C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\` y en el almacén de certificados del usuario.
- **Descripción**: 
  - Los certificados y las claves privadas utilizadas para la autenticación, cifrado, y firma digital son extremadamente sensibles.
  - Si se comprometen, un atacante puede usarlos para suplantar identidades, descifrar datos, o firmar código malicioso.

## 47. 🛡️ Credential Guard Artifacts
- **Ubicación**: Dentro de un entorno virtualizado protegido por el VSM (Virtual Secure Mode).
- **Descripción**: 
  - Credential Guard utiliza VSM para proteger las credenciales en un entorno aislado.
  - Aunque difícil de comprometer, las vulnerabilidades en la implementación de VSM o el hypervisor podrían exponer estas credenciales.

## 48. 🗒️ Event Forwarding Logs
- **Ubicación**: `C:\Windows\System32\winevt\Logs\ForwardedEvents.evtx`
- **Descripción**: 
  - En entornos donde se utiliza el reenviador de eventos de Windows, los logs reenviados pueden contener información sensible, como intentos de autenticación fallidos o detalles de accesos a recursos.
  - Es importante proteger y monitorear estos logs para evitar la exposición de información crítica.

## 49. 🛠️ System Management BIOS (SMBIOS) Data
- **Ubicación**: Almacenado en el firmware y accesible a través de la BIOS o UEFI.
- **Descripción**: 
  - SMBIOS puede almacenar información sobre la configuración del sistema, incluido el hardware, y en algunos casos, credenciales predeterminadas para acceso remoto o administración.
  - Es importante asegurar que este tipo de información no se exponga o no se use credenciales predeterminadas.

## 50. 🌍 Active Directory Federation Services (ADFS) Configuration Files
- **Ubicación**: `C:\Windows\ADFS\`.
- **Descripción**: 
  - ADFS se utiliza para la autenticación única (SSO) y puede almacenar configuraciones sensibles, incluidas las credenciales para servicios federados.
  - La configuración incorrecta o la falta de cifrado en estos archivos puede llevar a la exposición de credenciales y datos sensibles.

## 51. 🧰 Windows Subsystem for Linux (WSL)
- **Ubicación**: `C:\Users\[Username]\AppData\Local\Packages\CanonicalGroupLimited...`
- **Descripción**: 
  - WSL permite a los usuarios ejecutar un entorno Linux en Windows, y los archivos y configuraciones dentro de este entorno pueden contener credenciales y claves SSH.
  - Un atacante con acceso a este entorno podría comprometer las credenciales de servicios y aplicaciones ejecutadas en WSL.

## 52. 🖥️ Hyper-V Virtual Machines
- **Ubicación**: Archivos VHDX en `C:\ProgramData\Microsoft\Windows\Hyper-V\` o donde se configuren.
- **Descripción**: 
  - Las máquinas virtuales (VMs) pueden contener sistemas operativos completos con sus propios datos sensibles, incluyendo credenciales y configuraciones de AD.
  - Si un atacante accede a los archivos VHDX, puede montar la VM en otro entorno y extraer información sensible.

## 53. 🌩️ Azure AD Connect
- **Ubicación**: Configuración y logs en `C:\Program Files\Microsoft Azure AD Sync\`.
- **Descripción**: 
  - Azure AD Connect sincroniza objetos y credenciales entre Active Directory on-premises y Azure AD.
  - Los archivos de configuración y logs pueden contener credenciales o información sensible sobre la sincronización.

## 54. 📅 Cached Exchange Mode (Outlook)
- **Ubicación**: Archivos OST en `C:\Users\[Username]\AppData\Local\Microsoft\Outlook\`.
- **Descripción**: 
  - Outlook en modo de intercambio en caché almacena correos electrónicos y otros datos en un archivo OST.
  - Si este archivo se compromete, puede exponer correos electrónicos sensibles y otras informaciones almacenadas.

## 55. 🖥️ Remote Desktop Connection Manager (RDCMan)
- **Ubicación**: Archivos de configuración `.rdg` en ubicaciones definidas por el usuario.
- **Descripción**: 
  - RDCMan es utilizado para administrar múltiples conexiones RDP y puede almacenar credenciales en su archivo de configuración.
  - La exposición de este archivo puede dar acceso a credenciales de múltiples sistemas.

## 56. 🎫 Web Proxy Auto-Discovery Protocol (WPAD)
- **Ubicación**: Configurado a través de DHCP o DNS.
- **Descripción**: 
  - WPAD se utiliza para descubrir automáticamente el servidor proxy de la red.
  - Si está mal configurado, un atacante puede manipular el servidor proxy para interceptar o redirigir tráfico, obteniendo credenciales y otros datos sensibles.

## 57. 🔍 User Profile Disks (UPD)
- **Ubicación**: Almacenado en servidores de archivos, en rutas como `\\[servidor]\UserProfileDisks\`.
- **Descripción**: 
  - UPD permite a los usuarios acceder a sus perfiles desde cualquier máquina en un entorno VDI (Virtual Desktop Infrastructure).
  - Estos discos pueden contener credenciales y configuraciones sensibles, por lo que deben ser protegidos adecuadamente.

## 58. 🔗 Token-Based Authentication Data
- **Ubicación**: Almacenado en memoria o en archivos temporales.
- **Descripción**: 
  - Los tokens de autenticación (como OAuth, JWT) se utilizan para autenticaciones sin necesidad de contraseñas.
  - Si estos tokens son interceptados o robados, pueden ser reutilizados por un atacante para acceder a recursos protegidos.

## 59. 🌐 IIS Log Files
- **Ubicación**: `C:\inetpub\logs\LogFiles\`
- **Descripción**: 
  - Los logs de IIS pueden contener URLs, direcciones IP, y en algunos casos, información sensible enviada a través de HTTP, como credenciales.
  - Estos logs deben ser monitorizados y protegidos para evitar la exposición de datos sensibles.

## 60. 🧩 PowerShell Transcription Logs
- **Ubicación**: Configurable, comúnmente en `C:\ProgramData\Microsoft\Windows\PowerShell\`.
- **Descripción**: 
  - La transcripción de PowerShell graba todos los comandos y salidas en un archivo de log.
  - Si se utilizan comandos que manejan información sensible, como contraseñas, estos podrían quedar expuestos en los logs.

## 61. 🛠️ SCCM (System Center Configuration Manager) Database
- **Ubicación**: Generalmente en una instancia de SQL Server gestionada por SCCM.
- **Descripción**: 
  - La base de datos de SCCM contiene información detallada sobre la configuración del sistema, incluidos inventarios de software, configuraciones de red y, potencialmente, datos sensibles.
  - La seguridad de esta base de datos es crítica para evitar el acceso no autorizado a información de la infraestructura.

## 62. 📱 Enterprise Mobility Management (EMM) Profiles
- **Ubicación**: Configuración distribuida a dispositivos móviles, almacenada en servidores de administración.
- **Descripción**: 
  - Los perfiles EMM contienen configuraciones y políticas que pueden incluir credenciales de acceso a redes corporativas.
  - Si un atacante compromete estos perfiles, podría obtener acceso a dispositivos móviles gestionados por la organización.

## 63. 🖥️ Docker Containers on Windows
- **Ubicación**: `C:\ProgramData\Docker\` y otros lugares configurados.
- **Descripción**: 
  - Los contenedores Docker pueden contener aplicaciones completas con configuraciones y credenciales embebidas.
  - Si un contenedor es comprometido, las credenciales y configuraciones internas pueden ser expuestas.

## 64. 🌐 Application Insights Logs (para aplicaciones en la nube)
- **Ubicación**: Almacenados en Azure Monitor o en otras ubicaciones configuradas.
- **Descripción**: 
  - Application Insights recopila telemetría de aplicaciones, incluidos logs de errores que podrían contener información sensible.
  - Es importante revisar y proteger estos logs para evitar que datos sensibles sean capturados accidentalmente.

## 65. 🗂️ Windows Event Collector (WEC)
- **Ubicación**: Almacenado en `C:\Windows\System32\winevt\Logs\ForwardedEvents.evtx`.
- **Descripción**: 
  - WEC recolecta y centraliza logs de eventos desde múltiples sistemas.
  - Estos logs pueden contener información sensible sobre la actividad del sistema y deben ser protegidos contra accesos no autorizados.

## 66. 🔑 Cached Service Tickets (Kerberos)
- **Ubicación**: En memoria, manejado por LSASS.
- **Descripción**: 
  - Los tickets de servicio de Kerberos pueden ser almacenados en caché para mejorar el rendimiento.
  - Si un atacante puede acceder a estos tickets, puede utilizarlos en ataques de "Pass-the-Ticket" para suplantar la identidad de usuarios.

## 67. 🧰 Windows Subsystem for Linux (WSL) Filesystem
- **Ubicación**: `\\wsl$\<distro>` o `C:\Users\[Username]\AppData\Local\Packages\CanonicalGroupLimited...`
- **Descripción**: 
  - WSL proporciona un entorno de Linux dentro de Windows, y el sistema de archivos puede contener configuraciones y scripts con credenciales.
  - Un atacante con acceso a estos archivos puede comprometer las credenciales almacenadas en ellos.

## 68. 🌍 DNS Debug Logs
- **Ubicación**: `C:\Windows\System32\dns\dns.log`
- **Descripción**: 
  - Los logs de depuración de DNS pueden registrar todas las consultas y respuestas DNS.
  - Estos logs pueden revelar información sobre la estructura interna de la red, incluidas direcciones IP sensibles y nombres de dominio internos.

## 69. 🌐 Web Application Configuration Files
- **Ubicación**: Dependiendo del servidor web y la aplicación, por ejemplo, `C:\inetpub\wwwroot\` para IIS.
- **Descripción**: 
  - Los archivos de configuración de aplicaciones web (como `web.config` en ASP.NET) pueden contener credenciales para bases de datos o servicios externos.
  - La exposición de estos archivos puede comprometer toda la aplicación y los recursos a los que tiene acceso.

## 70. 📂 Windows Package Manager Repositories
- **Ubicación**: Configurado en `C:\Program Files\Windows Package Manager\`.
- **Descripción**: 
  - Los repositorios del gestor de paquetes pueden incluir scripts o configuraciones que almacenan credenciales.
  - Es importante proteger estos scripts para evitar que un atacante inyecte código malicioso o extraiga credenciales.



