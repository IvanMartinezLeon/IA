# Agente Experto: Feature Chat

## Descripción
Soy el agente experto en la **feature de Chat** (Foros) de la aplicación hypiend-flutter. Gestiono la comunicación entre participantes en foros, con polling periódico de mensajes, envío de mensajes, y visualización en tiempo semi-real.

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/chat/chat_cubit.dart` (159 líneas) |
| **State** | `lib/ui/chat/chat_state.dart` |
| **Page** | `lib/ui/chat/chat_page.dart` |
| **Component** | `lib/ui/chat/chat_component.dart` |
| **Message** | `lib/ui/chat/message_component.dart` |
| **Ruta** | `/chat` (recibe `forumId`) |

---

## Estado (`ChatState`)

```dart
class ChatState extends Equatable {
  final ForumDetail? forum;          // Detalles del foro (participantes)
  final bool isLoading;
  final bool isKeyboardVisible;
  final ScrollController? scrollController;
  final List<ForumMessage>? messages; // Lista de mensajes
  final String? currentUserUuid;     // UUID del usuario actual
  final String currentMessage;       // Mensaje en composición
  final List<Forum> forums;
}
```

---

## Funciones del Cubit (`ChatCubit`)

| Función | Descripción |
|---------|-------------|
| `initState()` | Carga foro, mensajes, UUID actual, inicia polling |
| `getForumDetail()` | `dmGetForumDetail(forumId)` — detalles del foro + participantes |
| `getForumMessages()` | `dmGetForumMessages(forumId)` — lista de mensajes |
| `getCurrentUserUuid()` | `getLocalUserUuid()` — UUID local del usuario autenticado |
| `startPolling()` | Timer cada 5 segundos → `_fetchNewMessages()` |
| `sendMessage(context)` | `dmPostForumMessage(forumId, message)` → refresca mensajes + limpia input |
| `onWriteTextChanged(text)` | Actualiza `currentMessage` en state |
| `getFormattedTime(date)` | Parsea fecha ISO → formato "HH:mm" |
| `getUsernameMessage(uuid)` | Busca nombre de participante por UUID (null si es usuario actual) |

---

## Llamadas API

| Función API | Descripción |
|-------------|-------------|
| `dmGetForumDetail(forumId)` | GET detalles del foro con participantes |
| `dmGetForumMessages(forumId)` | GET mensajes del foro |
| `dmPostForumMessage(forumId, message)` | POST nuevo mensaje al foro |
| `getLocalUserUuid()` | GET UUID del usuario desde preferences locales |

---

## Flujo de negocio

1. **Init**: Carga detalle del foro + mensajes + UUID usuario → inicia polling
2. **Polling**: Cada 5 segundos → recarga mensajes (semi-real-time)
3. **Enviar**: Valida no vacío → `dmPostForumMessage` → éxito → recarga mensajes, limpia controller
4. **Identificación**: Compara UUID de mensaje con UUID local para distinguir "mis mensajes" vs "otros"
5. **Nombres**: `getUsernameMessage` busca en `forum.participants` por UUID

---

## Lifecycle

- `Timer? _timer` — polling cada 5s, se cancela en `close()`
- `ScrollController` — scroll al final de mensajes en post-frame callback
- `KeyboardVisibilityController` — escucha cambios de teclado para ajustar UI
- `TextEditingController messageController` — controlador del input de texto

---

## Navegación

- **Entrada**: Desde Home → `/chat` con argumento `[forumId]`
- **Salida**: Back button estándar

---

## Dependencias

- `flutter_keyboard_visibility` — detección de teclado
- `intl` — formateo de fechas
