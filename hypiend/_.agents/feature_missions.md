# Agente Experto: Feature Missions

## Descripción
Soy el agente experto en la **feature de Misiones** de la aplicación hypiend-flutter. Gestiono el sistema de misiones con checklist de tareas, booster messages, selección/finalización de misiones, y reproducción de video (local y YouTube).

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/missions/missions_cubit.dart` (442 líneas) |
| **State** | `lib/ui/missions/missions_state.dart` |
| **Page** | `lib/ui/missions/missions_page.dart` |
| **Booster** | `lib/ui/missions/booster_messages_list_page.dart` |
| **Detail** | `lib/ui/missions/detail/missions_detail_page.dart` |
| **List** | `lib/ui/missions/list/missions_list_page.dart` |
| **Common** | `lib/ui/missions/common/` (3 archivos) |
| **Rutas** | `/missionsList`, `/missionsDetail` |

---

## Estado (`MissionsState`)

```dart
class MissionsState extends Equatable {
  final bool showMoreInfo;
  final MissionsContentType contentType; // boosterMessages | missions
  final bool enabledButton;
  final Map<String, bool> checkedQuestions; // checklist de la misión
  final bool loading;
  final CurrentMissionResponse? selectedMission;
  final RecommendationItem? selectedBoosterMessage;
  final List<Mission> missions;
  final List<RecommendationItem> boosterMessages;
  final File? image;
}
```

---

## Funciones del Cubit (`MissionsCubit`)

### Datos
| Función | Descripción |
|---------|-------------|
| `initState()` | Carga misiones, booster messages, misión actual, inicializa checklist |
| `getMissions()` | `dmGetMissions()` — misiones activas primero |
| `getBoosterMessages()` | `dmGetBoosterMessages()` — mensajes motivacionales |
| `getCurrentMission()` | `dmGetCurrentMission()` — misión seleccionada actual |
| `refresh()` | Recarga todo (misiones + boosters + misión actual) |

### Interacción con misiones
| Función | Descripción |
|---------|-------------|
| `onSelectedMissionClick(code, context)` | `dmPostSelectedMission()` → navega a `/missionsDetail` |
| `onFinishMissionClick(missionCode)` | `dmPostFinishMission()` → pop con resultado |
| `initializeCheckedQuestions()` | Inicializa mapa de checkbox vacío desde questionnaireDetail |
| `updateCheckedQuestion(literal, code, isChecked)` | Actualiza checklist + envía log como draft |
| `postQuestionnaireLog()` | Envía log de cuestionario de misión |

### Contenido multimedia
| Función | Descripción |
|---------|-------------|
| `loadTargetImage(recommendation)` | Descarga imagen de recomendación |
| `downloadFile(locale, qId, questionId)` | Descarga imagen de pregunta con autenticación |
| `getVideoController(videoUrl, context)` | Crea/reutiliza controlador de video (YouTube o normal) |

### UI
| Función | Descripción |
|---------|-------------|
| `onShowMoreInfoClick()` | Toggle info expandida |
| `onContentChanged(contentType)` | Switch entre misiones y booster messages |
| `getContent()` | Widget según contentType |

---

## Llamadas API

| Función API | Descripción |
|-------------|-------------|
| `dmGetMissions()` | GET lista de misiones |
| `dmGetCurrentMission()` | GET misión actual seleccionada |
| `dmGetBoosterMessages()` | GET booster messages |
| `dmPostSelectedMission(missionCode)` | POST seleccionar misión |
| `dmPostFinishMission(missionCode)` | POST finalizar misión |
| `dmPostQuestionnaireLog(...)` | POST log de checklist de misión (draft) |
| `dmGetContentFile(contentUuid, attachmentUuid)` | GET archivo/imagen de contenido |

---

## Flujo de negocio

1. **Init**: Carga misiones → booster messages → misión actual → inicializa checklist
2. **Estado checklist**: La misión tiene `questionnaireDetail` con sections/questions/constraints → se mapea a `Map<String, bool>`
3. **Actualizar checkbox**: Marca/desmarca → construye `MultipleSelectAnswer` → envía como draft con `dmPostQuestionnaireLog`
4. **Finalizar misión**: `dmPostFinishMission` → pop
5. **Seleccionar misión**: `dmPostSelectedMission` → `popAndPushNamed('/missionsDetail')`

---

## Gestión de video

- Caché de controladores en `Map<String, dynamic> _videoControllers`
- YouTube → `YoutubePlayerController` (detecta URLs youtube/youtu.be)
- Video normal → `VideoPlayerController.networkUrl()`
- Se disponen todos en `close()`

---

## Enum

```dart
enum MissionsContentType { boosterMessages, missions }
```

---

## Dependencias

- `video_player` — reproducción de video local
- `youtube_player_flutter` — reproducción de YouTube
- `path_provider` — directorio de documentos para descargas
