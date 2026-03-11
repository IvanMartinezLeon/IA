
---
# 🚀 StartFlutter_Ultimate_Engine_v0.0

> **⚙️ CONFIGURACIÓN:** Reemplaza `USUARIO/REPO` en las URLs con tu usuario y repositorio de GitHub.
> Ejemplo: `https://raw.githubusercontent.com/tu_usuario/flutter-workflow/main/doc_templates/`

## Descripción
Creación de Proyecto Flutter Estándar con Múltiples Entornos e Internacionalización.
Verificar el entorno y crear un nuevo proyecto Flutter estándar con:
- Configuración para tres entornos (debug, profile, release) mediante archivos `.env`
- Sistema de internacionalización con soporte para español (castellano) e inglés
- Detección automática del idioma del dispositivo
- Estructura lista para producción

## 0. Verificación del Entorno (Flutter Doctor)

**Acción obligatoria:** Analizar la salud del SDK antes de cualquier operación.

```bash
flutter doctor

```

> **Regla Crítica:** Detener el workflow si hay errores (❌) en Flutter SDK o Android Toolchain.

---

## 1. 🎛️ Fase de Configuración Interactiva (Ficha de Inicio)

**Instrucción para el Agente:** Detente y solicita al usuario estos datos. No ejecutes ninguna acción hasta recibir la ficha completa.

> ### 📝 Ficha de Inicialización de Proyecto
> 
> 
> 1. **Nombre de la Aplicación:** `[Escribe_aquí]` (ej: `nexus_app`)
> 2. **Bundle ID (Org):** `[Escribe_aquí]` (ej: `com.estudio.nexus`)
> 3. **Gestor de Estado (Marca con [X] solo uno):**
> 
> Por ejemplo, podrías ponerme:
> "Nombre: fitness_tracker, Org: com.miestudio, Gestor: Bloc"
> 
> 

---

## 2. 🛠️ Ejecución del Motor de Creación

### A. Identidad y Skill de IA

**1. Verificar estado de carpeta:**
```bash
# Verificar contenido y nombre de la carpeta actual
CURRENT_DIR=$(basename "$PWD")
ls -la
```

**2. Decisión automática:**
- Si carpeta vacía → `flutter create .`
- Si carpeta tiene contenido distinto al nombre → `flutter create --org <bundle_id> <nombre_app>`
- Si carpeta actual ya se llama igual que `<nombre_app>` → `flutter create --org <bundle_id> .`

```bash
# Crear proyecto con el Bundle ID y Nombre definidos
# El agente debe detectar automáticamente la opción correcta

if [ -z "$(ls -A)" ]; then
  # Carpeta vacía
  flutter create --org <bundle_id> .
elif [ "$CURRENT_DIR" = "<nombre_app>" ]; then
  # Ya estamos en carpeta con nombre del proyecto
  flutter create --org <bundle_id> .
else
  # Carpeta con otro contenido
  flutter create --org <bundle_id> <nombre_app>
  cd <nombre_app>
fi

```

* **Skill activada:** La IA ahora opera como Senior Flutter & Dart Architect.

> **⚡ ACTIVACIÓN DEL SKILL:** El Agente debe ejecutar ahora:
> ```bash
> skill --name flutter-expert
> ```
> Esto carga las instrucciones del experto Flutter en el contexto de la sesión.

### B. Configuración de `pubspec.yaml` (Base Técnica)

El Agente debe asegurar la siguiente configuración para habilitar la generación de código y los entornos:

```yaml
flutter:
  generate: true # Obligatorio para intl y generadores
  uses-material-design: true
  assets:
    - assets/env/.env.debug
    - assets/env/.env.profile
    - assets/env/.env.release

```

### C. Inyección de Dependencias Dinámica

* **Comunes:** `flutter_dotenv`, `intl`, `flutter_localizations`.
* **Específicos:** Según el gestor de estado elegido en el punto 1.3.

---

## 3. 🧠 Cerebro del Proyecto & SDD

