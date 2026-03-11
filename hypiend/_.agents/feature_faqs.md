# Agente Experto: Feature FAQs

## Descripción
Soy el agente experto en la **feature de FAQs** (Preguntas Frecuentes) de la aplicación hypiend-flutter. Gestiono la carga y visualización de FAQs con expansión/colapso tipo acordeón.

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/faqs/cubit/faqs_cubit.dart` (36 líneas) |
| **State** | `lib/ui/faqs/cubit/faqs_state.dart` |
| **Page** | `lib/ui/faqs/faqs_page.dart` |
| **Ruta** | `/faqs` |

---

## Estado (`FaqsState`)

```dart
class FaqsState extends Equatable {
  final List<FaqRecord> faqs;
  final bool isLoading;
  final int expandedIndex; // -1 = ninguno expandido
}
```

---

## Funciones del Cubit

| Función | Descripción |
|---------|-------------|
| `initState()` | `dmGetFaqs()` → carga FAQs + expandedIndex = -1 |
| `toggleExpansion(index)` | Si ya expandido → colapsa (-1), si no → expande |

---

## Llamadas API

| Función API | Descripción |
|-------------|-------------|
| `dmGetFaqs()` | GET lista de FAQs desde `GetFaqsResponse` |

---

## Flujo de negocio

1. Init → carga FAQs del backend
2. Visualización tipo acordeón: solo un FAQ expandido a la vez
3. Toggle: click en FAQ expandido → colapsa, click en otro → expande

---

## Navegación

- **Entrada**: Desde Profile o menú → `/faqs`
- **Salida**: Back button estándar

---

## Notas

- Feature muy simple (36 líneas de cubit)
- Usa `FaqRecord` como modelo de datos
- Solo un FAQ expandido a la vez (reemplaza con `FaqsState` nuevo, no copyWith)
