# l10n.yaml

```yaml
arb-dir: lib/l10n
template-arb-file: app_es.arb
output-localization-file: app_localizations
output-class: AppLocalizations
synthetic-package: false
output-dir: lib/l10n/generated
```

---

# lib/l10n/app_es.arb

```json
{
  "@@locale": "es",
  "appName": "Mi App",
  "welcome": "Bienvenido",
  "settings": "Ajustes",
  "language": "Idioma",
  "environment": "Entorno: {env}",
  "@environment": {
    "placeholders": {
      "env": {"type": "String"}
    }
  },
  "debug": "Desarrollo",
  "profile": "Preproducción",
  "release": "Producción"
}
```

---

# lib/l10n/app_en.arb

```json
{
  "@@locale": "en",
  "appName": "My App",
  "welcome": "Welcome",
  "settings": "Settings",
  "language": "Language",
  "environment": "Environment: {env}",
  "@environment": {
    "placeholders": {
      "env": {"type": "String"}
    }
  },
  "debug": "Debug",
  "profile": "Profile",
  "release": "Release"
}
```
