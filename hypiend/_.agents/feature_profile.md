# Agente Experto: Feature Profile

## Descripción
Soy el agente experto en la **feature de Perfil** de la aplicación hypiend-flutter. Gestiono la visualización y edición del perfil de usuario, logout, desactivación de cuenta, integración con Fitbit, y versión de la app.

---

## Archivos clave

| Archivo | Ruta |
|---------|------|
| **Cubit** | `lib/ui/profile/profile_cubit.dart` (194 líneas) |
| **State** | `lib/ui/profile/profile_state.dart` |
| **Page** | `lib/ui/profile/profile_page.dart` |
| **Ruta** | `/profile` |

---

## Estado (`ProfileState`)

```dart
class ProfileState extends Equatable {
  final String name;
  final String surname;
  final String email;
  final bool loading;
  final bool saveButtonLoading;
  final bool disableUserLoading;
  final bool fitbitLoading;
  final FitbitDeviceStatus fitbitStatus; // connected | disconnected | unknown
  final String appVersion;
}
```

---

## Funciones del Cubit (`ProfileCubit`)

### Datos del usuario
| Función | Descripción |
|---------|-------------|
| `initState(context)` | Inicializa versión + obtiene usuario + refresca estado Fitbit |
| `getUser(context)` | `dmGetUser()` → rellena nombre, apellido, email + TextControllers |
| `onNameChange(value)` | Actualiza nombre |
| `onSurnameChange(value)` | Actualiza apellido |
| `onUpdateUser(context)` | `dmPutUser(firstName, lastName)` → actualiza perfil |
| `initVersion()` | `PackageInfo.fromPlatform()` → versión + buildNumber |

### Cuenta
| Función | Descripción |
|---------|-------------|
| `onLogoutClick()` | `dmLogout()` → navega a `/` (limpia stack) |
| `onDisableUser(context)` | `dmDisableUser()` → desactiva cuenta |

### Fitbit
| Función | Descripción |
|---------|-------------|
| `getDeviceStatus(context)` | `dmGetFitbitDeviceStatus()` → actualiza estado |
| `onFitbitDeviceClick(context)` | Switch según estado: connected→disconnect, disconnected→connect, unknown→refresh |
| `onConnectClick(context)` | `dmGetDeviceAuthorizationUrl()` → abre WebView para OAuth |
| `onDisconnectClick(context)` | Muestra diálogo de confirmación |
| `onFitbitDisconnectConfirmClick(context)` | `dmFitbitDisconnect()` → refresca estado |
| `refreshDeviceStatus(context)` | Refresca estado Fitbit con loading |

---

## Llamadas API

| Función API | Descripción |
|-------------|-------------|
| `dmGetUser()` | GET datos del usuario |
| `dmPutUser(firstName, lastName)` | PUT actualizar nombre/apellido |
| `dmLogout()` | POST logout (limpia tokens) |
| `dmDisableUser()` | POST desactivar cuenta |
| `dmGetFitbitDeviceStatus()` | GET estado de conexión Fitbit |
| `dmGetDeviceAuthorizationUrl()` | GET URL de autorización OAuth Fitbit |
| `dmFitbitDisconnect()` | POST desconectar Fitbit |

---

## Flujo de negocio

1. **Init**: Carga versión app + datos usuario + estado Fitbit
2. **Editar**: Campos editables nombre/apellido → guardar con `dmPutUser`
3. **Logout**: `dmLogout()` → limpia stack → vuelve a Login
4. **Desactivar**: `dmDisableUser()` — acción destructiva
5. **Fitbit connect**: Obtiene URL OAuth → abre WebView → al volver refresca estado
6. **Fitbit disconnect**: Diálogo confirmación → `dmFitbitDisconnect()`

---

## Navegación

- **Entrada**: Desde Home header o menú → `/profile`
- **Salidas**:
  - `/` (logout — limpia stack)
  - `/webview` (conexión Fitbit OAuth)
  - `/change_password` (desde page, no en cubit)

---

## Dependencias

- `package_info_plus` — versión de la app
- WebView para flujo OAuth Fitbit
