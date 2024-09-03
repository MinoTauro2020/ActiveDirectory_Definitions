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

