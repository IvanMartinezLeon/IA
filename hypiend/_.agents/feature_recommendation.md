# Agente Experto: Feature Recommendation

## Descripción
Soy el agente experto en la **feature de Recomendaciones** de la aplicación hypiend-flutter. Gestiono la visualización de contenido recomendado (tipo Standard), interacciones de like/dislike, favoritos, y logs de engagement.

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/recommendation/recommendation_cubit.dart` (117 líneas) |
| **State** | `lib/ui/recommendation/recommendation_state.dart` |
| **Page** | `lib/ui/recommendation/recommendation_page.dart` |
| **Button** | `lib/ui/recommendation/recommendations_button.dart` |
| **Ruta** | `/recommendation` (recibe `Standard` task) |

---

## Estado (`RecommendationState`)

```dart
class RecommendationState extends Equatable {
  final Standard recommendationStandard; // Task tipo Standard
  final bool loading;
  final bool loadingLike;
  final bool loadingDisLike;
  final bool loadingFavorite;
  final LikeStateType like; // undefined | like | dislike
  final bool favorite;
  final double second;
}
```

---

## Enum `LikeStateType`

```dart
enum LikeStateType { undefined, like, dislike }
```

---

## Funciones del Cubit (`RecommendationCubit`)

| Función | Descripción |
|---------|-------------|
| `initState(context)` | Recupera log previo (favorito) + envía log silencioso inicial |
| `onLike()` | Toggle like: si ya liked → uncheck, si no → set like |
| `onDislike()` | Toggle dislike: si ya disliked → uncheck, si no → set dislike |
| `onFavorite()` | Toggle favorito + diálogo de éxito si se añade |
| `postLog(favorite, like, uncheckLike, uncheckDislike, silentCall)` | `dmPostRecommendationLog()` — log central de interacción |

---

## Llamadas API

| Función API | Descripción |
|-------------|-------------|
| `dmGetRecommendationLog(contentUuid)` | GET log previo (estado favorito) |
| `dmPostRecommendationLog(contentUuid, eventList, favorite, like, uncheckLike, uncheckDislike)` | POST log de interacción |

---

## Flujo de negocio

1. **Init**: Si `id != -1` → recupera log previo para estado favorito → envía log silencioso (tracking de visualización)
2. **Like/Dislike**: Toggle — si ya estaba activo → uncheck, si no → set. Son mutuamente excluyentes
3. **Favorito**: Toggle + si se añade → diálogo de éxito con icono `favoritos_empty`
4. **PostLog**: Centraliza toda la comunicación con el backend para registrar interacciones

---

## Navegación

- **Entrada**: Desde Home → `/recommendation` con argumento `Standard` task
- **Salida**: Back button estándar

---

## Notas importantes

- Cada interacción (like/dislike/favorite) tiene su propio loading state independiente
- `postLog` devuelve `bool` indicando éxito
- El diálogo de favorito añadido solo se muestra si `state.favorite == true` después del toggle
- Los `uncheckLike`/`uncheckDislike` permiten deshacer la acción anterior