> **📋 Templates:** https://raw.githubusercontent.com/USUARIO/REPO/main/doc_templates/

**Archivos de referencia:**
- `project_memory_template.md` - Log de decisiones
- `agent_rules.md` - Reglas del agente

**Crear archivos obligatorios:**
- `PROJECT_MEMORY.md` - Log de decisiones (usar template)
- `doc/PROJECT_SPECIFICATIONS.md` - Modelos y contratos
- `doc/architecture.md` - Guía de capas

---

## 4. 🌐 Entornos e Internacionalización

> **📋 Templates:** https://raw.githubusercontent.com/USUARIO/REPO/main/doc_templates/

**Archivos de referencia:**
- `env_templates.md` - Plantillas .env
- `environment_dart.md` - Código environment.dart
- `l10n_templates.md` - Configuración i18n
- `pubspec_template.md` - pubspec.yaml

### Pasos:
1. Crear `assets/env/` con archivos .env (ver `env_templates.md`)
2. Crear `lib/config/environment.dart` (ver `environment_dart.md`)
3. Configurar `l10n.yaml` y archivos `.arb` (ver `l10n_templates.md`)
4. Actualizar `pubspec.yaml` (ver `pubspec_template.md`)
5. Ejecutar `flutter pub get && flutter gen-l10n`

---

## 5. 🔄 Protocolo de Sincronización y Calidad

> **📋 Templates:** 
> - Protocolo: https://raw.githubusercontent.com/USUARIO/REPO/main/doc_templates/agent_rules.md
> - Ejemplo main.dart: https://raw.githubusercontent.com/USUARIO/REPO/main/doc_templates/main_example.md

Para cada tarea, el Agente seguirá este ciclo de vida:
1. **SDD Update:** Actualizar especificaciones en `doc/`
2. **Code & Gen:** Escribir código, ejecutar `flutter pub get` y `flutter gen-l10n`
3. **Linter Check:** `flutter analyze` (0 errores permitidos)
4. **Memory Log:** Registrar en `PROJECT_MEMORY.md`

---

## 6. ✅ Validación de Estructura Completa

**Archivos obligatorios:**
- `pubspec.yaml` (con flutter_dotenv, intl, flutter_localizations)
- `l10n.yaml` (configuración de localizaciones)
- `lib/config/environment.dart`
- `lib/l10n/app_es.arb`
- `lib/l10n/app_en.arb`
- `lib/l10n/generated/app_localizations.dart` (generado)
- `assets/env/.env.debug`
- `assets/env/.env.profile`
- `assets/env/.env.release`
- `assets/env/.env.example` (plantilla)
- `.gitignore` (con exclusión de .env)

**Carpetas obligatorias:**
- `android/`
- `ios/`
- `assets/env/`
- `lib/l10n/`
- `lib/config/`
- `doc/`

**Descarga de dependencias:**
```bash
flutter pub get
flutter pub upgrade --major-versions
```

**Verificación final:**
```bash
flutter analyze
```

> **Error Crítico:** Si falta cualquier archivo esencial o hay errores en `flutter analyze`, detén el flujo e informa específicamente qué elemento está ausente.

---

## 7. 📜 Reglas del Agente

> **📋 Template:** https://raw.githubusercontent.com/USUARIO/REPO/main/doc_templates/agent_rules.md

---

## 📋 Resumen de Configuración Exitosa (Checklist Final)

* [ ] **Identidad:** Nombre y Bundle ID aplicados.
* [ ] **Build System:** `generate: true` activo en `pubspec.yaml`.
* [ ] **IA Expert Skill:** Habilidades activas (`skill --name flutter-expert`).
* [ ] **Agente con Memoria:** `PROJECT_MEMORY.md` con reglas de calidad iniciales.
* [ ] **Arquitectura:** Carpeta `doc/` y Gestor de Estado configurados.
* [ ] **Configuración de Red:** `environment.dart` listo para manejar múltiples `.env`.
* [ ] **Entornos:** Archivos .env.debug, .env.profile, .env.release creados.
* [ ] **i18n:** Archivos .arb para es/en con generación automática.
* [ ] **Validación Estructural:** Todos los archivos y carpetas obligatorios presentes.
* [ ] **Dependencias:** `flutter pub get` y `flutter pub upgrade --major-versions` ejecutados.
* [ ] **Linter:** `flutter analyze` con 0 errores.

