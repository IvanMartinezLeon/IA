# Agente Experto: Feature Home

## Descripción
Soy el agente experto en la **feature Home** de la aplicación hypiend-flutter. Home es el **hub principal** de la app tras login. Gestiona la pantalla principal con burbuja animada, contenido dinámico por pestañas (home, cuestionarios, misiones, categorías, chat), onboarding/coachmarks, y coordinación con múltiples features.

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/home/home_cubit.dart` (737 líneas) |
| **State** | `lib/ui/home/home_state.dart` (257 líneas) |
| **Page** | `lib/ui/home/home_page.dart` |
| **Bubble** | `lib/ui/home/carpediem_home_bubble_shape.dart` |
| **Content** | `lib/ui/home/content/` (home, chat, categories, questionnaires, missions) |
| **Rutas** | `/home`, `/homeArguments` |

---

## Estado (`HomeState`)

```dart
class HomeState extends Equatable {
  // Datos principales
  final bool loading;
  final String category, title, description;
  final Task? task;
  final PillarFakeTask? pillar;
  final HomeContent currentContent; // enum: home, questionnaire, missions, categories, chat
  
  // Datos de features
  final List<RemoteRecord> questionnaires;
  final List<NewQuestionnaire> newQuestionnaires;
  final List<CarpediemCategory> categories;
  final List<Forum> forums;
  final List<Mission> missions;
  final List<RecommendationItem>? boosterMessages;
  final bool unreadMessages;
  final LocalUser? user;
  
  // Animaciones
  final bool categoryAnimation, titleAnimation, descriptionAnimation, lottieParlant;
  final bool cardClickRefreshHome;
  
  // Coachmark (onboarding)
  final bool showCoachmark;
  final int coachmarkStep; // 0-4
  final String coachmarkDescription;
  // + más flags de animación coachmark
}
```

---

## Funciones del Cubit (`HomeCubit`)

### Inicialización y datos
| Función | Descripción |
|---------|-------------|
| `initState({taskCode, currentContent})` | Inicialización principal: onboarding, cuestionarios, usuario, foros, datos home |
| `_reloadHomeData(taskCode, currentContent)` | Recarga datos home: foros, cuestionarios, `dmGetHome()` |
| `getUser()` | Obtiene datos del usuario logueado |
| `getQuestionnaires()` | Obtiene cuestionarios desde `dmGetQuestionnaires()` |
| `getNewQuestionnaires()` | Obtiene lista de cuestionarios desde `dmGetQuestionnaireList()` |
| `getCategories()` | Obtiene categorías desde `dmGetCategories()` |
| `getMissions()` | Obtiene misiones (activas primero) desde `dmGetMissions()` |
| `getBoosterMessages()` | Obtiene booster messages desde `dmGetBoosterMessages()` |
| `getAllForums()` | Obtiene y ordena foros por última fecha de mensaje |
| `fetchQuestionnaires()` | Reload combinado de cuestionarios + home data |

### Interacción con foros
| Función | Descripción |
|---------|-------------|
| `getLastMessage(forumId, date)` | Obtiene texto de último mensaje de un foro |
| `getUsernameForDate(forumId, date)` | Obtiene nombre de usuario por UUID en un foro |

### Selección y navegación
| Función | Descripción |
|---------|-------------|
| `postSelectedCategory(categoryCode, context)` | Selecciona categoría → `dmPostSelectedCategory()` |
| `getSelectedQuestionnaire(record, answeredQuestions, answered)` | Obtiene detalle de task → navega a la pantalla apropiada |
| `navigateToTask(task, answeredQuestions, answered)` | Navega según tipo de task: Standard→recommendation, Soundscape→soundscape, Questionnaire→questionnaire |
| `onHomeBubbleTap(context)` | Tap en burbuja principal → refresca o navega al task |
| `onContentChanged(content, beforeContent)` | Cambia tab del home (recarga datos si vuelve a home) |
| `getCurrentContent(content)` | Devuelve Widget según tab activo |

### Onboarding/Coachmark
| Función | Descripción |
|---------|-------------|
| `onHelpClick()` | Inicia flujo de coachmark (5 pasos: 0-4) |
| `onCoachmarkClick()` | Avanza paso de coachmark según `coachmarkStep` |
| Animaciones callbacks | `onCategoryAnimationFinished()`, `onTitleAnimationFinished()`, etc. |

---

## Llamadas API

| Función API | Descripción |
|-------------|-------------|
| `dmGetHome(requestedTaskCode)` | Principal: obtiene pillar + task + navigación automática |
| `dmGetQuestionnaires()` | Obtiene records de cuestionarios |
| `dmGetQuestionnaireList()` | Obtiene lista de cuestionarios nuevos |
| `dmGetCategories()` | Obtiene categorías disponibles |
| `dmGetMissions()` | Obtiene misiones del usuario |
| `dmGetBoosterMessages()` | Obtiene mensajes booster/recomendaciones |
| `dmGetForums()` | Obtiene foros de chat |
| `dmGetForumMessages(forumId)` | Obtiene mensajes de un foro |
| `dmGetForumDetail(forumId)` | Obtiene detalles de un foro (participantes) |
| `dmGetTaskDetail(priorityTaskId, priorityTaskEventList)` | Obtiene detalle de una tarea |
| `dmPostSelectedCategory(categoryCode)` | Selecciona una categoría |
| `dmGetUser()` | Obtiene datos del usuario |

---

## Flujo de negocio principal

1. **Init**: Comprueba onboarding local → si no completado, inicia coachmark
2. **Carga datos**: getQuestionnaires → getUser → escucha notificaciones push → `_reloadHomeData`
3. **`_reloadHomeData`**: Obtiene foros + cuestionarios + `dmGetHome()`:
   - `result.first` → Pillar (tipo de pilar actual)
   - `result.second` → Task actual (puede ser Standard, Soundscape, Questionnaire)
   - `result.third` → Auto-navegación a task
4. **Contenido burbuja**: Muestra category + title + description según task/pillar/misión o empty state
5. **Tabs**: 5 pestañas: Home, Questionnaires, Missions, Categories, Chat
6. **Navegación por task type**: Standard→`/recommendation`, Soundscape→`/soundscape`, Questionnaire→`/questionnaire`

---

## Navegación

- **Entrada**: Desde Login éxito → `/home`
- **Salidas**:
  - `/recommendation` (Task tipo Standard)
  - `/soundscape` (Task tipo Soundscape)
  - `/questionnaire` (Task tipo Questionnaire, con argumentos: task, answeredQuestions, answered)
  - `/profile` (desde header)

---

## Lifecycle

- `Timer? _refreshTimer` — timer periódico (comentado actualmente)
- `bool _isClosed` — flag manual para prevenir emits tras close
- `close()` — cancela timer y llama super.close()
- Escucha `LocalNotificationService.instance.onMessageStream` para unread messages

---

## Enum `HomeContent`

```dart
enum HomeContent { questionnaire, chat, missions, categories, home }
```

---

## Notas importantes

- Home es el **cubit más grande** de la app (737 líneas)
- Coordina datos de múltiples features: categorías, misiones, cuestionarios, foros, booster messages
- El coachmark tiene 5 pasos (0-4) con animaciones controladas por state
- El task puede ser null → muestra empty state con pillar o misiones o mensaje vacío
- `_reloadHomeData` es el método clave para refrescar todo el contenido
- El flavor (Become/Hypiend/MetaSleep) afecta al coachmark step 2→3
