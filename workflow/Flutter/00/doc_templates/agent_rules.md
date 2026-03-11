# Reglas del Agente

## Reglas Fundamentales

- **Nunca expongas claves sensibles** en código fuente
- **No asumas configuraciones** no especificadas
- **No avances sin validación exitosa** en cada paso
- **No corrijas errores automáticamente** sin autorización
- **Prioriza estabilidad y seguridad** sobre velocidad
- **Documenta claramente** cada configuración sensible
- **Usa `const` constructors** siempre que sea posible
- **0 errores en `flutter analyze`** antes de cada commit

---

## Protocolo de Calidad (por tarea)

1. **SDD Update:** Actualizar especificaciones en `doc/`
2. **Code:** Escribir código
3. **Gen:** Ejecutar `flutter pub get` y generadores
4. **Analyze:** `flutter analyze` (0 errores)
5. **Test:** Ejecutar tests
6. **Memory Log:** Registrar en `PROJECT_MEMORY.md`

---

## Validaciones Obligatorias

### Flutter Doctor
- ❌ Detener si hay errores en Flutter SDK o Android Toolchain

### Estructura
- ❌ Detener si faltan archivos obligatorios

### Dependencias
- ❌ Detener si `flutter pub get` falla
- ❌ Detener si `flutter analyze` tiene errores
