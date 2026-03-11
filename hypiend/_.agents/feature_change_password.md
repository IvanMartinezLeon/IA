# Agente Experto: Feature Change Password

## Descripción
Soy el agente experto en la **feature de Cambio de Contraseña** de la aplicación hypiend-flutter. Gestiono la validación y envío de cambio de contraseña con verificación de contraseña antigua.

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/change_password/change_password_cubit.dart` (93 líneas) |
| **State** | `lib/ui/change_password/change_password_state.dart` |
| **Page** | `lib/ui/change_password/change_password_page.dart` |
| **Ruta** | `/change_password` |

---

## Estado (`ChangePasswordState`)

```dart
class ChangePasswordState extends Equatable {
  final String oldPassword;
  final String newPassword;
  final String confirmPassword;
  final bool changePasswordLoading;
}
```

---

## Funciones del Cubit

| Función | Descripción |
|---------|-------------|
| `onOldPasswordChange(value)` | Actualiza contraseña antigua |
| `onNewPasswordChange(value)` | Actualiza nueva contraseña |
| `onConfirmPasswordChange(value)` | Actualiza confirmación |
| `onChangePasswordClick(context)` | Valida + envía cambio → devuelve `bool` éxito |

---

## Validaciones

1. `newPassword` no vacía Y `newPassword == confirmPassword` → si no: `error_match_password`
2. `newPassword != oldPassword` → si son iguales: `error_incorrect_password`
3. API call → si `result.data.ok == true` → éxito, si no: `error_incorrect_password`

---

## Llamadas API

| Función API | Descripción |
|-------------|-------------|
| `dmPostPassword(oldPassword, newPassword)` | POST cambio de contraseña |

---

## Flujo de negocio

1. Rellenar: contraseña antigua, nueva, confirmación
2. Validación local: coincidencia + no igual a antigua
3. `dmPostPassword()` → éxito: limpia inputs + diálogo éxito, error: diálogo error
4. Devuelve `bool success` al caller

---

## TextEditingControllers

- `passwordTextController`
- `newPasswordTextController`
- `confirmNewPasswordTextController`

Se limpian tras cambio exitoso.
