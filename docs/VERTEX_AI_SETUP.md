# 🔧 Guía Completa: Habilitar Vertex AI en Firebase

## 📋 Requisitos Previos

Vertex AI requiere **facturación habilitada** en tu proyecto de Google Cloud. Esta guía te ayudará a configurarlo correctamente.

---

## ✅ Paso 1: Habilitar Facturación en Google Cloud

1. **Accede a Google Cloud Console:**
   - Ve a: https://console.cloud.google.com/
   - Selecciona tu proyecto: **recetas-6a516**

2. **Habilitar Facturación:**
   - Ve a: https://console.developers.google.com/billing/enable?project=recetas-6a516
   - O navega manualmente: **Facturación** → **Habilitar facturación**
   - Crea una cuenta de facturación si no tienes una
   - Vincula la cuenta de facturación a tu proyecto

3. **Esperar propagación:**
   - Después de habilitar, espera **5-10 minutos** para que los cambios se propaguen

---

## ✅ Paso 2: Habilitar APIs Necesarias

1. **Ve a la Biblioteca de APIs:**
   - https://console.cloud.google.com/apis/library?project=recetas-6a516

2. **Habilita las siguientes APIs:**
   - ✅ **Vertex AI API** (buscar "Vertex AI API")
   - ✅ **Cloud Storage API** (si usas almacenamiento)
   - ✅ **IAM API** (para permisos)

3. **Verificar habilitación:**
   - Ve a: https://console.cloud.google.com/apis/dashboard?project=recetas-6a516
   - Confirma que "Vertex AI API" aparece como **HABILITADA**

---

## ✅ Paso 3: Configurar Remote Config en Firebase

1. **Accede a Firebase Console:**
   - https://console.firebase.google.com/project/recetas-6a516

2. **Ve a Remote Config:**
   - Navega a: **Build** → **Remote Config**

3. **Agrega los siguientes parámetros:**

   | Clave | Valor | Descripción |
   |-------|-------|-------------|
   | `use_vertex_ai` | `true` | Habilita Vertex AI (cambiar de "false" a "true") |
   | `vertex_location` | `us-central1` | Región de Vertex AI (puede ser: us-central1, us-east1, europe-west1, etc.) |
   | `model_name` | `gemini-2.0-flash-001` | Modelo de Gemini para Vertex AI (usa versión explícita) |

4. **Publica los cambios:**
   - Haz clic en **Publicar cambios**

---

## 🌍 Regiones Disponibles para Vertex AI

Puedes usar cualquiera de estas regiones según tu ubicación:

- **América:**
  - `us-central1` (Iowa, USA) - **Recomendado**
  - `us-east1` (Carolina del Sur, USA)
  - `us-west1` (Oregón, USA)
  - `southamerica-east1` (São Paulo, Brasil)

- **Europa:**
  - `europe-west1` (Bélgica)
  - `europe-west4` (Países Bajos)

- **Asia:**
  - `asia-northeast1` (Tokio, Japón)
  - `asia-southeast1` (Singapur)

**Recomendación:** Usa `us-central1` si no tienes preferencia específica.

---

## ✅ Paso 4: Verificar Configuración en el Código

El código ya está configurado para usar Vertex AI cuando `use_vertex_ai` esté en `true`.

### Verificar que Remote Config se actualice:

```dart
// El servicio automáticamente detectará la configuración
final configService = RemoteConfigService();
await configService.initialize();

// Si use_vertex_ai = "true", usará Vertex AI
// Si use_vertex_ai = "false", usará Google AI (gratis)
```

---

## 🧪 Paso 5: Probar Vertex AI

1. **Actualiza Remote Config en la app:**
   - Reinicia la app o espera a que Remote Config se actualice automáticamente

2. **Prueba una función:**
   ```dart
   final aiService = GeminiAIService(configService);
   final result = await aiService.generateTextToText(
     prompt: "Genera una receta de ceviche",
   );
   ```

