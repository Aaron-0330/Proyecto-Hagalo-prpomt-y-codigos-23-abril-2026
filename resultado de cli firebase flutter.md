Para llevar un proyecto como **HagaloCRUD** al siguiente nivel profesional, es fundamental dominar las herramientas de línea de comandos. Esto automatiza la conexión entre tu código y la consola de Firebase.

Aquí tienes la guía técnica paso a paso para preparar tu entorno en Windows.

---

## 1. Software Necesario: Node.js y NPM
Para usar Firebase CLI, necesitas **Node.js**, que incluye automáticamente **npm** (Node Package Manager).

### Cómo verificar si ya lo tienes
Abre una terminal (PowerShell o CMD) y escribe:
* `node -v`
* `npm -v`

> **Nota:** Si recibes un error indicando que el término no se reconoce, procede con la instalación.

### Instalación paso a paso (Global en Windows)
1.  **Descarga:** Ve al sitio oficial [nodejs.org](https://nodejs.org/) y descarga la versión **LTS** (Long Term Support), que es la más estable.
2.  **Ejecución:** Abre el instalador `.msi`.
3.  **Configuración Clave:** Durante la instalación, asegúrate de que la opción **"Add to PATH"** esté seleccionada (esto permite que los comandos funcionen en cualquier carpeta).
4.  **Finalización:** Acepta instalar las "Tools for Native Modules" si el instalador lo solicita (abrirá una ventana de PowerShell adicional).

---

## 2. Instalación de Firebase CLI
Una vez que `npm` funciona, instalaremos las herramientas de Firebase de forma **global**. Esto significa que el agente de "Firebase Tools" estará disponible para todos tus proyectos de Flutter.

### Comando de instalación global:
Escribe lo siguiente en tu terminal:
```bash
npm install -g firebase-tools
```
* **-g**: Indica que la instalación es global.
* **firebase-tools**: Es el paquete que contiene toda la lógica para interactuar con la consola de Firebase desde tu PC.

---

## 3. Acceso a Firebase con Cuenta de Google
Para que tu computadora tenga permiso de modificar tus proyectos en la nube, debes autenticarte.

### El comando de acceso:
```bash
firebase login
```
**Flujo de trabajo del Agente de Autenticación:**
1.  Al ejecutar el comando, se abrirá automáticamente tu navegador web predeterminado.
2.  Selecciona la **Cuenta de Google** donde creaste tu proyecto "HagaloCRUD".
3.  Haz clic en "Permitir" para conceder acceso a las herramientas de Firebase.
4.  Regresa a la terminal; verás un mensaje de éxito: `Success! Logged in as tu-correo@gmail.com`.

---

## 4. Configuración de Firebase en Flutter (FlutterFire CLI)
Para integrar Firebase de manera "Antigravity" (limpia y rápida), Flutter recomienda usar **FlutterFire CLI**.

### Instalación del activador:
```bash
dart pub global activate flutterfire_cli
```

### Configuración del proyecto:
Dentro de tu carpeta `xflutterdominguez0569/Hagalocrud`, ejecuta:
```bash
flutterfire configure
```
Este comando es un "agente inteligente" que:
* Te permite seleccionar tu proyecto de la lista de Firebase.
* Registra tus aplicaciones Android/iOS automáticamente.
* **Genera el archivo `firebase_options.dart`** en tu carpeta `lib`, eliminando la necesidad de configurar manualmente archivos `.json` o `.plist`.

---

## 5. Resumen de Comandos Útiles (`firebase-tools`)

| Comando | Función del Rol |
| :--- | :--- |
| `firebase projects:list` | Muestra todos los proyectos vinculados a tu cuenta. |
| `firebase init` | Inicia una configuración avanzada (Hosting, Functions, etc.). |
| `firebase logout` | Cierra la sesión de tu cuenta en la máquina actual. |
| `firebase deploy` | Sube reglas de seguridad de Firestore o configuraciones a la nube. |

---

### Metodología de Verificación para Estudiantes
Para asegurar que el flujo de trabajo es funcional, el estudiante debe cumplir este "Checklist de Agente":
1.  [ ] **Agente Node:** `node -v` devuelve una versión (ej. v20.x).
2.  [ ] **Agente Firebase:** `firebase --version` devuelve la versión de las herramientas.
3.  [ ] **Agente Auth:** `firebase login` muestra que ya estás identificado.

¿Deseas que procedamos a explicar cómo utilizar el archivo generado `firebase_options.dart` dentro del código de tu aplicación HagaloCRUD?
