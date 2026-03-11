# Agente Experto: Feature Categories

## Descripción
Soy el agente experto en la **feature de Categorías** de la aplicación hypiend-flutter. Gestiono la selección de categorías de bienestar y comportamientos objetivo (target behaviours), favoritos, carga de imágenes, y logs de interacción con el contenido.

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/categories/categories_cubit.dart` (321 líneas) |
| **State** | `lib/ui/categories/categories_state.dart` |
| **List** | `lib/ui/categories/categories_list.dart` |
| **Pool** | `lib/ui/categories/categories_pool.dart` |
| **Common** | `lib/ui/categories/common/` (1 archivo) |
| **Ruta** | Tab en Home + `/categoriesList` |

---

## Estado (`CategoriesState`)

```dart
class CategoriesState extends Equatable {
  final CategoriesContentType content; // list | pool
  final List<CarpediemCategory> categories;
  final List<Behavior>? behaviors;
  final CarpediemCategory? selectedCategory;
  final Behavior? selectedBehavior;
  final bool isLoading;
  final bool favorite;
}
```

---

## Funciones del Cubit (`CategoriesCubit`)

### Datos
| Función | Descripción |
|---------|-------------|
| `init()` → `fetchData()` | Preload imágenes + getSelectedCategory + getFavorites + getCategories + getSelectedBehaviour + getTargetBehavior |
| `getCategories()` | `dmGetCategories()` — reordena: seleccionada primero |
| `getSelectedCategory()` | `dmGetCurrentCategory()` — categoría activa |
| `getSelectedBehaviour()` | `dmGetCurrentBehavior()` — comportamiento activo |
| `getTargetBehavior()` | `dmGetBehaviors()` — lista de comportamientos, seleccionado primero |
| `getFavorites()` | `dmGetFavorites()` — check si categoría actual es favorita |

### Interacción
| Función | Descripción |
|---------|-------------|
| `postSelectedTargetBehavior(code)` | `dmPostSelectBehaviour()` → refresca seleccionado |
| `onFavoriteClick()` | Toggle favorito → `dmPostRemoveFavorite()` o `postLog(favorite: true)` |
| `postLog(favorite, like, uncheckLike, uncheckDislike, silentCall)` | `dmPostRecommendationLog()` para la categoría |
| `onCategorySelected(content)` | Cambia tipo de contenido (list/pool) |
| `changeContent(content)` | Cambia contenido con fallback inteligente |

### Multimedia
| Función | Descripción |
|---------|-------------|
| `loadCategoryImage(category)` | Descarga imagen de categoría via `dmGetContentFile()` |
| `loadTargetImage(behavior)` | Descarga imagen de comportamiento |
| `preloadImages(state)` | Precarga todas las imágenes de categorías y behaviors |

---

## Llamadas API

| Función API | Descripción |
|-------------|-------------|
| `dmGetCategories()` | GET lista de categorías |
| `dmGetCurrentCategory()` | GET categoría seleccionada |
| `dmGetCurrentBehavior()` | GET comportamiento seleccionado |
| `dmGetBehaviors()` | GET lista de comportamientos |
| `dmGetFavorites()` | GET lista de favoritos |
| `dmPostSelectBehaviour(behaviourCode)` | POST seleccionar comportamiento |
| `dmPostRecommendationLog(contentUuid, eventList, favorite, like, ...)` | POST log de interacción |
| `dmPostRemoveFavorite(contentCode)` | POST eliminar favorito |
| `dmGetContentFile(contentUuid, attachmentUuid)` | GET archivo multimedia |

---

## Flujo de negocio

1. **Init**: Precarga imágenes → obtiene categoría seleccionada → favoritos → categorías → comportamiento → target behaviors
2. **Visualización**: Dos vistas: `list` (lista de categorías) y `pool` (detalle de categoría con behaviors)
3. **Selección behavior**: `postSelectedTargetBehavior` → refresca behavior activo
4. **Favoritos**: Toggle → si elimina: `dmPostRemoveFavorite`, si añade: `postLog(favorite: true)`
5. **Log**: Registra likes/dislikes/favoritos para analytics via `dmPostRecommendationLog`

---

## Lifecycle

- `StreamController<String>` para comunicación interna (actualmente test data)
- `close()`: cierra StreamController + super.close()
- `PageController` para swipe entre categorías y behaviors (viewport fractions: 0.7 y 0.48)

---

## Enums

```dart
enum CategoriesContentType { list, pool }
```
