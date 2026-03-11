# Agente Experto: Feature Register

## Descripción
Soy el agente experto en la **feature de Registro** de la aplicación hypiend-flutter. Gestiono el alta de nuevos usuarios con validación de formularios y comunicación con el backend.

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/register/register_cubit.dart` |
| **State** | `lib/ui/register/register_state.dart` |
| **Page** | `lib/ui/register/register_page.dart` |
| **Ruta** | `/register` |

---

## Estado (`RegisterState`)

```dart
class RegisterState extends Equatable {
  final String name;
  final String surname;
  final String email;
  final String password;
  final String confirmPassword;
  final bool registeruserLoading;
  final bool isSwitchOn;
}
```

- `name` / `surname`: datos personales
- `email` / `password` / `confirmPassword`: credenciales
- `registeruserLoading`: indicador de carga durante registro
- `isSwitchOn`: toggle de aceptación de términos

---

## Funciones del Cubit (`RegisterCubit`)

| Función | Descripción |
|---------|-------------|
| `onNameChange(value)` | Actualiza nombre |
| `onSurnameChange(value)` | Actualiza apellido |
| `onEmailChange(value)` | Actualiza email |
| `onCodeChange(value)` | Actualiza código (campo adicional) |
| `onPasswordChange(value)` | Actualiza contraseña |
| `onConfirmPasswordChange(value)` | Actualiza confirmación de contraseña |
| `toggleSwitch()` | Toggle de términos y condiciones |
| `onRegisterClick(context)` | Ejecuta registro llamando a `dmRegisterUser()` |

---

## Llamadas API

| Función API | Descripción |
|-------------|-------------|
| `dmRegisterUser(email, firstName, lastName, password)` | POST registro de usuario nuevo |

---

## Flujo de negocio

1. El usuario rellena: nombre, apellido, email, contraseña, confirmación
2. Acepta términos con toggle
3. Al pulsar "Registrar" → `dmRegisterUser()`
4. **Éxito**: Muestra diálogo de éxito (`register_success_message`), al cerrar → `Navigator.pop()` + `Modular.to.pop()` (vuelve a Login)
5. **Error**: Muestra diálogo con `result.data.message`

---

## Navegación

- **Entrada**: Desde Login → `/register`
- **Salida éxito**: `pop()` doble — cierra diálogo y vuelve a Login
- **Salida cancelar**: Back button estándar

---

## Bug conocido

> En `copyWithProps`, el campo `registeruserLoading` NO se pasa al nuevo `RegisterState`. Esto significa que el loading state no se propaga correctamente. El campo `code` en `copyWithProps` existe como parámetro pero no se asigna.

---

## Dependencias

- `flutter_bloc`, `equatable`, `flutter_modular`, `flutter_gen`
