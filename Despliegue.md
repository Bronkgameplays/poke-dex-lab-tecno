# Documentación del Despliegue en Azure Static Web Apps

## Introducción
En esta actividad nosotros realizamos el despliegue de nuestra aplicación PokeDex como una aplicación estática en Azure, utilizando GitHub Actions para la automatización del proceso (CI/CD). El objetivo principal era lograr un flujo completo de integración continua y asegurar la aplicación con cabeceras de seguridad (security headers) para obtener una buena calificación en el escaneo de seguridad.

---

## 1. Clonación del repositorio
El primer paso fue clonar el repositorio que contenía el código fuente de la actividad desde GitHub. Esto se hizo para poder trabajar localmente y realizar las configuraciones necesarias antes del despliegue.

---

## 2. Configuración de Azure Static Web App
Luego procedimos a subir el proyecto a Azure como una aplicación estática. Durante este proceso, se generó un flujo de trabajo (workflow) automático dentro del repositorio, encargado de realizar la construcción y publicación de la aplicación cada vez que se hiciera un push a la rama principal (main).

El archivo del workflow se guardó en la ruta:
- `.github/workflows/azure-static-web-apps-happy-ground-0fd10210f.yml`

---

## 3. Configuración del archivo de workflow
Dentro del archivo YAML se establecieron los pasos del proceso de CI/CD. El flujo realiza lo siguiente:
- Descarga el código del repositorio (checkout).
- Configura Node.js en su versión 18.
- Instala las dependencias del proyecto con npm install.
- Construye el proyecto Angular en modo producción.
- Encuentra automáticamente la carpeta de build (dist).
- Copia los archivos generados en una carpeta temporal de despliegue (deploy).
- Realiza el despliegue directamente en Azure Static Web Apps usando el token de autenticación configurado en los secrets del repositorio.

El token (**AZURE_STATIC_WEB_APPS_API_TOKEN_NICE_MUSHROOM_03922F80F**) también fue actualizado para mantener la seguridad de la integración.

---

## 4. Archivo de configuración de seguridad
Después, agregamos el archivo `staticwebapp.config.json` en la raíz del proyecto. Este archivo contiene las políticas de cabeceras de seguridad (security headers) que ayudan a proteger la aplicación contra ataques como clickjacking, XSS o mixed content.

Algunas de las políticas incluidas fueron:
- **Content-Security-Policy:** para restringir el origen de scripts, estilos, imágenes y fuentes.
- **X-Frame-Options:** configurado en DENY para evitar la carga del sitio dentro de iframes.
- **Strict-Transport-Security:** para forzar la conexión HTTPS.
- **Referrer-Policy** y **Permissions-Policy:** para limitar información compartida por el navegador y controlar permisos del dispositivo.

Además, se configuraron rutas de caché para optimizar el rendimiento: los archivos estáticos (`/assets/*`) con almacenamiento en caché prolongado y los archivos dinámicos con `no-cache`.

---

## 5. Error de autenticación o falta de credenciales
Este error ocurre porque GitHub no tiene acceso al token de despliegue de Azure (**AZURE_STATIC_WEB_APPS_API_TOKEN**).  
Ese token es el que permite que GitHub suba los archivos generados a tu aplicación en Azure.
<img width="899" height="342" alt="image" src="https://github.com/user-attachments/assets/facc0ab5-8ae5-4f8d-af94-74250cecb084" />

En pocas palabras:  
El workflow está listo para desplegar, pero Azure no reconoce la cuenta o el permiso porque el token no fue configurado o se eliminó.

Para solucionarlo, ingresamos a la configuración de Azure, copiamos el token de despliegue (deployment token) y lo pegamos en el campo correspondiente de Azure Token dentro del flujo de GitHub Actions.

Después de realizar este paso, hicimos un pull para actualizar los cambios en el repositorio y el despliegue se completó correctamente. Finalmente, verificamos que todo funcionaba bien desde el dashboard de Azure, confirmando que la aplicación ya estaba publicada con éxito.

**Ruta del workflow:**
.github → workflow → azure-static-web-apps-happy-ground-0fd10210f.yml

**El secret se guardó con el nombre:**
AZURE_STATIC_WEB_APPS_API_TOKEN_NICE_MUSHROOM_03922F80F

Luego de guardar los cambios, volvimos a ejecutar el workflow desde **Actions → Re-run workflow**, y el despliegue se completó exitosamente.

---

## 7. Corrección de error en las imágenes
Durante las pruebas de despliegue, notamos que las imágenes no se mostraban correctamente. El problema estaba en la ruta configurada dentro del archivo `src/environments/environment.prod.ts`.

Originalmente, el valor de `imagesPath` apuntaba a una ruta incorrecta. Lo corregimos a:
imagesPath: '/assets/images'

Con este cambio, las imágenes se cargaron correctamente desde la carpeta de recursos.

---

## 8. Resultado final
Una vez realizados todos los pasos, ejecutamos nuevamente el workflow y el proyecto se desplegó correctamente en Azure. El sitio quedó accesible mediante la siguiente URL:
https://nice-mushroom-03922f80f.2.azurestaticapps.net/

La aplicación funciona perfectamente, con todas las rutas operativas, las imágenes visibles y las políticas de seguridad activas. De esta manera, completamos satisfactoriamente la actividad grupal.
