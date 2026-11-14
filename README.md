# HomeScreen Widgets - News Reader App

Aplicación móvil desarrollada con **Flutter** que integra **Home Screen Widgets nativos** tanto en **Android (AppWidget)** como en **iOS (WidgetKit)** utilizando el paquete [`home_widget`](https://pub.dev/packages/home_widget). La aplicación permite visualizar noticias destacadas, renderizar gráficos vectoriales Flutter fuera de pantalla hacia imágenes bitmap y actualizar dinámicamente los widgets de la pantalla de inicio del sistema operativo con títulos, descripciones e imágenes sincronizadas.

---

## 🚀 Tecnologías Usadas

### 1. Núcleo Flutter & Dart
* **[Flutter SDK](https://flutter.dev/) (v3.x / Dart 3.x)**: Framework de desarrollo multiplataforma compilado a código nativo.
* **[Material UI / Material Design 3](https://pub.dev/packages/material_ui)**: Sistema de diseño y tematización visual con paletas generadas dinámicamente (`ColorScheme.fromSeed(seedColor: Colors.deepPurple)`).
* **[Home Widget (`home_widget`)](https://pub.dev/packages/home_widget)**: Librería encargada de la comunicación bidireccional entre la capa Flutter (Dart) y las APIs nativas de widgets de Android e iOS.
* **[Shared Preferences (`shared_preferences`)](https://pub.dev/packages/shared_preferences)**: Persistencia clave-valor compartida para el paso de información entre procesos.
* **[Cupertino Icons](https://pub.dev/packages/cupertino_icons)**: Tipografía de glifos e iconos estilizados para iOS.
* **Tipografía personalizada (`Chewy`)**: Fuente TrueType (`fonts/Chewy-Regular.ttf`) registrada en `pubspec.yaml` y cargada tanto en Flutter como a nivel nativo en iOS vía `CoreText`.

### 2. Motor Gráfico & Renderizado
* **`CustomPainter` & `Canvas API`**: Dibujo procedural a bajo nivel del componente `LineChart` mediante trazos vectoriales (`Path`, `Paint`).
* **Renderizado Off-Screen (`HomeWidget.renderFlutterWidget`)**: Captura del widget `LineChart` en memoria a resolución lógica y pixel ratio del dispositivo para persistirlo como archivo PNG y enviarlo al widget nativo.

### 3. Plataforma Android
* **Lenguaje**: Kotlin & Java.
* **Android AppWidget Framework**: Implementación de `NewsWidget : AppWidgetProvider` con soporte para ciclo de vida `onUpdate`.
* **Vistas Remotas (`RemoteViews`)**: Maquetación declarativa XML en `res/layout/news_widget.xml` con `TextView` y `ImageView`.
* **Gráficos Bitmap**: Decodificación de imágenes locales mediante `BitmapFactory.decodeFile()` y renderizado con `setImageViewBitmap()`.
* **Configuración XML de Proveedor**: Metadatos en `res/xml/news_widget_info.xml` definiendo tamaños mínimos (`minWidth`/`minHeight`), modos de redimensionamiento y periodicidad de actualización.

### 4. Plataforma iOS
* **Lenguaje & Framework**: Swift, SwiftUI y **WidgetKit**.
* **Arquitectura de Widgets**:
  * `NewsWidgets : Widget` y `NewsWidgetsBundle : WidgetBundle`.
  * `NewsWidgetsEntryView : View`: Vista SwiftUI responsiva adaptada al tamaño del widget (`displaySize`).
  * `Provider : TimelineProvider`: Generación de estados (`TimelineEntry`, `NewsArticleEntry`) y políticas de recarga (`TimelineReloadPolicy.atEnd`).
* **Seguridad & Comunicación entre Procesos**: `UserDefaults(suiteName: appGroupId)` mediante **App Groups**, permitiendo a la extensión del widget (`.appex`) leer los datos guardados por la app principal.
* **Carga Dinámica de Tipografías (`CoreText`)**: Acceso al bundle de assets compilados de Flutter (`App.framework/flutter_assets`) para registrar la tipografía `Chewy` en el proceso del widget mediante `CTFontManagerRegisterFontsForURL`.

---

## 📂 Estructura del Proyecto

```text
homescreen_widgets/
├── android/
│   └── app/src/main/
│       ├── AndroidManifest.xml          # Registro del receptor .NewsWidget
│       ├── java/.../NewsWidget.kt       # Implementación Kotlin de AppWidgetProvider
│       └── res/
│           ├── layout/news_widget.xml   # Layout nativo del widget Android
│           ├── xml/news_widget_info.xml # Metadatos de configuración del widget
│           └── values/                  # Estilos, temas, dimensiones y strings
├── fonts/
│   └── Chewy-Regular.ttf                # Fuente tipográfica Chewy
├── ios/
│   ├── NewsWidgets/
│   │   ├── NewsWidgets.swift            # Extensión WidgetKit (Provider, Views, Entry)
│   │   └── NewsWidgetsBundle.swift      # Punto de entrada de la extensión
│   └── Runner/                          # Configuración y runner nativo de iOS
├── lib/
│   ├── main.dart                        # Punto de entrada de la app y configuración de tema
│   ├── news_data.dart                   # Modelo NewsArticle y repositorio de noticias simuladas
│   ├── home_screen.dart                 # Pantalla principal (lista y envío a widget)
│   └── article_screen.dart              # Detalle de noticia y captura de LineChart a imagen
└── pubspec.yaml                         # Dependencias y registro de fuentes
```

---

## 🛠️ Requisitos Previos

Antes de arrancar la aplicación, asegúrate de tener instalado:

1. **Flutter SDK** (versión 3.13 o superior):
   ```bash
   flutter --version
   flutter doctor
   ```
2. **Entorno Android**:
   * Android Studio con Android SDK (API nivel 26 o superior).
   * Emulador de Android configurado o dispositivo físico conectado por USB con depuración activada.
3. **Entorno iOS** (opcional, requiere macOS):
   * Xcode 14+ y CocoaPods.
   * Simulador iOS configurado con iOS 14.0 o superior.

---

## ⚙️ Configuración Previa

### Identificador de App Group (iOS)
Para que el widget de iOS comparta información con la app Flutter:
1. Abre `lib/home_screen.dart` y reemplaza el valor de `appGroupId`:
   ```dart
   const String appGroupId = 'group.com.mydomain.homescreenWidgets';
   ```
2. Abre `ios/NewsWidgets/NewsWidgets.swift` y coloca el mismo identificador:
   ```swift
   let userDefaults = UserDefaults(suiteName: "group.com.mydomain.homescreenWidgets")
   ```
3. En Xcode, activa la capacidad **App Groups** tanto en el target `Runner` como en el target `NewsWidgetsExtension` asignando dicho identificador.

---

## 🖥️ Cómo Arrancar y Ejecutar la Aplicación

### 1. Obtener Dependencias
Navega a la carpeta del proyecto y descarga los paquetes:
```bash
cd homescreen_widgets
flutter pub get
```

### 2. Verificar Dispositivos Disponibles
Comprueba qué emuladores o dispositivos físicos están detectados:
```bash
flutter devices
```

Para listar e iniciar un emulador de Android existente:
```bash
flutter emulators
flutter emulators --launch <ID_DEL_EMULADOR>
```

### 3. Iniciar el Servidor de Desarrollo y Ejecutar la Aplicación

Ejecuta el comando principal de Flutter. Esto iniciará el entorno de compilación, el servidor de depuración (*Dart VM Service / Hot Reload Server*) y lanzará la aplicación:

```bash
# Ejecutar en el dispositivo activo por defecto
flutter run

# O especificar explícitamente el dispositivo/plataforma
flutter run -d android
flutter run -d ios
```

#### Controles interactivos en la terminal durante la ejecución:
* `r`: **Hot Reload** (recarga instantánea de cambios en la UI).
* `R`: **Hot Restart** (reinicio completo de la máquina virtual Dart).
* `v`: Abrir **Flutter DevTools** en el navegador para inspeccionar rendimiento, memoria y árbol de widgets.
* `q`: Detener la ejecución y cerrar la app.

### 4. Lanzar con Servidor de Flutter DevTools Independiente (Opcional)
Si deseas inspeccionar el estado de la app en un navegador web dedicado:
```bash
flutter run --observatory-port=8888
```
Y en otra pestaña de la terminal:
```bash
dart devtools
```

---

## 📱 Cómo Probar los Widgets en la Pantalla de Inicio

### En Android:
1. Abre la aplicación en tu emulador o dispositivo y navega por las noticias.
2. Presiona el botón flotante **"Update Homescreen"** en la pantalla de detalle de una noticia para renderizar el gráfico y guardar el titular actual.
3. Minimiza la aplicación (botón Home).
4. Mantén presionado un área vacía de la pantalla de inicio y selecciona **Widgets**.
5. Busca **homescreen_widgets** o **NewsWidget**.
6. Arrastra el widget a la pantalla de inicio: verás el título, la descripción y el gráfico `LineChart` renderizado.

### En iOS:
1. Compila y ejecuta la aplicación en el Simulador de iOS.
2. Abre una noticia y pulsa **"Update Homescreen"**.
3. Ve a la pantalla de inicio del dispositivo, mantén presionado el fondo hasta que los iconos comiencen a temblar (Modo Jiggle).
4. Toca el botón **`+`** en la esquina superior izquierda.
5. Busca **homescreen_widgets** / **NewsWidgets**, elige el tamaño del widget y pulsa **Add Widget**.
6. El widget se inicializará mostrando la tipografía `Chewy` en el título y el gráfico de líneas correspondiente.

---

## 🧪 Análisis de Código y Verificación

Para garantizar que no existan advertencias ni errores de análisis estático en el código Dart:
```bash
flutter analyze
```
