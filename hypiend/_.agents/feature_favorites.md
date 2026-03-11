# Agente Experto: Feature Favorites

## Descripción
Soy el agente experto en la **feature de Favoritos** de la aplicación hypiend-flutter. Gestiono la lista de contenidos marcados como favoritos, su visualización y eliminación.

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/favorites/favorites_cubit.dart` (79 líneas) |
| **State** | `lib/ui/favorites/favorites_state.dart` |
| **Page** | `lib/ui/favorites/favorites_page.dart` |
| **Card** | `lib/ui/favorites/favorites_card.dart` |
| **Bubble** | `lib/ui/favorites/favorite_bubble_shape.dart` |
| **Back Button** | `lib/ui/favorites/carpediem_back_button.dart` |
| **Ruta** | `/favorites` |

---

## Estado (`FavoritesState`)

```dart
class FavoritesState extends Equatable {
  final bool loading;
  final List<Recommendation> favoriteItems; // Lista de favoritos (tipo Recommendation)
  final String favoriteLoadingCode;          // Código del item en proceso de eliminación
}
```

---

## Funciones del Cubit (`FavoritesCubit`)

| Función | Descripción |
|---------|-------------|
| `initState(context, {isRefresh})` | Carga favoritos con `dmGetFavorites()`. Si no es refresh → muestra loading |
| `onListItemClick(itemIndex)` | Navega según tipo: Standard→`/recommendation`, Soundscape→`/soundscape` |
| `onDeleteFavoriteConfirm(index)` | Elimina favorito: `dmPostRemoveFavorite(contentCode)` → recarga lista |

---

## Llamadas API

| Función API | Descripción |
|-------------|-------------|
| `dmGetFavorites()` | GET lista de favoritos del usuario |
| `dmPostRemoveFavorite(contentCode)` | POST eliminar favorito por código |

---

## Flujo de negocio

1. **Carga**: `dmGetFavorites()` → lista de `Recommendation` items
2. **Navegación**: Click en item → navega a `/recommendation` o `/soundscape` según tipo
3. **Eliminar**: Obtiene `detailId` y `contentCode` → `dmPostRemoveFavorite` → recarga lista
4. **Loading granular**: `favoriteLoadingCode` permite mostrar loading solo en el item que se elimina

---

## Navegación

- **Entrada**: Desde Profile o menú → `/favorites`
- **Salida a contenido**: `/recommendation` (Standard) o `/soundscape` (Soundscape)

---

## Notas

- `FavoritesCubit` recibe `BuildContext` en constructor (no ideal, se usa para diálogos)
- `onDeleteFavoriteConfirm` busca código por `detailId` usando `firstWhereOrNull`
- Tras eliminar recarga la lista completa con `initState(context, isRefresh: true)`
