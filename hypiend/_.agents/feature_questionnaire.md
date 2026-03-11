# Agente Experto: Feature Questionnaire

## Descripción
Soy el agente experto en la **feature de Cuestionarios** de la aplicación hypiend-flutter. Gestiono la presentación y llenado de cuestionarios dinámicos con múltiples tipos de preguntas, dependencias entre preguntas, borradores (drafts), y envío de respuestas al backend.

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/questionnaire/questionnaire_cubit.dart` (635 líneas) |
| **State** | `lib/ui/questionnaire/questionnaire_state.dart` |
| **Page** | `lib/ui/questionnaire/questionnaire_page.dart` |
| **Content CD** | `lib/ui/questionnaire/carpediem_question_content.dart` |
| **Common** | `lib/ui/questionnaire/common/` (6 archivos) |
| **Ruta** | `/questionnaire` |

---

## Estado (`QuestionnaireState`)

```dart
class QuestionnaireState extends Equatable {
  final Questionnaire questionnaire;     // El cuestionario actual
  final int step;                        // Paso actual (0-indexed)
  final List<QuestionAnswer> answers;    // Lista de respuestas
  final List<QuestionnaireAnsweredQuestionnaire> answeredQuestionnaire; // Pre-respuestas
  final bool animateTransition;
  final double animationOffset;
  final bool success;                    // ¿Completado?
  final bool loading;
  final bool forceFinishText;            // Forzar texto de finalizar
  final bool descriptionAnimation;
}
```

---

## Tipos de preguntas soportados

| Tipo | Question class | Answer class |
|------|---------------|--------------|
| Selección múltiple | `MultipleSelectQuestion` | `MultipleSelectAnswer` |
| Selección única | `SingleSelectQuestion` | `SingleSelectAnswer` |
| Número entero | `SingleIntQuestion` | `SingleIntAnswer` |
| Número decimal | `SingleFloatQuestion` | `SingleFloatAnswer` |
| Texto libre | `SingleStringQuestion` | `SingleStringAnswer` |
| Slider | `SliderQuestion` | `SliderAnswer` |
| Fecha | `SingleDateQuestion` | `SingleDateAnswer` |

---

## Funciones del Cubit (`QuestionnaireCubit`)

### Inicialización
| Función | Descripción |
|---------|-------------|
| `initState(answeredQuestionnaires)` | Crea respuestas por defecto, inicializa TextControllers, marca answered |
| `fetchLogs()` | Recupera logs previos (borradores) con `dmGetLog()` y pre-rellena respuestas |
| `createDefaultResponse(question, [answered], [logAnswers])` | Crea respuesta por defecto según tipo, puede pre-rellenar desde QA o logs |

### Navegación de preguntas
| Función | Descripción |
|---------|-------------|
| `onAnswerChanged(step, answer)` | Cambia respuesta, limpia dependientes, auto-avanza en selección única |
| `onNextButtonPressed(context, isAnswered, loadFile)` | Avanza: valida formato, calcula siguiente paso, o envía si es último |
| `onPreviousButtonPressed(loadFile)` | Retrocede respetando dependencias |
| `getNextStep(step, questionAnswer)` | Calcula siguiente paso válido según dependencias |
| `getPreviousStep(step)` | Calcula paso anterior válido según dependencias |
| `getAnswerValueForAccept(answer)` | Extrae valor de respuesta para comparar con dependencias |

### Sistema de dependencias
Las preguntas pueden tener `dependency` con:
- `dependsOnCode`: código de la pregunta de la que depende
- `acceptValues`: valores aceptados para mostrar esta pregunta

Si una pregunta depende de otra y el valor no coincide, se salta automáticamente.

### Envío y borradores
| Función | Descripción |
|---------|-------------|
| `sendQuestionnaireLog(context, {isDraft})` | Envía respuestas con `dmPostQuestionnaireLog()` |
| `_updateAllAnswersStatus(answers)` | Actualiza `answered` status de todas las respuestas antes de enviar |

### Validación
| Función | Descripción |
|---------|-------------|
| `answerListCheckCurrentAnswered()` | Marca respuesta actual como answered si tiene valor |
| `answerUncheckAnswered()` | Desmarca answered del paso actual |

### Descarga de archivos
| Función | Descripción |
|---------|-------------|
| `downloadFile(locale, questionnaireId, questionId)` | Descarga imagen de pregunta con autenticación manual via `HttpClient` |

---

## Llamadas API

| Función API | Descripción |
|-------------|-------------|
| `dmPostQuestionnaireLog(draft, contentUuid, version, answerLocalQuestionList, eventList)` | POST respuestas del cuestionario |
| `dmGetLog(contentUuid)` | GET logs previos (para recuperar borradores) |
| Descarga directa | `GET /questionnaire/{id}/question/{id}/imageI18N` con `x-access-token` |

---

## Flujo de negocio

1. **Entrada**: Recibe `Questionnaire` task + optional `answeredQuestionnaire` (pre-respuestas)
2. **Init**: Crea respuestas por defecto → si no hay pre-respuestas, busca borradores con `fetchLogs()`
3. **Navegación**: PageView controlado por `PageController` con `jumpToPage()`
4. **Dependencias**: Al cambiar respuesta → limpia respuestas dependientes posteriores
5. **Auto-avance**: Selección única avanza automáticamente tras 300ms delay
6. **Validación**: Int/Float → valida formato. No bloquea avance si no contestado
7. **Envío**: `sendQuestionnaireLog()` → éxito → `state.success = true`
8. **Borrador**: `sendQuestionnaireLog(isDraft: true)` → pop con resultado `true`
9. **Salida**: Diálogo de confirmación si no completado, con opción de cancelar

---

## Navegación

- **Entrada**: Desde Home → `/questionnaire` con argumentos `[Questionnaire, List<QuestionnaireAnsweredQuestionnaire>?, bool?]`
- **Salida éxito**: `Modular.to.pop()` o `Modular.to.pop(true)` (para draft)
- **Salida cancelar**: Diálogo → `popUntil(ModalRoute.withName('/home'))`

---

## Notas importantes

- Los `TextEditingController` se generan dinámicamente según cantidad de preguntas
- El `PageController` se dispone en `close()`
- La descarga de imágenes usa `HttpClient` directo (no Dio) con headers manuales
- El borrador envía la respuesta actual del paso, `_updateAllAnswersStatus` revisa todas
- `forceFinishText` se activa cuando el siguiente paso calculado está fuera de rango
- Las respuestas pre-rellenadas vienen de dos fuentes: `answeredQuestionnaire` (API) o `logAnswers` (borradores locales)