**ESTADO FINAL: READY_FOR_SPECIFICATION_DRIVEN_DEVELOPMENT = TRUE**

---

## 🎉 Mensaje Final Obligatorio del Agente

```
✅ PROYECTO CONFIGURADO EXITOSAMENTE

Entornos disponibles:
  • Debug (desarrollo)     → API: api-dev.example.com
  • Profile (preproducción) → API: api-staging.example.com  
  • Release (producción)   → API: api.example.com

Idiomas configurados:
  • Español (castellano) - Detectado automáticamente
  • Inglés (English) - Detectado automáticamente

El proyecto está listo para:
  • Desarrollo inmediato
  • Pruebas en múltiples entornos
  • Publicación con configuración específica por entorno
  • Ampliación de traducciones (añade nuevos archivos .arb)

Estoy preparado para continuar con:
  • Arquitectura avanzada (Bloc, Riverpod, etc.)
  • Implementación de pantallas
  • Gestión de estado
  • Integración con Firebase
  • Conexión con APIs
  • Implementación de CI/CD
```

---

## 8. 🚀 Flujo de Trabajo para Nuevas Features

> **⚡ NOTA:** Este proceso se ejecuta **automáticamente** cuando solicites crear una nueva feature. El agente seguirá este protocolo sin que tengas que recordarlo.

Cuando el usuario solicite crear una nueva feature, seguir este protocolo:

### A. Activación del Skill
```bash
skill --name flutter-expert
```
> El agente debe activar el skill de Flutter Expert al inicio de cada nueva tarea.

### B. Creación de Documentación de Feature

**1. Verificar ubicación y crear carpeta:**
```bash
# Obtener nombre de la carpeta actual (nombre del proyecto)
CURRENT_DIR=$(basename "$PWD")

# Si ya estamos en la carpeta del proyecto (contiene pubspec.yaml)
# crear directamente en doc/<nombre-feature>
# Si estamos en la raíz del workspace, usar <nombre-proyecto>/doc/<nombre-feature>

if [ -f "pubspec.yaml" ]; then
  mkdir -p doc/<nombre-feature>
  DOC_PATH="doc/<nombre-feature>"
else
  # Estructura con subcarpeta de proyecto
  mkdir -p <nombre-proyecto>/doc/<nombre-feature>
  DOC_PATH="<nombre-proyecto>/doc/<nombre-feature>"
fi
```

**2. Archivos obligatorios en `$DOC_PATH`:**

> **📋 Template:** https://raw.githubusercontent.com/USUARIO/REPO/main/doc_templates/feature_templates.md

*Crear archivos manualmente o copiar del template:*
- **SPEC.md** - Especificación
- **ARCHITECTURE.md** - Arquitectura
- **TODO.md** - Checklist

### C. Protocolo de Implementación

1. **SDD Update:** Actualizar `doc/<nombre-feature>/SPEC.md`
2. **Code:** Implementar código
3. **Gen:** Ejecutar `flutter pub get` y generadores necesarios
4. **Analyze:** `flutter analyze` (0 errores)
5. **Test:** Ejecutar tests
6. **Memory Log:** Registrar en `PROJECT_MEMORY.md`

---

### 🚀 ¿Iniciamos el motor?

Por favor, completa la ficha de la **Sección 1** para proceder.

1. **Nombre de la App:** 2.  **Bundle ID:** 3.  **Gestor de Estado:**
* [ ] **Riverpod**
* [ ] **Bloc / Cubit**
* [ ] **Híbrido (Pro)** (Riverpod + Bloc)