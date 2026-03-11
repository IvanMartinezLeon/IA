# Environment Templates

## .env.debug
```env
ENVIRONMENT=debug
API_URL=https://api-dev.example.com
LOG_LEVEL=debug
FEATURE_FLAGS=true
APP_NAME="Mi App (DEV)"
```

## .env.profile
```env
ENVIRONMENT=profile
API_URL=https://api-staging.example.com
LOG_LEVEL=info
FEATURE_FLAGS=true
APP_NAME="Mi App (STAGING)"
```

## .env.release
```env
ENVIRONMENT=release
API_URL=https://api.example.com
LOG_LEVEL=error
FEATURE_FLAGS=false
APP_NAME="Mi App"
```

## .env.example (plantilla)
```env
ENVIRONMENT=debug
API_URL=https://api-dev.example.com
LOG_LEVEL=debug
FEATURE_FLAGS=true
APP_NAME="Mi App"
```

## .gitignore
```gitignore
# Archivos de entorno - NO subir a repositorio
assets/env/.env*
!.env.example
```
