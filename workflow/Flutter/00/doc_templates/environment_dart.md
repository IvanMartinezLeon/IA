# lib/config/environment.dart

```dart
import 'package:flutter_dotenv/flutter_dotenv.dart';
import 'package:flutter/foundation.dart';

enum AppEnvironment { debug, profile, release }

class EnvironmentConfig {
  static late AppEnvironment environment;
  static late String apiUrl;
  static late String logLevel;
  static late bool featureFlags;
  static late String appName;

  static Future<void> initialize() async {
    final envFile = kDebugMode ? '.env.debug' 
                 : kProfileMode ? '.env.profile' 
                 : '.env.release';
    
    environment = kDebugMode ? AppEnvironment.debug 
               : kProfileMode ? AppEnvironment.profile 
               : AppEnvironment.release;

    await dotenv.load(fileName: 'assets/env/$envFile');
    
    apiUrl = dotenv.env['API_URL'] ?? 'https://api.example.com';
    logLevel = dotenv.env['LOG_LEVEL'] ?? 'info';
    featureFlags = dotenv.env['FEATURE_FLAGS'] == 'true';
    appName = dotenv.env['APP_NAME'] ?? 'Mi App';
  }

  static String get environmentName => switch (environment) {
        AppEnvironment.debug => 'debug',
        AppEnvironment.profile => 'profile',
        AppEnvironment.release => 'release',
      };
}
```

## Uso en runtime
```dart
// Leer variables de entorno
final apiUrl = EnvironmentConfig.apiUrl;
final isDebug = EnvironmentConfig.environment == AppEnvironment.debug;
final featuresEnabled = EnvironmentConfig.featureFlags;
```
