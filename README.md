# Plataforma de seguimiento para sastrerías (multiempresa)

App web para llevar el seguimiento de tickets, prendas, pagos parciales,
inventario, medidas de clientes y comisiones — pensada para que **varias
empresas** la usen, cada una con su propio login y sus propios datos
(aislados entre sí).

## Qué es cada archivo

- `index.html` — la app completa (HTML + CSS). No la edites salvo que sepas qué haces.
- `script.js` — toda la lógica (Firebase, pantallas, módulos). **Aquí es donde pegas tu configuración de Firebase** (ver abajo).
- `firestore.rules` — reglas de seguridad para pegar en la consola de Firebase (aíslan los datos por empresa).

## 1. Crea tu backend en Firebase (gratis)

1. Ve a [console.firebase.google.com](https://console.firebase.google.com) → **Crear un proyecto**.
2. **Authentication** → pestaña *Sign-in method* → habilita **Correo electrónico/contraseña**.
3. **Firestore Database** → *Crear base de datos* → modo producción → elige una región cercana (ej. `us-central` o `southamerica-east1`).
4. **Firestore Database** → pestaña *Reglas* → pega el contenido de `firestore.rules` → *Publicar*.
5. Ícono de engrane (⚙️) → *Configuración del proyecto* → sección *Tus apps* → agrega una app web (ícono `</>`) → copia el bloque `firebaseConfig` que te muestra.

## 2. Conecta la app a tu Firebase

Abre `script.js` y reemplaza esto (líneas 4-11) con el `firebaseConfig` que copiaste:

```js
const firebaseConfig = {
  apiKey: "PEGA_TU_API_KEY",
  authDomain: "TU-PROYECTO.firebaseapp.com",
  projectId: "TU-PROYECTO",
  storageBucket: "TU-PROYECTO.appspot.com",
  messagingSenderId: "000000000000",
  appId: "1:000000000000:web:xxxxxxxxxxxxxxxxxxxx"
};
```

## 3. Súbelo a GitHub y publícalo con GitHub Pages

```bash
git init
git add .
git commit -m "Primera versión"
git branch -M main
git remote add origin https://github.com/TU-USUARIO/TU-REPO.git
git push -u origin main
```

Luego, en GitHub: **Settings → Pages → Source: rama `main`, carpeta `/root`** → Guardar.
En unos minutos tu app estará en `https://TU-USUARIO.github.io/TU-REPO/`.

## 4. Autoriza ese dominio en Firebase

En Firebase: **Authentication → Settings → Authorized domains** → agrega el dominio de GitHub Pages (`TU-USUARIO.github.io`), si no aparece ya automáticamente.

## Cómo funciona el aislamiento por empresa

Cada empresa se registra con correo/contraseña (Firebase Authentication).
Su `uid` de Firebase es el identificador de su empresa (`companyId`).
Todos sus datos viven en Firestore bajo `companies/{companyId}/...`.
Las reglas de `firestore.rules` impiden que una empresa lea o escriba
los datos de otra, aunque conozca la URL o el ID.

## Limitaciones que debes conocer

- **Un solo login por empresa**, no login individual por empleado. El
  selector "Sesión activa" del sidebar es un filtro de conveniencia por
  sucursal, **no es una restricción de seguridad real** (cualquiera con
  la contraseña de la empresa puede cambiarlo).
- **Fotos de prendas guardadas como texto (base64) dentro del propio
  documento de Firestore**, no en un servicio de archivos aparte. Esto
  es simple y funciona bien a baja escala, pero si crecen mucho (miles
  de tickets con fotos), conviene migrar las fotos a **Firebase Storage**
  para no acercarse al límite de tamaño por documento de Firestore (1 MB).
- El plan gratuito de Firebase (Spark) alcanza para empezar; si crece
  mucho el uso, Firebase pasa a cobrar por consumo (plan Blaze).
- Esto es un prototipo funcional, no un producto con soporte, backups
  automáticos verificados, ni pruebas exhaustivas de seguridad — antes
  de venderlo o usarlo con datos reales de terceros, vale la pena una
  revisión de un desarrollador.
