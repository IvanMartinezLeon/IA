# Agente Experto: Feature Login

## Descripción
Soy el agente experto en la **feature de Login** de la aplicación hypiend-flutter. Gestiono la autenticación de usuarios, validación de credenciales, manejo de sesiones expiradas y autofill de credenciales.

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/login/login_cubit.dart` |
| **State** | `lib/ui/login/login_state.dart` |
| **Page** | `lib/ui/login/login_page.dart` |
| **Ruta** | `/` (ruta raíz en `app_module.dart`) |

---

## Estado (`LoginState`)

```dart
class LoginState extends Equatable {
  final String email;
  final String password;
  final bool showPassword;
  final bool loginLoading;
}
```

- `email` / `password`: credenciales del usuario
- `showPassword`: toggle de visibilidad de contraseña
- `loginLoading`: indica si el login está en progreso

---

## Funciones del Cubit (`LoginCubit`)

| Función | Descripción |
|---------|-------------|
| `onEmailChange(value)` | Actualiza el email en el state |
| `onPasswordChange(value)` | Actualiza la password en el state |
| `toggleShowPassword()` | Alterna visibilidad de la contraseña |
| `onLoginClick(context)` | Ejecuta login llamando a `dmLogin()` |
| `initState(context)` | Inicializa: muestra diálogo si sesión expirada, carga último email |
| `onRegisterClick()` | Navega a `/register` |
| `autoCompleteDebugUser()` | Autocompleta credenciales de debug desde `M.flavor` |

---

## Llamadas API

| Función API | Origen | Descripción |
|-------------|--------|-------------|
| `dmLogin(username, password)` | `api_caller.dart` | POST login con credenciales |
| `setLastLoginEmail(email)` | `carpediem_preferences.dart` | Guarda el email para próximos logins |
| `getLastLoginEmail()` | `carpediem_preferences.dart` | Recupera último email usado |

---

## Flujo de negocio

1. **Inicialización**: Si `sessionExpired == true`, muestra diálogo informativo. Carga último email usado.
2. **Login**: Llama `dmLogin()`. Si éxito → guarda email localmente, finaliza autofill context, navega a `/home`. Si error → muestra diálogo con `error_bad_credentials`.
3. **Registro**: Navega a `/register` con `Modular.to.pushNamed`.
4. **Debug**: En modo debug se puede autocompletar con `M.flavor.debugUsername/debugPassword`.

---

## Navegación

- **Entrada**: Ruta raíz `/` — es la pantalla inicial de la app
- **Salida éxito**: `pushNamedAndRemoveUntil("/home", (_) => false)` — limpia stack y va a Home
- **Salida registro**: `pushNamed('/register')`

---

## Dependencias

- `flutter_bloc` (Cubit pattern)
- `equatable` (comparación de states)
- `flutter_modular` (navegación)
- `flutter_gen` (localización i18n)
- `TextInput.finishAutofillContext` (guardar credenciales del sistema)

---

## Notas importantes

- El `LoginCubit` recibe `sessionExpired` como parámetro del constructor
- Se usa `TextInput.finishAutofillContext(shouldSave: true)` para guardar credenciales en el sistema de autofill
- El manejo de errores incluye catch genérico con diálogo para excepciones inesperadas
- Los `TextEditingController` para email y password se mantienen en el Cubit
