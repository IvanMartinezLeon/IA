# Agente Experto: Feature Soundscape

## Descripción
Soy el agente experto en la **feature de Soundscape** (Paisaje sonoro) de la aplicación hypiend-flutter. Gestiono la reproducción de ambientes sonoros combinados con control de volumen individual, temporizador de sesión, favoritos, y onboarding/coachmarks.

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/soundscape/soundscape_cubit.dart` (270 líneas) |
| **State** | `lib/ui/soundscape/soundscape_state.dart` |
| **Page** | `lib/ui/soundscape/soundscape_page.dart` |
| **Audio Card** | `lib/ui/soundscape/carpediem_audio_card.dart` |
| **Sound Card** | `lib/ui/soundscape/carpediem_sound_card.dart` |
| **Bottom Sheet** | `lib/ui/soundscape/carpediem_bottom_sheet.dart` |
| **Slider** | `lib/ui/soundscape/carpediem_slider_soundscape.dart` |
| **Ruta** | `/soundscape` (recibe `Soundscape` task) |

---

## Estado (`SoundscapeState`)

```dart
class SoundscapeState extends Equatable {
  final Soundscape soundscape;        // Task de tipo Soundscape
  final bool favorite;                // ¿Es favorito?
  final bool favoriteLoading;
  final bool loading;
  final List<SoundscapeAmbient> ambients; // Lista de sonidos ambientales
  final List<int> streams;            // IDs de streams de audio
  final bool playing;                 // ¿Reproduciendo?
  final int currentTimeMillis;        // Tiempo actual de sesión en ms
  
  // Coachmark
  final bool showCoachmark;
  final bool coachmarkDescriptionAnimation;
  final int coachmarkStep; // 0-2
  final String coachmarkDescription;
}
```

---

## Funciones del Cubit (`SoundscapeCubit`)

### Audio
| Función | Descripción |
|---------|-------------|
| `initState(context)` | Carga estado favorito → obtiene ambients → inicia reproducción + timer |
| `resumeSounds()` | Reanuda reproducción y timer |
| `pauseSounds()` | Pausa reproducción y timer |
| `stopSounds()` | Detiene reproducción y timer |
| `setVolumes()` | Sincroniza volúmenes (placeholder, Soundpool comentado) |
| `onVolumeChanged(ambient, value)` | Cambia volumen individual [0,1] de un ambient |
| `onPlayPausePressed()` | Toggle play/pause |

### Sesión
| Función | Descripción |
|---------|-------------|
| `_startTimer()` | Timer cada 1000ms → incrementa `currentTimeMillis` → check fin de sesión |
| `onSessionEnd()` | Stop + postLog + reset timer |
| `onPageExit()` | Cancel timer + postLog silencioso |

### Favoritos y logs
| Función | Descripción |
|---------|-------------|
| `onFavoriteClick()` | Toggle favorito + postLog |
| `postLog(silentCall, favorite)` | `dmPostRecommendationLog()` con time spent |

### Onboarding
| Función | Descripción |
|---------|-------------|
| `onHelpClick()` | Inicia coachmark (3 pasos: 0-2) + pausa audio |
| `onCoachmarkClick(context)` | Avanza coachmark. Step 1 → muestra bottom sheet |

---

## Llamadas API

| Función API | Descripción |
|-------------|-------------|
| `dmGetSoundscapeAmbients(soundscape)` | GET lista de ambients con archivos de audio |
| `dmGetRecommendationLog(contentUuid)` | GET log previo (para recuperar estado favorito) |
| `dmPostRecommendationLog(contentUuid, eventList, favorite, timeSpentInSeconds)` | POST log de sesión |

---

## Flujo de negocio

1. **Entrada**: Recibe `Soundscape` task con `sessionDuration` (en ms)
2. **Init**: Recupera estado favorito → descarga ambients → inicia reproducción
3. **Sesión**: Timer incrementa `currentTimeMillis` cada 1s → al alcanzar `sessionDuration` → stop + log
4. **Volumen**: Cada ambient tiene `audioVolume` individual controlable [0, 1]
5. **Favorito**: Toggle → postLog con nuevo estado
6. **Salida**: `onPageExit()` → cancela timer + envía log silencioso

---

## Navegación

- **Entrada**: Desde Home → `/soundscape` con argumento `Soundscape` task
- **Salida**: Back button → `close()` → `onPageExit()`

---

## Audio (estado actual)

> ⚠️ **Soundpool comentado**: La implementación original usaba `soundpool` para reproducción simultánea de múltiples streams. Actualmente está **comentado** y reemplazado por placeholders (índices en lugar de stream IDs reales). El timer simula la reproducción.

---

## Dependencias

- ~~`soundpool`~~ (comentado, pero referenciado)
- Coachmark con `showCarpediemSoundscapeBottomSheet`
