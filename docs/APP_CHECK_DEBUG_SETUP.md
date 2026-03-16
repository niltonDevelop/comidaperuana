# Firebase App Check - Configuración para desarrollo

## Error: "Firebase App Check token is invalid" (401 UNAUTHENTICATED)

Este error ocurre cuando Firebase Auth (o otros servicios) rechazan las peticiones porque el token de App Check no es válido. En desarrollo con el **debug provider**, debes registrar el token en Firebase Console.

---

## Pasos para solucionar

### 1. Obtener el debug token

Ejecuta la app en modo debug (flavor dev, staging o prod):

```bash
flutter run -t lib/main_dev.dart --flavor dev
```

En la **consola de debug** (VS Code Debug Console o Xcode), busca una línea como:

**iOS:**
```
Firebase App Check Debug Token: 123a4567-b89c-12d3-e456-789012345678
```

**Android:**
```
D DebugAppCheckProvider: Enter this debug secret into the allow list in
the Firebase Console for your project: 123a4567-b89c-12d3-e456-789012345678
```

> El token aparece cuando la app hace la **primera petición** a Firebase (por ejemplo, al intentar login con Google). Si no lo ves, habilita el logging de Firebase.

### 2. Habilitar logging de Firebase (iOS)

En Xcode:

1. **Product** → **Scheme** → **Edit Scheme**
2. Selecciona **Run** → pestaña **Arguments**
3. En "Arguments Passed on Launch" añade: `-FIRDebugEnabled`

### 3. Registrar el token en Firebase Console

1. Ve a [Firebase Console](https://console.firebase.google.com)
2. Selecciona el proyecto **goncook-55c25** (o el que corresponda a tu flavor)
3. Menú lateral: **App Check**
4. En la app (iOS o Android), clic en los **3 puntos** (⋮) → **Manage debug tokens**
5. **Add debug token** y pega el token que obtuviste en el paso 1
6. Pon un nombre descriptivo (ej: "Simulador iOS dev")
7. Guarda

### 4. Probar de nuevo

Reinicia la app y vuelve a intentar el login con Google.

---

## Proyecto correcto por flavor

Si usas flavors con **proyectos Firebase distintos** (dev, staging, prod):

- **dev** → registra el token en el proyecto Firebase de desarrollo
- **staging** → en el proyecto de staging
- **prod** → en el proyecto de producción

Cada proyecto tiene sus propios debug tokens.

---

## Seguridad

- No subas los debug tokens a repositorios públicos
- No uses el debug provider en builds de producción
- Si un token se compromete, revócalo en Firebase Console
