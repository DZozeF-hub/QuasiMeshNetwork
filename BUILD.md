# Инструкция по сборке APK

## 🚀 Быстрая сборка через GitHub Actions

### Автоматическая сборка (рекомендуется)

1. **Загрузите проект на GitHub:**
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git remote add origin <ваш-репозиторий>
   git push -u origin main
   ```

2. **GitHub Actions автоматически соберет APK:**
   - Перейдите в раздел `Actions` вашего репозитория
   - Дождитесь завершения сборки
   - Скачайте APK из артефактов

## 🔧 Локальная сборка

### Вариант 1: Через Android Studio (самый простой)

1. Откройте проект в Android Studio
2. Выберите `Build` → `Build Bundle(s) / APK(s)` → `Build APK(s)`
3. APK будет в `app/build/outputs/apk/release/app-release.apk`

### Вариант 2: Через командную строку (требует Android SDK)

#### Требования:
- Android SDK установлен
- Переменная окружения `ANDROID_HOME` настроена
- Java JDK 8+

#### Шаги:

1. **Установите Android SDK:**
   ```bash
   # Windows
   set ANDROID_HOME=C:\Users\%USERNAME%\AppData\Local\Android\Sdk
   
   # Linux/Mac
   export ANDROID_HOME=$HOME/Android/Sdk
   ```

2. **Соберите APK:**
   ```bash
   # Windows
   gradlew.bat assembleRelease
   
   # Linux/Mac
   ./gradlew assembleRelease
   ```

3. **APK будет в:**
   ```
   app/build/outputs/apk/release/app-release.apk
   ```

## 📦 Сборка через Docker (альтернатива)

Если у вас нет Android SDK, можно использовать Docker:

```bash
docker run --rm -v "$PWD":/project -w /project \
  android-build-tools:latest \
  ./gradlew assembleRelease
```

## ✅ Проверка APK

После сборки проверьте APK:

```bash
# Установите apksigner (входит в Android SDK)
apksigner verify app-release.apk

# Или через aapt
aapt dump badging app-release.apk
```

## 🔐 Подпись APK для релиза

Для production версии нужно создать keystore:

```bash
keytool -genkey -v -keystore release.keystore \
  -alias release -keyalg RSA -keysize 2048 -validity 10000
```

Затем обновите `app/build.gradle`:

```gradle
signingConfigs {
    release {
        storeFile file('release.keystore')
        storePassword 'your-password'
        keyAlias 'release'
        keyPassword 'your-password'
    }
}
buildTypes {
    release {
        signingConfig signingConfigs.release
    }
}
```

## 🐛 Решение проблем

### Ошибка: "SDK location not found"
```bash
# Создайте local.properties в корне проекта
echo "sdk.dir=C:/Users/YourName/AppData/Local/Android/Sdk" > local.properties
```

### Ошибка: "Gradle sync failed"
- Проверьте интернет-соединение
- Убедитесь, что версия Gradle совместима
- Выполните `./gradlew clean`

### Ошибка при сборке
- Убедитесь, что установлен Android SDK Platform 34
- Проверьте, что Java версии 8+

## 📱 Установка APK на устройство

```bash
adb install app-release.apk
```

Или просто скопируйте APK на устройство и установите вручную (нужно разрешить установку из неизвестных источников).

---

**Самый простой способ**: Используйте GitHub Actions - он соберет APK автоматически! 🎉