3. **Verifica los logs:**
   - Deberías ver: `✅ Usando Vertex AI con location: us-central1`
   - Si ves: `ℹ️ Usando Google AI (gratis, sin facturación)` → Remote Config no se actualizó

---

## ⚠️ Solución de Problemas

### Error: "This API method requires billing to be enabled"

**Causas posibles:**
1. Facturación no habilitada
2. Facturación habilitada pero no propagada (espera 10 minutos)
3. API de Vertex AI no habilitada

**Solución:**
1. Verifica facturación: https://console.cloud.google.com/billing?project=recetas-6a516
2. Verifica API habilitada: https://console.cloud.google.com/apis/library/aiplatform.googleapis.com?project=recetas-6a516
3. Espera 10 minutos y vuelve a intentar

### Error: "Publisher Model ... was not found or your project does not have access to it"

**Causas posibles:**
1. Facturación no habilitada en Google Cloud
2. Vertex AI API no habilitada
3. Nombre del modelo incorrecto (usar `gemini-2.0-flash-001` en lugar de `gemini-2.0-flash`)

**Solución:**
1. Habilita facturación: https://console.cloud.google.com/billing?project=TU_PROYECTO
2. Habilita Vertex AI API: https://console.cloud.google.com/apis/library/aiplatform.googleapis.com
3. En Remote Config, usa `model_name`: `gemini-2.0-flash-001` (versión explícita recomendada)

### Error: "Location not found" o "Invalid location"

**Solución:**
- Verifica que `vertex_location` en Remote Config sea una región válida
- Usa `us-central1` como valor por defecto

### Vertex AI no se activa aunque `use_vertex_ai = "true"`

**Solución:**
1. Verifica que Remote Config se haya actualizado:
   ```dart
   final value = configService.getString("use_vertex_ai");
   print("use_vertex_ai: $value"); // Debe ser "true"
   ```

2. Fuerza actualización de Remote Config:
   ```dart
   await _remoteConfig.fetchAndActivate();
   ```

---

## 💰 Costos de Vertex AI

### Modelos de Texto (Gemini):
- **Gemini 2.0 Flash:** ~$0.075 por 1M tokens de entrada
- **Gemini 2.0 Pro:** ~$1.25 por 1M tokens de entrada

### Generación de Imágenes (Imagen):
- **Imagen 3:** ~$0.020 por imagen generada

**Nota:** Google Cloud ofrece créditos gratuitos ($300) para nuevos usuarios.

---

## 📝 Resumen de Configuración

| Configuración | Valor Actual | Cambio Necesario |
|---------------|--------------|------------------|
| Facturación | ❓ | ✅ Habilitar |
| Vertex AI API | ❓ | ✅ Habilitar |
| Remote Config: `use_vertex_ai` | `false` | ✅ Cambiar a `true` |
| Remote Config: `vertex_location` | `us-central1` | ✅ Ya configurado |

---

## 🔗 Enlaces Útiles

- **Habilitar Facturación:** https://console.developers.google.com/billing/enable?project=recetas-6a516
- **Vertex AI API:** https://console.cloud.google.com/apis/library/aiplatform.googleapis.com?project=recetas-6a516
- **Remote Config:** https://console.firebase.google.com/project/recetas-6a516/config
- **Documentación Vertex AI:** https://cloud.google.com/vertex-ai/docs
- **Precios:** https://cloud.google.com/vertex-ai/pricing

---

## ✅ Checklist Final

- [ ] Facturación habilitada en Google Cloud
- [ ] Vertex AI API habilitada
- [ ] Remote Config actualizado con `use_vertex_ai = "true"`
- [ ] Remote Config actualizado con `vertex_location = "us-central1"`
- [ ] Esperado 10 minutos después de habilitar facturación
- [ ] Probado en la app y funciona correctamente

---

**Última actualización:** Noviembre 2025

