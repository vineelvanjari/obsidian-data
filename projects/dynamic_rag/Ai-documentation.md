# Dynamic RAG — Level 1 Build Guide

# Android + Web — Complete Step-by-Step

> Every step: what we build, why, then code.  
> Never skip a step. Never move on until the current step works.

---

## How to Use This File

- Follow steps in order — each one builds on the previous
- `✅ Checkpoint` = verify this works before continuing
- `💡 Concept` = explanation of why something works the way it does
- `📁 File` = exact file path to create or edit
- Every code block is complete — no "fill in the rest yourself"

---

## The Full Step List

```
PHASE 1 — Foundation          Steps  1–7
PHASE 2 — Authentication      Steps  8–14
PHASE 3 — Settings            Steps 15–19
PHASE 4 — File Management     Steps 20–30
PHASE 5 — Python Backend      Steps 31–42
PHASE 6 — Chat                Steps 43–52
PHASE 7 — Polish + Deploy     Steps 53–59
```

---

---

# PHASE 1 — Foundation

---

## Step 1 — Create Flutter Project

### What

Create the Flutter project with only Android and Web platforms. No iOS, desktop, Linux — those come at their levels.

### Command

```bash
flutter create dynamic_rag --org com.dynamicrag --platforms android,web
cd dynamic_rag
```

**What each part means:**

- `dynamic_rag` — folder name and Dart package name. Dart uses snake_case.
- `--org com.dynamicrag` — becomes Android package name: `com.dynamicrag.dynamic_rag`. Pick your own like `com.vineel` — just stay consistent.
- `--platforms android,web` — only generate what we need now.

### Verify it runs

```bash
flutter run
```

Pick Android emulator or Chrome. Default counter app should appear.

### ✅ Checkpoint

Counter app runs on Android emulator AND Chrome browser.

---

## Step 2 — Understand Project Structure

### What

Before touching anything, understand what every generated file and folder does. No black boxes.

### The Structure Flutter Generated

```
dynamic_rag/
│
├── lib/                        ← ALL your Dart code lives here. This is your app.
│   └── main.dart               ← Entry point. Flutter starts here.
│
├── android/                    ← Android-specific config. You rarely touch this directly.
│   └── app/
│       └── build.gradle        ← Android build config. You will add SHA-1 here later.
│
├── web/                        ← Web-specific files.
│   ├── index.html              ← The HTML page Flutter Web runs inside. You will add OAuth client ID here.
│   └── manifest.json           ← Web app metadata (name, icons)
│
├── test/                       ← Unit and widget tests. We add these at Level 2.
│
├── pubspec.yaml                ← THE most important config file.
│                                  Declares app name, Flutter version, and all packages.
│
├── pubspec.lock                ← Auto-generated. Records exact versions installed. Never edit manually.
│
└── .gitignore                  ← Files Git should ignore. firebase_options.dart is added here later.
```

### 💡 Concept — What is pubspec.yaml?

Think of it as `package.json` if you know web dev, or `requirements.txt` if you know Python. It lists every dependency your app needs. When you run `flutter pub get`, Flutter downloads them all.

### 💡 Concept — What is lib/?

This is the only folder you write Dart code in. Everything in `android/` and `web/` is generated config — you only touch those files for specific platform setup steps (we will tell you exactly when).

### ✅ Checkpoint

You can explain what `pubspec.yaml` does and where your Dart code lives.

---

## Step 3 — Add All Packages

### What

Add every package the app needs to `pubspec.yaml`. We add all of them now so we never have a "package not found" error mid-build.

### 💡 Concept — What is a Flutter package?

A package is someone else's code you use in your app. Instead of writing a Firebase integration from scratch, you add `firebase_core` and it's done. Packages live on [pub.dev](https://pub.dev/).

### 📁 Edit `pubspec.yaml`

Replace the `dependencies:` section with this:

```yaml
dependencies:
  flutter:
    sdk: flutter

  # Firebase
  firebase_core: ^3.3.0
  firebase_auth: ^5.1.4
  google_sign_in: ^6.2.1
  cloud_firestore: ^5.2.1
  firebase_storage: ^12.1.3

  # Backend HTTP
  dio: ^5.7.0

  # State management
  flutter_riverpod: ^2.5.1
  riverpod_annotation: ^2.3.5

  # UI
  flutter_markdown: ^0.7.3
  dash_chat_2: ^0.0.21

  # Utilities
  connectivity_plus: ^6.0.5
  uuid: ^4.5.0
  shared_preferences: ^2.3.2
  intl: ^0.19.0
  go_router: ^14.2.7

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^4.0.0
  build_runner: ^2.4.12
  riverpod_generator: ^2.4.3
  custom_lint: ^0.6.7
  riverpod_lint: ^2.3.13
```

Then run:

```bash
flutter pub get
```

You should see "Got dependencies!" with no errors.

### ✅ Checkpoint

`flutter pub get` runs with no errors.

---

## Step 4 — Create Folder Structure

### What

Create all the folders and empty Dart files. We create them all now so every import path works from the start. Empty files are fine — we fill them in as we go.

### 💡 Concept — Why create empty files now?

When file A imports file B, Dart needs file B to exist — even if it's empty. Creating the structure upfront prevents "file not found" errors while we're building.

### Run These Commands

From your project root (`dynamic_rag/`):

```bash
# core
mkdir -p lib/core/constants lib/core/errors lib/core/network lib/core/utils

# ui/atoms
mkdir -p lib/ui/atoms

# ui/molecules
mkdir -p lib/ui/molecules

# ui/organisms
mkdir -p lib/ui/organisms

# ui/templates
mkdir -p lib/ui/templates

# features
mkdir -p lib/features/auth/screens
mkdir -p lib/features/auth/providers
mkdir -p lib/features/auth/services
mkdir -p lib/features/files/screens
mkdir -p lib/features/files/providers
mkdir -p lib/features/files/services
mkdir -p lib/features/files/models
mkdir -p lib/features/chat/screens
mkdir -p lib/features/chat/providers
mkdir -p lib/features/chat/services
mkdir -p lib/features/chat/models
mkdir -p lib/features/settings/screens
mkdir -p lib/features/settings/providers
mkdir -p lib/features/settings/models

# shared services + router
mkdir -p lib/services
mkdir -p lib/router
```

Now create all empty Dart files:

```bash
# core
touch lib/core/constants/app_strings.dart
touch lib/core/constants/app_sizes.dart
touch lib/core/constants/app_routes.dart
touch lib/core/errors/app_exceptions.dart
touch lib/core/network/connectivity_service.dart
touch lib/core/utils/date_formatter.dart

# ui atoms
touch lib/ui/atoms/app_colors.dart
touch lib/ui/atoms/app_text_styles.dart
touch lib/ui/atoms/app_theme.dart
touch lib/ui/atoms/app_button.dart
touch lib/ui/atoms/app_icon.dart
touch lib/ui/atoms/app_loader.dart

# ui molecules
touch lib/ui/molecules/file_list_tile.dart
touch lib/ui/molecules/chat_input_bar.dart
touch lib/ui/molecules/offline_banner.dart
touch lib/ui/molecules/index_status_badge.dart
touch lib/ui/molecules/settings_toggle_row.dart
touch lib/ui/molecules/file_filter_chip.dart
touch lib/ui/molecules/name_input_dialog.dart

# ui organisms
touch lib/ui/organisms/file_list.dart
touch lib/ui/organisms/chat_message_bubble.dart
touch lib/ui/organisms/chat_history.dart
touch lib/ui/organisms/chat_filter_bar.dart
touch lib/ui/organisms/app_bottom_nav.dart

# ui templates
touch lib/ui/templates/main_scaffold.dart
touch lib/ui/templates/editor_scaffold.dart

# auth
touch lib/features/auth/screens/login_screen.dart
touch lib/features/auth/screens/onboarding_screen.dart
touch lib/features/auth/providers/auth_provider.dart
touch lib/features/auth/services/auth_service.dart

# files
touch lib/features/files/screens/file_list_screen.dart
touch lib/features/files/screens/editor_screen.dart
touch lib/features/files/providers/file_list_provider.dart
touch lib/features/files/providers/editor_provider.dart
touch lib/features/files/services/file_service.dart
touch lib/features/files/services/index_service.dart
touch lib/features/files/models/md_file.dart

# chat
touch lib/features/chat/screens/chat_screen.dart
touch lib/features/chat/providers/chat_provider.dart
touch lib/features/chat/services/chat_service.dart
touch lib/features/chat/models/chat_message.dart

# settings
touch lib/features/settings/screens/settings_screen.dart
touch lib/features/settings/screens/profile_screen.dart
touch lib/features/settings/providers/settings_provider.dart
touch lib/features/settings/models/app_settings.dart

# services + router
touch lib/services/backend_service.dart
touch lib/router/app_router.dart
```

### ✅ Checkpoint

Run `find lib -name "*.dart" | wc -l` — you should see 40+ files.

---

## Step 5 — Set Up Firebase Project

### What

Create a Firebase project in the Firebase Console. Enable the three services we need: Auth, Firestore, Storage.

### Steps (in Firebase Console — console.firebase.google.com)

**Create project:**

1. Click "Add project"
2. Name: `dynamic-rag`
3. Disable Google Analytics (not needed)
4. Click "Create project"

**Enable Authentication:**

1. Left sidebar → Build → Authentication
2. Click "Get started"
3. Sign-in method tab → Google → Enable
4. Set a support email → Save

**Enable Firestore:**

1. Left sidebar → Build → Firestore Database
2. Click "Create database"
3. Choose "Start in test mode" (we add proper rules in Step 6)
4. Pick a region close to you → Enable

**Enable Storage:**

1. Left sidebar → Build → Storage
2. Click "Get started"
3. Start in test mode → Next
4. Same region as Firestore → Done

### ✅ Checkpoint

Firebase Console shows Auth ✓, Firestore ✓, Storage ✓ all enabled.

---

## Step 6 — Connect Flutter to Firebase

### What

Use FlutterFire CLI to connect your Flutter app to your Firebase project. This auto-generates `firebase_options.dart` — the config file Flutter needs to talk to Firebase.

### 💡 Concept — What is firebase_options.dart?

It contains your Firebase project's API keys, project ID, and app IDs for each platform. FlutterFire CLI generates it by reading your Firebase project directly. You never write this file manually — you always generate it.

### Install FlutterFire CLI

```bash
dart pub global activate flutterfire_cli
```

### Run Configuration

```bash
flutterfire configure --project=dynamic-rag
```

When asked which platforms to support: select **Android** and **Web** only.

This generates `lib/firebase_options.dart` automatically.

### Add Firebase to Android — SHA-1 Fingerprint

Google Sign-In on Android requires your app's SHA-1 fingerprint registered in Firebase.

Get your debug SHA-1:

```bash
cd android
./gradlew signingReport
```

Look for the `debug` variant SHA1 line. Copy it.

Go to Firebase Console → Project Settings (gear icon) → Your apps → Android app → Add fingerprint → paste SHA-1 → Save.

### ✅ Checkpoint

`lib/firebase_options.dart` exists and contains your project configuration.

---

## Step 7 — Write main.dart

### What

The entry point of the app. Initialises Firebase, wraps everything in Riverpod's `ProviderScope`, and sets up the `MaterialApp` with theming and routing.

### 💡 Concept — What is ProviderScope?

Riverpod's state management needs a `ProviderScope` at the very top of the widget tree. Think of it as the container that holds all your app's state. Without it, no Riverpod provider works.

### 💡 Concept — Why MaterialApp.router instead of MaterialApp?

`MaterialApp.router` integrates with `go_router` for navigation. Regular `MaterialApp` uses a simpler push/pop navigation that doesn't support auth-aware redirects cleanly.

### 📁 `lib/main.dart`

```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'firebase_options.dart';
import 'router/app_router.dart';
import 'features/settings/providers/settings_provider.dart';
import 'ui/atoms/app_theme.dart';

void main() async {
  // Ensures Flutter engine is ready before we call any native code
  WidgetsFlutterBinding.ensureInitialized();

  // Initialise Firebase using the generated config for the current platform
  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );

  // ProviderScope wraps the entire app — required for Riverpod
  runApp(const ProviderScope(child: DynamicRagApp()));
}

class DynamicRagApp extends ConsumerWidget {
  const DynamicRagApp({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    // Watch settings so the theme reacts live when user changes it
    final settings = ref.watch(settingsProvider);

    return MaterialApp.router(
      title: 'Dynamic RAG',
      debugShowCheckedModeBanner: false,

      // Light and dark themes — built from AppColors
      theme: AppTheme.light(),
      darkTheme: AppTheme.dark(),

      // ThemeMode.system = follows device setting
      // Changes to light/dark when user picks in Settings
      themeMode: settings.themeMode,

      // go_router handles all navigation
      routerConfig: appRouter,
    );
  }
}
```

### Why `ConsumerWidget` and not `StatelessWidget`?

`ConsumerWidget` is Riverpod's version of `StatelessWidget`. The `ref` parameter it provides is how you read providers. We need `ref.watch(settingsProvider)` so the theme updates live when the user changes it in Settings.

### ✅ Checkpoint

App compiles. It will crash on missing files — that's expected. We fix those next.

---

---

# PHASE 2 — Authentication

---

## Step 8 — Atoms: Colors, Text Styles, Theme

### What

The three foundational atom files. Everything visual in the app traces back to these.

### 📁 `lib/ui/atoms/app_colors.dart`

```dart
import 'package:flutter/material.dart';

// All colours in one place. Never use Color() directly in any widget.
// Change here → changes everywhere.
class AppColors {
  AppColors._(); // private constructor — all static, never instantiate

  // Brand
  static const Color primary     = Color(0xFF6C63FF);
  static const Color primaryDark = Color(0xFF4B44CC);

  // Light theme
  static const Color lightBackground = Color(0xFFFFFFFF);
  static const Color lightSurface    = Color(0xFFF5F5F5);
  static const Color lightText       = Color(0xFF1A1A1A);
  static const Color lightTextSub    = Color(0xFF666666);
  static const Color lightBorder     = Color(0xFFE0E0E0);

  // Dark theme
  static const Color darkBackground  = Color(0xFF121212);
  static const Color darkSurface     = Color(0xFF1E1E1E);
  static const Color darkText        = Color(0xFFF5F5F5);
  static const Color darkTextSub     = Color(0xFFAAAAAA);
  static const Color darkBorder      = Color(0xFF2C2C2C);

  // Semantic — same in both themes
  static const Color success = Color(0xFF4CAF50);
  static const Color error   = Color(0xFFE53935);
  static const Color warning = Color(0xFFFFA726);
  static const Color info    = Color(0xFF29B6F6);
}
```

### 📁 `lib/ui/atoms/app_text_styles.dart`

```dart
import 'package:flutter/material.dart';
import 'app_colors.dart';

class AppTextStyles {
  AppTextStyles._();

  // Builds a full TextTheme using the given text colour.
  // Called by AppTheme with lightText or darkText.
  static TextTheme buildTextTheme(Color textColor) {
    return TextTheme(
      // Large titles
      headlineLarge: TextStyle(
        fontSize: 28, fontWeight: FontWeight.bold, color: textColor),
      headlineMedium: TextStyle(
        fontSize: 22, fontWeight: FontWeight.bold, color: textColor),
      headlineSmall: TextStyle(
        fontSize: 18, fontWeight: FontWeight.w600, color: textColor),

      // Body text
      bodyLarge: TextStyle(fontSize: 16, color: textColor),
      bodyMedium: TextStyle(fontSize: 14, color: textColor),
      bodySmall: TextStyle(
        fontSize: 12, color: textColor.withOpacity(0.7)),

      // Labels (buttons, captions)
      labelLarge: TextStyle(
        fontSize: 14, fontWeight: FontWeight.w600, color: textColor),
      labelSmall: TextStyle(
        fontSize: 11, color: textColor.withOpacity(0.6)),
    );
  }
}
```

### 📁 `lib/ui/atoms/app_theme.dart`

```dart
import 'package:flutter/material.dart';
import 'app_colors.dart';
import 'app_text_styles.dart';

class AppTheme {
  AppTheme._();

  static ThemeData light() {
    return ThemeData(
      brightness: Brightness.light,
      colorScheme: ColorScheme.light(
        primary:   AppColors.primary,
        secondary: AppColors.primaryDark,
        surface:   AppColors.lightSurface,
        background: AppColors.lightBackground,
        error:     AppColors.error,
        onPrimary: Colors.white,
        onSurface: AppColors.lightText,
      ),
      scaffoldBackgroundColor: AppColors.lightBackground,
      textTheme: AppTextStyles.buildTextTheme(AppColors.lightText),
      dividerColor: AppColors.lightBorder,
      appBarTheme: AppBarTheme(
        backgroundColor: AppColors.lightBackground,
        foregroundColor: AppColors.lightText,
        elevation: 0,
        surfaceTintColor: Colors.transparent,
      ),
      cardTheme: CardTheme(
        color: AppColors.lightSurface,
        elevation: 0,
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(12),
          side: BorderSide(color: AppColors.lightBorder),
        ),
      ),
    );
  }

  static ThemeData dark() {
    return ThemeData(
      brightness: Brightness.dark,
      colorScheme: ColorScheme.dark(
        primary:   AppColors.primary,
        secondary: AppColors.primaryDark,
        surface:   AppColors.darkSurface,
        background: AppColors.darkBackground,
        error:     AppColors.error,
        onPrimary: Colors.white,
        onSurface: AppColors.darkText,
      ),
      scaffoldBackgroundColor: AppColors.darkBackground,
      textTheme: AppTextStyles.buildTextTheme(AppColors.darkText),
      dividerColor: AppColors.darkBorder,
      appBarTheme: AppBarTheme(
        backgroundColor: AppColors.darkBackground,
        foregroundColor: AppColors.darkText,
        elevation: 0,
        surfaceTintColor: Colors.transparent,
      ),
      cardTheme: CardTheme(
        color: AppColors.darkSurface,
        elevation: 0,
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(12),
          side: BorderSide(color: AppColors.darkBorder),
        ),
      ),
    );
  }
}
```

### ✅ Checkpoint

No analysis errors on these three files (`flutter analyze lib/ui/atoms/`).

---

## Step 9 — Constants and Router Shell

### What

`app_strings.dart` holds every hardcoded string. `app_routes.dart` holds route name constants. `app_router.dart` wires up navigation. We write a shell router now — just enough to compile. We update it as we build each screen.

### 📁 `lib/core/constants/app_strings.dart`

```dart
// Every string the app displays or uses — in one place.
// When we add localisation later, this file is where we start.
class AppStrings {
  AppStrings._();

  // App
  static const String appName         = 'Dynamic RAG';

  // Auth
  static const String signInWithGoogle = 'Sign in with Google';
  static const String signOut          = 'Sign Out';

  // Onboarding
  static const String onboardingTitle    = 'Welcome to Dynamic RAG';
  static const String onboardingSubtitle = 'Your personal AI knowledge base';
  static const String onboardingFiles    = 'Tab 1 — Files\nCreate and edit your Markdown notes';
  static const String onboardingChat     = 'Tab 2 — Chat\nAsk questions about your notes';
  static const String onboardingSettings = 'Tab 3 — Settings\nConfigure your preferences';
  static const String onboardingRag      =
      'Your notes are indexed so the AI can find relevant parts '
      'when you ask a question. The AI only answers from your notes '
      '— not from general knowledge.';
  static const String onboardingButton  = 'Got it';

  // Files
  static const String filesTab          = 'Files';
  static const String newFile           = 'New File';
  static const String noFilesYet        = 'No files yet.\nTap + to create your first note.';
  static const String enterFileName     = 'Enter file name';
  static const String fileNameHint      = 'e.g. Project Ideas';
  static const String create            = 'Create';
  static const String cancel            = 'Cancel';
  static const String delete            = 'Delete';
  static const String edit              = 'Edit';
  static const String save              = 'Save';
  static const String deleteConfirmTitle = 'Delete file?';
  static const String deleteConfirmBody  = 'This cannot be undone.';

  // Index status
  static const String indexed       = 'Indexed ✓';
  static const String syncing       = 'Syncing…';
  static const String syncFailed    = 'Sync failed';
  static const String sync          = 'Sync';
  static const String retrySync     = 'Retry';

  // Chat
  static const String chatTab       = 'Chat';
  static const String chatHint      = 'Ask something about your notes…';
  static const String allFiles      = 'All Files';
  static const String clearChat     = 'Clear Chat';
  static const String sources       = 'Sources:';
  static const String noAnswer      = 'I don\'t have that in your notes.';

  // Settings
  static const String settingsTab        = 'Settings';
  static const String theme              = 'Theme';
  static const String reindexMode        = 'Reindex Mode';
  static const String reindexAuto        = 'Auto — reindex on every save';
  static const String reindexManual      = 'Manual — tap Sync button';
  static const String chatHistoryLimit   = 'Chat History';
  static const String showSources        = 'Show Sources';
  static const String showSourcesDesc    = 'Display which files answered each message';
  static const String profile            = 'Profile';

  // Offline
  static const String offlineMessage = 'You are offline. Please reconnect to use the app.';

  // Errors
  static const String errorGeneric     = 'Something went wrong. Please try again.';
  static const String errorNetwork     = 'Network error. Check your connection.';
  static const String errorIndexFailed = 'Failed to index file after 3 attempts.';

  // Backend URL — change this when you deploy to Railway
  static const String backendBaseUrl = 'http://10.0.2.2:8000';
  // For web dev use: 'http://localhost:8000'
  // For production use: 'https://your-app.railway.app'
}
```

### 📁 `lib/core/constants/app_routes.dart`

```dart
class AppRoutes {
  AppRoutes._();

  static const String login       = '/login';
  static const String onboarding  = '/onboarding';
  static const String home        = '/home';
  static const String editor      = '/editor';
  static const String profile     = '/profile';
}
```

### 📁 `lib/router/app_router.dart`

```dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import '../core/constants/app_routes.dart';
import '../features/auth/providers/auth_provider.dart';
import '../features/auth/screens/login_screen.dart';
import '../features/auth/screens/onboarding_screen.dart';
import '../ui/templates/main_scaffold.dart';
import '../features/files/screens/editor_screen.dart';
import '../features/settings/screens/profile_screen.dart';

// A provider so the router can watch auth state
final _routerProvider = Provider<GoRouter>((ref) {
  final authState = ref.watch(authProvider);

  return GoRouter(
    initialLocation: AppRoutes.login,
    redirect: (context, state) {
      final isLoggedIn = authState.valueOrNull != null;
      final isOnLogin = state.matchedLocation == AppRoutes.login;
      final isOnOnboarding = state.matchedLocation == AppRoutes.onboarding;

      // Not logged in → always go to login
      if (!isLoggedIn && !isOnLogin) return AppRoutes.login;

      // Logged in but on login page → go home
      if (isLoggedIn && isOnLogin) return AppRoutes.home;

      return null; // no redirect needed
    },
    routes: [
      GoRoute(
        path: AppRoutes.login,
        builder: (context, state) => const LoginScreen(),
      ),
      GoRoute(
        path: AppRoutes.onboarding,
        builder: (context, state) => const OnboardingScreen(),
      ),
      GoRoute(
        path: AppRoutes.home,
        builder: (context, state) => const MainScaffold(),
        routes: [
          GoRoute(
            path: 'editor',
            builder: (context, state) {
              // fileId passed as extra — null means new file
              final fileId = state.extra as String?;
              return EditorScreen(fileId: fileId);
            },
          ),
          GoRoute(
            path: 'profile',
            builder: (context, state) => const ProfileScreen(),
          ),
        ],
      ),
    ],
  );
});

// This is what main.dart uses
GoRouter get appRouter {
  // We need a ProviderContainer to read providers outside widget tree
  // In practice this gets replaced once the widget tree is built
  // For now a simple GoRouter works
  return GoRouter(
    initialLocation: AppRoutes.login,
    routes: [
      GoRoute(
        path: AppRoutes.login,
        builder: (context, state) => const LoginScreen(),
      ),
      GoRoute(
        path: AppRoutes.onboarding,
        builder: (context, state) => const OnboardingScreen(),
      ),
      GoRoute(
        path: AppRoutes.home,
        builder: (context, state) => const MainScaffold(),
        routes: [
          GoRoute(
            path: 'editor',
            builder: (context, state) {
              final fileId = state.extra as String?;
              return EditorScreen(fileId: fileId);
            },
          ),
          GoRoute(
            path: 'profile',
            builder: (context, state) => const ProfileScreen(),
          ),
        ],
      ),
    ],
  );
}
```

> Note: We refine the router with auth redirects in Step 11 once `authProvider` exists.

### ✅ Checkpoint

`flutter analyze` shows no errors on these files.

---

## Step 10 — Auth Service

### What

The service that talks to Firebase Auth and Google Sign-In. Pure logic — no UI.

### 💡 Concept — Service vs Provider vs Screen

- **Service** = talks to external things (Firebase, HTTP). No Flutter widgets.
- **Provider** = holds and exposes state. Watches the service. No widgets.
- **Screen/Widget** = the UI. Reads from providers. No direct Firebase calls.

This separation means you can change Firebase to something else without touching any UI.

### 📁 `lib/features/auth/services/auth_service.dart`

```dart
import 'package:firebase_auth/firebase_auth.dart';
import 'package:google_sign_in/google_sign_in.dart';
import 'package:cloud_firestore/cloud_firestore.dart';

class AuthService {
  final _auth = FirebaseAuth.instance;
  final _googleSignIn = GoogleSignIn();
  final _firestore = FirebaseFirestore.instance;

  // Returns the currently signed-in user, or null if not signed in
  User? get currentUser => _auth.currentUser;

  // Stream of auth state changes — emits User when logged in, null when logged out
  Stream<User?> get authStateChanges => _auth.authStateChanges();

  Future<UserCredential> signInWithGoogle() async {
    // Step 1: trigger the Google account picker
    final googleUser = await _googleSignIn.signIn();
    if (googleUser == null) throw Exception('Sign-in cancelled by user');

    // Step 2: get auth tokens from the chosen account
    final googleAuth = await googleUser.authentication;

    // Step 3: exchange Google tokens for a Firebase credential
    final credential = GoogleAuthProvider.credential(
      accessToken: googleAuth.accessToken,
      idToken: googleAuth.idToken,
    );

    // Step 4: sign into Firebase with that credential
    return await _auth.signInWithCredential(credential);
  }

  Future<void> signOut() async {
    await _googleSignIn.signOut();
    await _auth.signOut();
  }

  // Check if this is the user's first login
  // We use a Firestore field to track this
  Future<bool> isFirstLogin(String uid) async {
    final doc = await _firestore
        .collection('users')
        .doc(uid)
        .collection('profile')
        .doc('preferences')
        .get();
    return !doc.exists || doc.data()?['onboardingComplete'] != true;
  }

  // Mark onboarding as complete
  Future<void> completeOnboarding(String uid) async {
    await _firestore
        .collection('users')
        .doc(uid)
        .collection('profile')
        .doc('preferences')
        .set({'onboardingComplete': true}, SetOptions(merge: true));
  }

  // Get the current user's Firebase ID token
  // This is what we send to the backend for verification
  Future<String?> getIdToken() async {
    return await _auth.currentUser?.getIdToken();
  }
}
```

### ✅ Checkpoint

No analysis errors on this file.

---

## Step 11 — Auth Provider

### What

A Riverpod provider that exposes auth state to the whole app. Any widget that needs to know "is the user logged in?" watches this.

### 💡 Concept — StreamProvider

`StreamProvider` wraps a `Stream` and makes it available as Riverpod state. `FirebaseAuth.authStateChanges()` is a stream that emits every time the user logs in or out. Our `authProvider` wraps this stream — so any widget watching it rebuilds automatically when auth state changes.

### 📁 `lib/features/auth/providers/auth_provider.dart`

```dart
import 'package:firebase_auth/firebase_auth.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import '../services/auth_service.dart';

// Single instance of AuthService shared across the app
final authServiceProvider = Provider<AuthService>((ref) => AuthService());

// StreamProvider — emits User? whenever auth state changes
// User = logged in, null = logged out
// AsyncValue wraps the stream value with loading/error states
final authProvider = StreamProvider<User?>((ref) {
  return ref.watch(authServiceProvider).authStateChanges;
});
```

Now update the router to use auth redirects properly:

### 📁 Update `lib/router/app_router.dart`

Replace the entire file with this final version:

```dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import '../core/constants/app_routes.dart';
import '../features/auth/providers/auth_provider.dart';
import '../features/auth/screens/login_screen.dart';
import '../features/auth/screens/onboarding_screen.dart';
import '../ui/templates/main_scaffold.dart';
import '../features/files/screens/editor_screen.dart';
import '../features/settings/screens/profile_screen.dart';

final appRouterProvider = Provider<GoRouter>((ref) {
  final authState = ref.watch(authProvider);

  return GoRouter(
    initialLocation: AppRoutes.login,
    refreshListenable: _AuthStateListenable(ref),
    redirect: (context, state) {
      // While auth is loading, stay put
      if (authState.isLoading) return null;

      final isLoggedIn = authState.valueOrNull != null;
      final location   = state.matchedLocation;

      final isOnLogin      = location == AppRoutes.login;
      final isOnOnboarding = location == AppRoutes.onboarding;

      if (!isLoggedIn && !isOnLogin) return AppRoutes.login;
      if (isLoggedIn && isOnLogin)   return AppRoutes.home;

      return null;
    },
    routes: [
      GoRoute(
        path: AppRoutes.login,
        builder: (_, __) => const LoginScreen(),
      ),
      GoRoute(
        path: AppRoutes.onboarding,
        builder: (_, __) => const OnboardingScreen(),
      ),
      GoRoute(
        path: AppRoutes.home,
        builder: (_, __) => const MainScaffold(),
        routes: [
          GoRoute(
            path: 'editor',
            builder: (_, state) => EditorScreen(fileId: state.extra as String?),
          ),
          GoRoute(
            path: 'profile',
            builder: (_, __) => const ProfileScreen(),
          ),
        ],
      ),
    ],
  );
});

// Notifies go_router to re-evaluate redirects when auth state changes
class _AuthStateListenable extends ChangeNotifier {
  _AuthStateListenable(Ref ref) {
    ref.listen(authProvider, (_, __) => notifyListeners());
  }
}
```

Update `main.dart` to use `appRouterProvider`:

```dart
// In main.dart, replace routerConfig: appRouter with:
routerConfig: ref.watch(appRouterProvider),
```

### ✅ Checkpoint

No analysis errors. App still compiles (screens are still empty stubs).

---

## Step 12 — Login Screen

### What

The screen shown to unauthenticated users. One button: Sign in with Google.

### 📁 `lib/features/auth/screens/login_screen.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:go_router/go_router.dart';
import '../providers/auth_provider.dart';
import '../services/auth_service.dart';
import '../../../core/constants/app_strings.dart';
import '../../../core/constants/app_routes.dart';
import '../../../ui/atoms/app_colors.dart';

class LoginScreen extends ConsumerStatefulWidget {
  const LoginScreen({super.key});

  @override
  ConsumerState<LoginScreen> createState() => _LoginScreenState();
}

class _LoginScreenState extends ConsumerState<LoginScreen> {
  bool _isLoading = false;
  String? _error;

  Future<void> _signIn() async {
    setState(() { _isLoading = true; _error = null; });

    try {
      final authService = ref.read(authServiceProvider);
      final credential  = await authService.signInWithGoogle();
      final uid         = credential.user?.uid;

      if (uid == null) throw Exception('No user after sign-in');

      // Check if first login → go to onboarding, else go home
      final isFirst = await authService.isFirstLogin(uid);
      if (mounted) {
        context.go(isFirst ? AppRoutes.onboarding : AppRoutes.home);
      }
    } catch (e) {
      setState(() { _error = e.toString(); });
    } finally {
      if (mounted) setState(() { _isLoading = false; });
    }
  }

  @override
  Widget build(BuildContext context) {
    final theme = Theme.of(context);

    return Scaffold(
      body: SafeArea(
        child: Padding(
          padding: const EdgeInsets.all(32.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: [
              // App name
              Text(
                AppStrings.appName,
                style: theme.textTheme.headlineLarge?.copyWith(
                  color: AppColors.primary,
                  fontWeight: FontWeight.bold,
                ),
                textAlign: TextAlign.center,
              ),

              const SizedBox(height: 8),

              Text(
                'Your personal AI knowledge base',
                style: theme.textTheme.bodyMedium,
                textAlign: TextAlign.center,
              ),

              const SizedBox(height: 64),

              // Sign-in button
              if (_isLoading)
                const Center(child: CircularProgressIndicator())
              else
                ElevatedButton.icon(
                  onPressed: _signIn,
                  icon: const Icon(Icons.login),
                  label: const Text(AppStrings.signInWithGoogle),
                  style: ElevatedButton.styleFrom(
                    backgroundColor: AppColors.primary,
                    foregroundColor: Colors.white,
                    padding: const EdgeInsets.symmetric(vertical: 16),
                    shape: RoundedRectangleBorder(
                      borderRadius: BorderRadius.circular(12),
                    ),
                  ),
                ),

              // Error message
              if (_error != null) ...[
                const SizedBox(height: 16),
                Text(
                  _error!,
                  style: TextStyle(color: AppColors.error, fontSize: 12),
                  textAlign: TextAlign.center,
                ),
              ],
            ],
          ),
        ),
      ),
    );
  }
}
```

### ✅ Checkpoint

App compiles. Login screen shows. (Button won't work yet — Google Sign-In needs SHA-1 configured.)

---

## Step 13 — Onboarding Screen

### What

Shown once after first login. Explains the three tabs and how RAG works in plain language.

### 📁 `lib/features/auth/screens/onboarding_screen.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:go_router/go_router.dart';
import '../providers/auth_provider.dart';
import '../services/auth_service.dart';
import '../../../core/constants/app_strings.dart';
import '../../../core/constants/app_routes.dart';
import '../../../ui/atoms/app_colors.dart';

class OnboardingScreen extends ConsumerWidget {
  const OnboardingScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final theme = Theme.of(context);

    return Scaffold(
      body: SafeArea(
        child: Padding(
          padding: const EdgeInsets.all(24.0),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: [
              const Spacer(),

              Text(
                AppStrings.onboardingTitle,
                style: theme.textTheme.headlineLarge,
                textAlign: TextAlign.center,
              ),
              const SizedBox(height: 8),
              Text(
                AppStrings.onboardingSubtitle,
                style: theme.textTheme.bodyMedium,
                textAlign: TextAlign.center,
              ),

              const SizedBox(height: 48),

              _OnboardingItem(
                icon: Icons.description_outlined,
                text: AppStrings.onboardingFiles,
              ),
              const SizedBox(height: 20),
              _OnboardingItem(
                icon: Icons.chat_outlined,
                text: AppStrings.onboardingChat,
              ),
              const SizedBox(height: 20),
              _OnboardingItem(
                icon: Icons.settings_outlined,
                text: AppStrings.onboardingSettings,
              ),

              const SizedBox(height: 32),

              // Explain RAG in plain language
              Container(
                padding: const EdgeInsets.all(16),
                decoration: BoxDecoration(
                  color: AppColors.primary.withOpacity(0.08),
                  borderRadius: BorderRadius.circular(12),
                  border: Border.all(
                    color: AppColors.primary.withOpacity(0.2)),
                ),
                child: Text(
                  AppStrings.onboardingRag,
                  style: theme.textTheme.bodySmall,
                  textAlign: TextAlign.center,
                ),
              ),

              const Spacer(),

              ElevatedButton(
                onPressed: () async {
                  final user = ref.read(authProvider).valueOrNull;
                  if (user != null) {
                    await ref
                        .read(authServiceProvider)
                        .completeOnboarding(user.uid);
                  }
                  if (context.mounted) context.go(AppRoutes.home);
                },
                style: ElevatedButton.styleFrom(
                  backgroundColor: AppColors.primary,
                  foregroundColor: Colors.white,
                  padding: const EdgeInsets.symmetric(vertical: 16),
                  shape: RoundedRectangleBorder(
                    borderRadius: BorderRadius.circular(12),
                  ),
                ),
                child: const Text(AppStrings.onboardingButton),
              ),

              const SizedBox(height: 16),
            ],
          ),
        ),
      ),
    );
  }
}

class _OnboardingItem extends StatelessWidget {
  final IconData icon;
  final String text;
  const _OnboardingItem({required this.icon, required this.text});

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Icon(icon, color: AppColors.primary, size: 28),
        const SizedBox(width: 16),
        Expanded(
          child: Text(
            text,
            style: Theme.of(context).textTheme.bodyMedium,
          ),
        ),
      ],
    );
  }
}
```

### ✅ Checkpoint

Onboarding screen builds with no errors.

---

## Step 14 — Test Authentication

### What

Verify the full login flow works end-to-end before building anything else.

### Web — Extra Setup

For Google Sign-In on web, add the Web client ID to `web/index.html`:

```html
<!-- Inside <head> in web/index.html -->
<meta name="google-signin-client_id" content="YOUR_WEB_CLIENT_ID.apps.googleusercontent.com">
```

Find your Web client ID: Firebase Console → Project Settings → Your apps → Web app → Web API Key / OAuth.

Or go directly to [console.cloud.google.com](https://console.cloud.google.com/) → APIs & Services → Credentials → Web client ID.

### Test Checklist

- [ ] Run on Android emulator → Login screen appears
- [ ] Tap "Sign in with Google" → Google account picker appears → pick account
- [ ] First login → onboarding screen appears → tap "Got it" → home screen (empty for now)
- [ ] Sign out → login screen
- [ ] Sign in again → home screen directly (skips onboarding)
- [ ] Run on Chrome → same flow works

### ✅ Checkpoint

Full login and onboarding flow works on both Android and Web.

---

---

# PHASE 3 — Settings

---

## Step 15 — App Settings Model

### What

The data class that represents all user settings. A model is just a plain Dart class that holds data — no UI, no Firebase calls.

### 💡 Concept — Why a model class?

Instead of storing theme as a raw string and reindex mode as another raw string scattered around the app, we group them into one `AppSettings` object. This means: one place to add new settings, one place to change defaults, one place to convert to/from Firestore format.

### 📁 `lib/features/settings/models/app_settings.dart`

```dart
import 'package:flutter/material.dart';

enum ReindexMode { auto, manual }

class AppSettings {
  final ThemeMode themeMode;
  final ReindexMode reindexMode;
  final int chatHistoryLimit;
  final bool showSources;

  const AppSettings({
    this.themeMode        = ThemeMode.system,
    this.reindexMode      = ReindexMode.auto,
    this.chatHistoryLimit = 10,
    this.showSources      = true,
  });

  // Default settings — used on first launch
  factory AppSettings.defaults() => const AppSettings();

  // Convert from Firestore map
  factory AppSettings.fromMap(Map<String, dynamic> map) {
    return AppSettings(
      themeMode: _parseThemeMode(map['theme'] as String? ?? 'system'),
      reindexMode: map['reindexMode'] == 'manual'
          ? ReindexMode.manual
          : ReindexMode.auto,
      chatHistoryLimit: map['chatHistoryLimit'] as int? ?? 10,
      showSources: map['showSources'] as bool? ?? true,
    );
  }

  // Convert to Firestore map
  Map<String, dynamic> toMap() => {
    'theme':             _themeModeToString(themeMode),
    'reindexMode':       reindexMode == ReindexMode.manual ? 'manual' : 'auto',
    'chatHistoryLimit':  chatHistoryLimit,
    'showSources':       showSources,
  };

  // Copy with updated fields — immutable pattern
  AppSettings copyWith({
    ThemeMode?    themeMode,
    ReindexMode?  reindexMode,
    int?          chatHistoryLimit,
    bool?         showSources,
  }) {
    return AppSettings(
      themeMode:        themeMode        ?? this.themeMode,
      reindexMode:      reindexMode      ?? this.reindexMode,
      chatHistoryLimit: chatHistoryLimit  ?? this.chatHistoryLimit,
      showSources:      showSources      ?? this.showSources,
    );
  }

  static ThemeMode _parseThemeMode(String value) {
    switch (value) {
      case 'light': return ThemeMode.light;
      case 'dark':  return ThemeMode.dark;
      default:      return ThemeMode.system;
    }
  }

  static String _themeModeToString(ThemeMode mode) {
    switch (mode) {
      case ThemeMode.light:  return 'light';
      case ThemeMode.dark:   return 'dark';
      default:               return 'system';
    }
  }
}
```

### ✅ Checkpoint

No analysis errors.

---

## Step 16 — Settings Provider

### What

Reads settings from `shared_preferences` on load (instant, no network). Syncs to/from Firestore in background. Exposes a way for the UI to update settings.

### 📁 `lib/features/settings/providers/settings_provider.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:shared_preferences/shared_preferences.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:firebase_auth/firebase_auth.dart';
import 'dart:convert';
import '../models/app_settings.dart';

class SettingsNotifier extends Notifier<AppSettings> {
  static const String _prefsKey = 'app_settings';

  @override
  AppSettings build() {
    // Load from shared_preferences immediately — synchronous
    _loadFromPrefs();
    return AppSettings.defaults();
  }

  Future<void> _loadFromPrefs() async {
    final prefs = await SharedPreferences.getInstance();
    final json  = prefs.getString(_prefsKey);
    if (json != null) {
      final map = jsonDecode(json) as Map<String, dynamic>;
      state = AppSettings.fromMap(map);
    }
    // Also load from Firestore in background for cross-device sync
    _syncFromFirestore();
  }

  Future<void> _syncFromFirestore() async {
    final uid = FirebaseAuth.instance.currentUser?.uid;
    if (uid == null) return;
    try {
      final doc = await FirebaseFirestore.instance
          .collection('users').doc(uid)
          .collection('settings').doc('preferences')
          .get();
      if (doc.exists && doc.data() != null) {
        final settings = AppSettings.fromMap(doc.data()!);
        state = settings;
        _saveToPrefs(settings); // keep local in sync
      }
    } catch (_) {
      // Silently fail — local settings still work
    }
  }

  Future<void> updateTheme(ThemeMode mode) async {
    final updated = state.copyWith(themeMode: mode);
    state = updated;
    await _persist(updated);
  }

  Future<void> updateReindexMode(ReindexMode mode) async {
    final updated = state.copyWith(reindexMode: mode);
    state = updated;
    await _persist(updated);
  }

  Future<void> updateChatHistoryLimit(int limit) async {
    final updated = state.copyWith(chatHistoryLimit: limit);
    state = updated;
    await _persist(updated);
  }

  Future<void> updateShowSources(bool show) async {
    final updated = state.copyWith(showSources: show);
    state = updated;
    await _persist(updated);
  }

  Future<void> _persist(AppSettings settings) async {
    await _saveToPrefs(settings);
    await _saveToFirestore(settings);
  }

  Future<void> _saveToPrefs(AppSettings settings) async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.setString(_prefsKey, jsonEncode(settings.toMap()));
  }

  Future<void> _saveToFirestore(AppSettings settings) async {
    final uid = FirebaseAuth.instance.currentUser?.uid;
    if (uid == null) return;
    try {
      await FirebaseFirestore.instance
          .collection('users').doc(uid)
          .collection('settings').doc('preferences')
          .set(settings.toMap(), SetOptions(merge: true));
    } catch (_) {
      // Silently fail — local prefs already saved
    }
  }
}

final settingsProvider = NotifierProvider<SettingsNotifier, AppSettings>(
  SettingsNotifier.new,
);
```

### ✅ Checkpoint

No analysis errors.

---

## Step 17 — Settings Screen + Toggle Row Molecule

### 📁 `lib/ui/molecules/settings_toggle_row.dart`

```dart
import 'package:flutter/material.dart';
import '../atoms/app_colors.dart';

class SettingsToggleRow extends StatelessWidget {
  final String label;
  final String? description;
  final Widget trailing;
  final VoidCallback? onTap;

  const SettingsToggleRow({
    super.key,
    required this.label,
    this.description,
    required this.trailing,
    this.onTap,
  });

  @override
  Widget build(BuildContext context) {
    final theme = Theme.of(context);
    return InkWell(
      onTap: onTap,
      borderRadius: BorderRadius.circular(12),
      child: Padding(
        padding: const EdgeInsets.symmetric(horizontal: 16, vertical: 12),
        child: Row(
          children: [
            Expanded(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text(label, style: theme.textTheme.bodyLarge),
                  if (description != null) ...[
                    const SizedBox(height: 2),
                    Text(
                      description!,
                      style: theme.textTheme.bodySmall,
                    ),
                  ],
                ],
              ),
            ),
            const SizedBox(width: 12),
            trailing,
          ],
        ),
      ),
    );
  }
}
```

### 📁 `lib/features/settings/screens/settings_screen.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:go_router/go_router.dart';
import '../providers/settings_provider.dart';
import '../models/app_settings.dart';
import '../../../core/constants/app_strings.dart';
import '../../../core/constants/app_routes.dart';
import '../../../ui/molecules/settings_toggle_row.dart';
import '../../../ui/atoms/app_colors.dart';

class SettingsScreen extends ConsumerWidget {
  const SettingsScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final settings = ref.watch(settingsProvider);
    final notifier  = ref.read(settingsProvider.notifier);
    final theme     = Theme.of(context);

    return Scaffold(
      appBar: AppBar(title: const Text(AppStrings.settingsTab)),
      body: ListView(
        children: [
          const SizedBox(height: 8),

          // ── Theme ──────────────────────────────────────────────
          SettingsToggleRow(
            label: AppStrings.theme,
            trailing: DropdownButton<ThemeMode>(
              value: settings.themeMode,
              underline: const SizedBox(),
              items: const [
                DropdownMenuItem(
                  value: ThemeMode.system,
                  child: Text('System')),
                DropdownMenuItem(
                  value: ThemeMode.light,
                  child: Text('Light')),
                DropdownMenuItem(
                  value: ThemeMode.dark,
                  child: Text('Dark')),
              ],
              onChanged: (mode) {
                if (mode != null) notifier.updateTheme(mode);
              },
            ),
          ),

          const Divider(indent: 16, endIndent: 16),

          // ── Reindex Mode ───────────────────────────────────────
          SettingsToggleRow(
            label: AppStrings.reindexMode,
            description: settings.reindexMode == ReindexMode.auto
                ? AppStrings.reindexAuto
                : AppStrings.reindexManual,
            trailing: Switch(
              value: settings.reindexMode == ReindexMode.auto,
              activeColor: AppColors.primary,
              onChanged: (isAuto) {
                notifier.updateReindexMode(
                  isAuto ? ReindexMode.auto : ReindexMode.manual);
              },
            ),
          ),

          const Divider(indent: 16, endIndent: 16),

          // ── Chat History Limit ─────────────────────────────────
          SettingsToggleRow(
            label: AppStrings.chatHistoryLimit,
            trailing: DropdownButton<int>(
              value: settings.chatHistoryLimit,
              underline: const SizedBox(),
              items: const [
                DropdownMenuItem(value: 5,  child: Text('5 messages')),
                DropdownMenuItem(value: 10, child: Text('10 messages')),
                DropdownMenuItem(value: 20, child: Text('20 messages')),
              ],
              onChanged: (limit) {
                if (limit != null) notifier.updateChatHistoryLimit(limit);
              },
            ),
          ),

          const Divider(indent: 16, endIndent: 16),

          // ── Show Sources ───────────────────────────────────────
          SettingsToggleRow(
            label: AppStrings.showSources,
            description: AppStrings.showSourcesDesc,
            trailing: Switch(
              value: settings.showSources,
              activeColor: AppColors.primary,
              onChanged: notifier.updateShowSources,
            ),
          ),

          const Divider(indent: 16, endIndent: 16),

          // ── Profile ────────────────────────────────────────────
          ListTile(
            leading: const Icon(Icons.person_outline),
            title: const Text(AppStrings.profile),
            trailing: const Icon(Icons.chevron_right),
            onTap: () => context.go('${AppRoutes.home}/profile'),
          ),
        ],
      ),
    );
  }
}
```

### ✅ Checkpoint

Settings screen builds. Theme toggle changes the app theme live.

---

## Step 18 — Profile Screen

### 📁 `lib/features/settings/screens/profile_screen.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:go_router/go_router.dart';
import 'package:firebase_auth/firebase_auth.dart';
import '../../auth/providers/auth_provider.dart';
import '../../auth/services/auth_service.dart';
import '../../../core/constants/app_strings.dart';
import '../../../core/constants/app_routes.dart';
import '../../../ui/atoms/app_colors.dart';

class ProfileScreen extends ConsumerWidget {
  const ProfileScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final user  = ref.watch(authProvider).valueOrNull;
    final theme = Theme.of(context);

    return Scaffold(
      appBar: AppBar(title: const Text(AppStrings.profile)),
      body: Padding(
        padding: const EdgeInsets.all(24.0),
        child: Column(
          children: [
            const SizedBox(height: 24),

            // Profile photo
            CircleAvatar(
              radius: 48,
              backgroundImage: user?.photoURL != null
                  ? NetworkImage(user!.photoURL!)
                  : null,
              backgroundColor: AppColors.primary,
              child: user?.photoURL == null
                  ? Text(
                      user?.displayName?.substring(0, 1).toUpperCase() ?? '?',
                      style: const TextStyle(
                        fontSize: 32, color: Colors.white),
                    )
                  : null,
            ),

            const SizedBox(height: 16),

            // Display name
            Text(
              user?.displayName ?? 'Unknown',
              style: theme.textTheme.headlineSmall,
            ),

            const SizedBox(height: 4),

            // Email
            Text(
              user?.email ?? '',
              style: theme.textTheme.bodySmall,
            ),

            const SizedBox(height: 48),

            // Sign out
            SizedBox(
              width: double.infinity,
              child: OutlinedButton.icon(
                onPressed: () async {
                  await ref.read(authServiceProvider).signOut();
                  if (context.mounted) context.go(AppRoutes.login);
                },
                icon: const Icon(Icons.logout, color: AppColors.error),
                label: Text(
                  AppStrings.signOut,
                  style: TextStyle(color: AppColors.error),
                ),
                style: OutlinedButton.styleFrom(
                  padding: const EdgeInsets.symmetric(vertical: 14),
                  side: BorderSide(color: AppColors.error),
                  shape: RoundedRectangleBorder(
                    borderRadius: BorderRadius.circular(12)),
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

### ✅ Checkpoint

Profile screen shows name, photo, email. Sign Out works.

---

## Step 19 — Test Settings

- [ ] Toggle dark mode → app changes theme instantly
- [ ] Toggle reindex mode → description text updates
- [ ] Change chat history limit → value persists after hot restart
- [ ] Navigate to Profile → see Google name, photo, email
- [ ] Sign out from Profile → back to login screen

### ✅ Checkpoint

All settings work. Theme persists after full app restart.

---

---

# PHASE 4 — File Management

---

## Step 20 — MdFile Model

### What

The data class representing one Markdown file. Maps between Dart objects and Firestore documents.

### 📁 `lib/features/files/models/md_file.dart`

```dart
import 'package:cloud_firestore/cloud_firestore.dart';

class MdFile {
  final String    id;
  final String    name;
  final String    storagePath;
  final DateTime  createdAt;
  final DateTime  updatedAt;
  final bool      indexed;
  final DateTime? indexedAt;
  final int       size;

  const MdFile({
    required this.id,
    required this.name,
    required this.storagePath,
    required this.createdAt,
    required this.updatedAt,
    this.indexed   = false,
    this.indexedAt = null,
    this.size      = 0,
  });

  factory MdFile.fromFirestore(DocumentSnapshot doc) {
    final data = doc.data() as Map<String, dynamic>;
    return MdFile(
      id:          doc.id,
      name:        data['name']        as String,
      storagePath: data['storagePath'] as String,
      createdAt:   (data['createdAt']  as Timestamp).toDate(),
      updatedAt:   (data['updatedAt']  as Timestamp).toDate(),
      indexed:     data['indexed']     as bool? ?? false,
      indexedAt:   data['indexedAt'] != null
          ? (data['indexedAt'] as Timestamp).toDate()
          : null,
      size:        data['size'] as int? ?? 0,
    );
  }

  Map<String, dynamic> toFirestore() => {
    'name':        name,
    'storagePath': storagePath,
    'createdAt':   Timestamp.fromDate(createdAt),
    'updatedAt':   Timestamp.fromDate(updatedAt),
    'indexed':     indexed,
    'indexedAt':   indexedAt != null
        ? Timestamp.fromDate(indexedAt!)
        : null,
    'size':        size,
  };

  MdFile copyWith({
    bool?      indexed,
    DateTime?  indexedAt,
    DateTime?  updatedAt,
    int?       size,
  }) {
    return MdFile(
      id:          id,
      name:        name,
      storagePath: storagePath,
      createdAt:   createdAt,
      updatedAt:   updatedAt   ?? this.updatedAt,
      indexed:     indexed     ?? this.indexed,
      indexedAt:   indexedAt   ?? this.indexedAt,
      size:        size        ?? this.size,
    );
  }
}
```

### ✅ Checkpoint

No analysis errors.

---

## Step 21 — File Service

### What

All Firestore and Firebase Storage operations for files. The only place in the app that talks to Firebase for file CRUD.

### 📁 `lib/features/files/services/file_service.dart`

```dart
import 'dart:convert';
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:firebase_storage/firebase_storage.dart';
import 'package:firebase_auth/firebase_auth.dart';
import 'package:uuid/uuid.dart';
import '../models/md_file.dart';

class FileService {
  final _firestore = FirebaseFirestore.instance;
  final _storage   = FirebaseStorage.instance;
  final _uuid      = const Uuid();

  String get _uid => FirebaseAuth.instance.currentUser!.uid;

  CollectionReference get _filesCollection =>
      _firestore.collection('users').doc(_uid).collection('files');

  // ── CREATE ─────────────────────────────────────────────────────────────

  Future<MdFile> createFile(String name, String content) async {
    final fileId      = _uuid.v4();
    final storagePath = 'users/$_uid/files/$fileId.md';
    final now         = DateTime.now();
    final bytes       = utf8.encode(content);

    // Upload content to Firebase Storage
    await _storage.ref(storagePath).putData(
      Uint8ListFromList(bytes),
      SettableMetadata(contentType: 'text/markdown'),
    );

    final file = MdFile(
      id:          fileId,
      name:        name,
      storagePath: storagePath,
      createdAt:   now,
      updatedAt:   now,
      size:        bytes.length,
    );

    // Write metadata to Firestore
    await _filesCollection.doc(fileId).set(file.toFirestore());

    return file;
  }

  // ── READ ───────────────────────────────────────────────────────────────

  // Real-time stream of all files, sorted by updatedAt descending
  Stream<List<MdFile>> watchFiles() {
    return _filesCollection
        .orderBy('updatedAt', descending: true)
        .snapshots()
        .map((snap) => snap.docs.map(MdFile.fromFirestore).toList());
  }

  // Download the raw markdown content of a file
  Future<String> getFileContent(String storagePath) async {
    final bytes = await _storage.ref(storagePath).getData();
    if (bytes == null) return '';
    return utf8.decode(bytes);
  }

  // ── UPDATE ─────────────────────────────────────────────────────────────

  Future<void> updateFile(String fileId, String storagePath,
      String content) async {
    final now   = DateTime.now();
    final bytes = utf8.encode(content);

    // Overwrite file in Storage
    await _storage.ref(storagePath).putData(
      Uint8ListFromList(bytes),
      SettableMetadata(contentType: 'text/markdown'),
    );

    // Update metadata in Firestore
    await _filesCollection.doc(fileId).update({
      'updatedAt': Timestamp.fromDate(now),
      'indexed':   false,  // mark as needing re-index
      'size':      bytes.length,
    });
  }

  // Mark file as indexed (called after successful backend indexing)
  Future<void> markIndexed(String fileId) async {
    await _filesCollection.doc(fileId).update({
      'indexed':   true,
      'indexedAt': Timestamp.fromDate(DateTime.now()),
    });
  }

  // Mark file as not indexed (called when indexing fails)
  Future<void> markNotIndexed(String fileId) async {
    await _filesCollection.doc(fileId).update({'indexed': false});
  }

  // ── DELETE ─────────────────────────────────────────────────────────────

  Future<void> deleteFile(String fileId, String storagePath) async {
    // Delete from Storage first
    try {
      await _storage.ref(storagePath).delete();
    } catch (_) {
      // File may not exist in storage — continue with Firestore delete
    }
    // Delete metadata from Firestore
    await _filesCollection.doc(fileId).delete();
  }
}

// Helper: convert List<int> to Uint8List
Uint8ListFromList(List<int> list) {
  final result = Uint8List(list.length);
  for (int i = 0; i < list.length; i++) result[i] = list[i];
  return result;
}
```

> Note: Import `dart:typed_data` for `Uint8List`.

### ✅ Checkpoint

No analysis errors.

---

## Step 22 — Index Service

### What

Calls the Python backend to index/reindex/delete file vectors. Implements the 3-retry logic. Respects the reindex mode setting.

### 📁 `lib/features/files/services/index_service.dart`

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';
import '../../../services/backend_service.dart';
import '../../settings/providers/settings_provider.dart';
import '../../settings/models/app_settings.dart';
import '../services/file_service.dart';

class IndexService {
  final BackendService _backend;
  final SettingsNotifier _settings;
  final FileService _fileService;

  IndexService(this._backend, this._settings, this._fileService);

  // Index a file — respects reindexMode setting
  // If manual mode, does nothing (UI shows Sync button instead)
  Future<IndexResult> indexIfAuto(String fileId) async {
    if (_settings.state.reindexMode == ReindexMode.manual) {
      return IndexResult.skipped;
    }
    return indexFile(fileId);
  }

  // Force index regardless of mode (used by manual Sync button)
  Future<IndexResult> indexFile(String fileId) async {
    return _retryIndex(() => _backend.post('/index/$fileId'), fileId);
  }

  // Re-index after edit
  Future<IndexResult> reindexFile(String fileId) async {
    return _retryIndex(() => _backend.put('/index/$fileId'), fileId);
  }

  // Delete file vectors from ChromaDB
  Future<void> deleteFileIndex(String fileId) async {
    try {
      await _backend.delete('/index/$fileId');
    } catch (_) {
      // Best-effort delete — don't block the UI
    }
  }

  // 3 retries with exponential backoff: 2s, 4s, 8s
  Future<IndexResult> _retryIndex(
      Future<void> Function() call, String fileId) async {
    int attempt = 0;
    final delays = [2, 4, 8];

    while (attempt < 3) {
      try {
        await call();
        await _fileService.markIndexed(fileId);
        return IndexResult.success;
      } catch (_) {
        if (attempt < 2) {
          await Future.delayed(Duration(seconds: delays[attempt]));
        }
        attempt++;
      }
    }

    await _fileService.markNotIndexed(fileId);
    return IndexResult.failed;
  }
}

enum IndexResult { success, failed, skipped }

final indexServiceProvider = Provider<IndexService>((ref) {
  return IndexService(
    ref.read(backendServiceProvider),
    ref.read(settingsProvider.notifier),
    FileService(),
  );
});
```

### ✅ Checkpoint

No analysis errors.

---

## Step 23 — Index Status Badge Atom

### 📁 `lib/ui/molecules/index_status_badge.dart`

```dart
import 'package:flutter/material.dart';
import '../atoms/app_colors.dart';

enum IndexStatus { indexed, syncing, failed, unsynced }

class IndexStatusBadge extends StatelessWidget {
  final IndexStatus status;
  final VoidCallback? onRetry;

  const IndexStatusBadge({
    super.key,
    required this.status,
    this.onRetry,
  });

  @override
  Widget build(BuildContext context) {
    switch (status) {
      case IndexStatus.indexed:
        return _badge('Indexed ✓', AppColors.success);
      case IndexStatus.syncing:
        return Row(mainAxisSize: MainAxisSize.min, children: [
          SizedBox(
            width: 10, height: 10,
            child: CircularProgressIndicator(
              strokeWidth: 1.5,
              color: AppColors.info,
            ),
          ),
          const SizedBox(width: 4),
          _text('Syncing…', AppColors.info),
        ]);
      case IndexStatus.failed:
        return GestureDetector(
          onTap: onRetry,
          child: _badge('Sync failed — Retry', AppColors.error),
        );
      case IndexStatus.unsynced:
        return GestureDetector(
          onTap: onRetry,
          child: _badge('Sync', AppColors.primary),
        );
    }
  }

  Widget _badge(String label, Color color) {
    return Container(
      padding: const EdgeInsets.symmetric(horizontal: 8, vertical: 3),
      decoration: BoxDecoration(
        color: color.withOpacity(0.12),
        borderRadius: BorderRadius.circular(20),
        border: Border.all(color: color.withOpacity(0.4)),
      ),
      child: _text(label, color),
    );
  }

  Widget _text(String label, Color color) {
    return Text(label,
        style: TextStyle(
            fontSize: 10, color: color, fontWeight: FontWeight.w500));
  }
}
```

### ✅ Checkpoint

Badge renders all four states correctly.

---

## Step 24 — Name Input Dialog Molecule

### 📁 `lib/ui/molecules/name_input_dialog.dart`

```dart
import 'package:flutter/material.dart';
import '../atoms/app_colors.dart';
import '../../core/constants/app_strings.dart';

class NameInputDialog extends StatefulWidget {
  const NameInputDialog({super.key});

  @override
  State<NameInputDialog> createState() => _NameInputDialogState();
}

class _NameInputDialogState extends State<NameInputDialog> {
  final _controller = TextEditingController();
  String? _error;

  void _confirm() {
    final name = _controller.text.trim();
    if (name.isEmpty) {
      setState(() => _error = 'File name cannot be empty');
      return;
    }
    Navigator.of(context).pop(name);
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return AlertDialog(
      title: const Text(AppStrings.newFile),
      content: Column(
        mainAxisSize: MainAxisSize.min,
        children: [
          TextField(
            controller: _controller,
            autofocus: true,
            decoration: InputDecoration(
              hintText: AppStrings.fileNameHint,
              errorText: _error,
              border: OutlineInputBorder(
                borderRadius: BorderRadius.circular(8)),
            ),
            onSubmitted: (_) => _confirm(),
          ),
        ],
      ),
      actions: [
        TextButton(
          onPressed: () => Navigator.of(context).pop(null),
          child: const Text(AppStrings.cancel),
        ),
        ElevatedButton(
          onPressed: _confirm,
          style: ElevatedButton.styleFrom(
            backgroundColor: AppColors.primary,
            foregroundColor: Colors.white,
          ),
          child: const Text(AppStrings.create),
        ),
      ],
    );
  }
}

// Helper function to show the dialog and return the name
Future<String?> showNameInputDialog(BuildContext context) {
  return showDialog<String>(
    context: context,
    builder: (_) => const NameInputDialog(),
  );
}
```

### ✅ Checkpoint

Dialog shows, validates empty name, returns name on confirm, returns null on cancel.

---

## Step 25 — File List Tile Molecule

### 📁 `lib/ui/molecules/file_list_tile.dart`

```dart
import 'package:flutter/material.dart';
import '../atoms/app_colors.dart';
import 'index_status_badge.dart';
import '../../core/constants/app_strings.dart';
import '../../core/utils/date_formatter.dart';
import '../../features/files/models/md_file.dart';

class FileListTile extends StatelessWidget {
  final MdFile file;
  final VoidCallback onEdit;
  final VoidCallback onDelete;
  final VoidCallback? onSync;   // null in auto mode, non-null in manual mode
  final bool isSyncing;

  const FileListTile({
    super.key,
    required this.file,
    required this.onEdit,
    required this.onDelete,
    this.onSync,
    this.isSyncing = false,
  });

  IndexStatus get _status {
    if (isSyncing)    return IndexStatus.syncing;
    if (file.indexed) return IndexStatus.indexed;
    if (onSync != null) return IndexStatus.unsynced; // manual mode
    return IndexStatus.unsynced;
  }

  @override
  Widget build(BuildContext context) {
    final theme = Theme.of(context);

    return Card(
      margin: const EdgeInsets.symmetric(horizontal: 16, vertical: 4),
      child: InkWell(
        onTap: onEdit,
        borderRadius: BorderRadius.circular(12),
        child: Padding(
          padding: const EdgeInsets.all(16),
          child: Row(
            children: [
              // File icon
              Icon(Icons.description_outlined,
                  color: AppColors.primary, size: 20),
              const SizedBox(width: 12),

              // Name + date
              Expanded(
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    Text(
                      file.name,
                      style: theme.textTheme.bodyLarge,
                      maxLines: 1,
                      overflow: TextOverflow.ellipsis,
                    ),
                    const SizedBox(height: 4),
                    Text(
                      DateFormatter.relative(file.updatedAt),
                      style: theme.textTheme.bodySmall,
                    ),
                  ],
                ),
              ),

              const SizedBox(width: 8),

              // Index status badge
              IndexStatusBadge(status: _status, onRetry: onSync),

              const SizedBox(width: 4),

              // Three-dot menu
              PopupMenuButton<String>(
                icon: const Icon(Icons.more_vert, size: 18),
                onSelected: (value) {
                  if (value == 'edit')   onEdit();
                  if (value == 'delete') onDelete();
                },
                itemBuilder: (_) => [
                  const PopupMenuItem(value: 'edit',
                      child: Text(AppStrings.edit)),
                  PopupMenuItem(value: 'delete',
                      child: Text(AppStrings.delete,
                          style: TextStyle(color: AppColors.error))),
                ],
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

### 📁 `lib/core/utils/date_formatter.dart`

```dart
import 'package:intl/intl.dart';

class DateFormatter {
  DateFormatter._();

  // Returns "2 hours ago", "Yesterday", "12 Mar" etc.
  static String relative(DateTime date) {
    final now  = DateTime.now();
    final diff = now.difference(date);

    if (diff.inMinutes < 1)  return 'Just now';
    if (diff.inHours < 1)    return '${diff.inMinutes}m ago';
    if (diff.inHours < 24)   return '${diff.inHours}h ago';
    if (diff.inDays == 1)    return 'Yesterday';
    if (diff.inDays < 7)     return '${diff.inDays} days ago';
    return DateFormat('d MMM').format(date);
  }

  static String full(DateTime date) =>
      DateFormat('d MMM yyyy, HH:mm').format(date);
}
```

### ✅ Checkpoint

File tile renders with all elements. No analysis errors.

---

## Step 26 — File List Organism + Providers

### 📁 `lib/features/files/providers/file_list_provider.dart`

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';
import '../models/md_file.dart';
import '../services/file_service.dart';

final fileServiceProvider = Provider<FileService>((ref) => FileService());

// Stream of all files — updates in real-time from Firestore
final fileListProvider = StreamProvider<List<MdFile>>((ref) {
  return ref.watch(fileServiceProvider).watchFiles();
});
```

### 📁 `lib/ui/organisms/file_list.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:go_router/go_router.dart';
import '../../features/files/providers/file_list_provider.dart';
import '../../features/files/services/index_service.dart';
import '../../features/settings/providers/settings_provider.dart';
import '../../features/settings/models/app_settings.dart';
import '../../features/files/models/md_file.dart';
import '../molecules/file_list_tile.dart';
import '../molecules/name_input_dialog.dart';
import '../../core/constants/app_strings.dart';
import '../../core/constants/app_routes.dart';
import '../atoms/app_colors.dart';

class FileList extends ConsumerStatefulWidget {
  const FileList({super.key});

  @override
  ConsumerState<FileList> createState() => _FileListState();
}

class _FileListState extends ConsumerState<FileList> {
  // Track which files are currently being synced
  final Set<String> _syncingIds = {};

  Future<void> _createFile() async {
    final name = await showNameInputDialog(context);
    if (name == null || !mounted) return;

    // Navigate to editor with null fileId = new file, pass name
    context.go('${AppRoutes.home}/editor', extra: {'fileId': null, 'name': name});
  }

  Future<void> _deleteFile(MdFile file) async {
    final confirmed = await showDialog<bool>(
      context: context,
      builder: (_) => AlertDialog(
        title: const Text(AppStrings.deleteConfirmTitle),
        content: Text('Delete "${file.name}"?\n${AppStrings.deleteConfirmBody}'),
        actions: [
          TextButton(
            onPressed: () => Navigator.pop(context, false),
            child: const Text(AppStrings.cancel),
          ),
          TextButton(
            onPressed: () => Navigator.pop(context, true),
            child: Text(AppStrings.delete,
                style: TextStyle(color: AppColors.error)),
          ),
        ],
      ),
    );

    if (confirmed != true) return;

    final fileService  = ref.read(fileServiceProvider);
    final indexService = ref.read(indexServiceProvider);

    await fileService.deleteFile(file.id, file.storagePath);
    await indexService.deleteFileIndex(file.id);
  }

  Future<void> _syncFile(MdFile file) async {
    setState(() => _syncingIds.add(file.id));
    final indexService = ref.read(indexServiceProvider);
    await indexService.indexFile(file.id);
    if (mounted) setState(() => _syncingIds.remove(file.id));
  }

  @override
  Widget build(BuildContext context) {
    final filesAsync = ref.watch(fileListProvider);
    final settings   = ref.watch(settingsProvider);
    final isManual   = settings.reindexMode == ReindexMode.manual;

    return filesAsync.when(
      loading: () => const Center(child: CircularProgressIndicator()),
      error: (e, _) => Center(child: Text('Error: $e')),
      data: (files) {
        if (files.isEmpty) {
          return Center(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                Icon(Icons.description_outlined,
                    size: 64,
                    color: AppColors.primary.withOpacity(0.3)),
                const SizedBox(height: 16),
                Text(
                  AppStrings.noFilesYet,
                  textAlign: TextAlign.center,
                  style: Theme.of(context).textTheme.bodyMedium,
                ),
              ],
            ),
          );
        }

        return Stack(
          children: [
            ListView.builder(
              padding: const EdgeInsets.only(top: 8, bottom: 80),
              itemCount: files.length,
              itemBuilder: (_, i) {
                final file = files[i];
                return FileListTile(
                  file:      file,
                  isSyncing: _syncingIds.contains(file.id),
                  onEdit:    () => context.go(
                    '${AppRoutes.home}/editor',
                    extra: {'fileId': file.id, 'name': file.name},
                  ),
                  onDelete:  () => _deleteFile(file),
                  onSync:    isManual ? () => _syncFile(file) : null,
                );
              },
            ),

            // FAB
            Positioned(
              right: 16, bottom: 16,
              child: FloatingActionButton(
                onPressed: _createFile,
                backgroundColor: AppColors.primary,
                child: const Icon(Icons.add, color: Colors.white),
              ),
            ),
          ],
        );
      },
    );
  }
}
```

### ✅ Checkpoint

File list renders with empty state and FAB. No errors.

---

## Step 27–29 — Screens + Templates + Editor

### 📁 `lib/ui/templates/main_scaffold.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import '../../features/files/screens/file_list_screen.dart';
import '../../features/chat/screens/chat_screen.dart';
import '../../features/settings/screens/settings_screen.dart';
import '../../core/constants/app_strings.dart';
import '../../core/network/connectivity_service.dart';
import '../molecules/offline_banner.dart';
import '../atoms/app_colors.dart';

class MainScaffold extends ConsumerStatefulWidget {
  const MainScaffold({super.key});

  @override
  ConsumerState<MainScaffold> createState() => _MainScaffoldState();
}

class _MainScaffoldState extends ConsumerState<MainScaffold> {
  int _currentIndex = 0;

  final _screens = const [
    FileListScreen(),
    ChatScreen(),
    SettingsScreen(),
  ];

  @override
  Widget build(BuildContext context) {
    final isOnline = ref.watch(isOnlineProvider);

    return Scaffold(
      body: Column(
        children: [
          if (!isOnline) const OfflineBanner(),
          Expanded(child: _screens[_currentIndex]),
        ],
      ),
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex,
        onTap: (i) => setState(() => _currentIndex = i),
        selectedItemColor: AppColors.primary,
        items: const [
          BottomNavigationBarItem(
            icon: Icon(Icons.description_outlined),
            label: AppStrings.filesTab,
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.chat_outlined),
            label: AppStrings.chatTab,
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.settings_outlined),
            label: AppStrings.settingsTab,
          ),
        ],
      ),
    );
  }
}
```

### 📁 `lib/features/files/screens/file_list_screen.dart`

```dart
import 'package:flutter/material.dart';
import '../../../core/constants/app_strings.dart';
import '../../../ui/organisms/file_list.dart';

class FileListScreen extends StatelessWidget {
  const FileListScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text(AppStrings.filesTab)),
      body: const FileList(),
    );
  }
}
```

### 📁 `lib/ui/templates/editor_scaffold.dart`

```dart
import 'package:flutter/material.dart';
import '../atoms/app_colors.dart';
import '../../core/constants/app_strings.dart';

class EditorScaffold extends StatelessWidget {
  final String title;
  final String initialContent;
  final bool isSaving;
  final void Function(String content) onSave;

  const EditorScaffold({
    super.key,
    required this.title,
    required this.initialContent,
    required this.onSave,
    this.isSaving = false,
  });

  @override
  Widget build(BuildContext context) {
    final controller = TextEditingController(text: initialContent);

    return Scaffold(
      appBar: AppBar(
        title: Text(title),
        actions: [
          if (isSaving)
            const Padding(
              padding: EdgeInsets.all(16),
              child: SizedBox(
                width: 20, height: 20,
                child: CircularProgressIndicator(strokeWidth: 2),
              ),
            )
          else
            TextButton(
              onPressed: () => onSave(controller.text),
              child: Text(
                AppStrings.save,
                style: TextStyle(
                  color: AppColors.primary,
                  fontWeight: FontWeight.bold,
                ),
              ),
            ),
        ],
      ),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: TextField(
          controller: controller,
          maxLines: null,
          expands: true,
          keyboardType: TextInputType.multiline,
          style: const TextStyle(fontFamily: 'monospace', fontSize: 14),
          decoration: const InputDecoration(
            border: InputBorder.none,
            hintText: 'Start writing your note…',
          ),
        ),
      ),
    );
  }
}
```

### 📁 `lib/features/files/screens/editor_screen.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:go_router/go_router.dart';
import '../services/file_service.dart';
import '../services/index_service.dart';
import '../providers/file_list_provider.dart';
import '../../../ui/templates/editor_scaffold.dart';
import '../../../core/constants/app_routes.dart';

class EditorScreen extends ConsumerStatefulWidget {
  final String? fileId;       // null = new file
  final String? initialName;  // name from dialog (new file only)

  const EditorScreen({super.key, this.fileId, this.initialName});

  @override
  ConsumerState<EditorScreen> createState() => _EditorScreenState();
}

class _EditorScreenState extends ConsumerState<EditorScreen> {
  String _content     = '';
  String _title       = '';
  String? _storagePath;
  bool   _isSaving    = false;
  bool   _isLoading   = true;

  @override
  void initState() {
    super.initState();
    _load();
  }

  Future<void> _load() async {
    if (widget.fileId == null) {
      // New file
      _title    = widget.initialName ?? 'Untitled';
      _isLoading = false;
      setState(() {});
      return;
    }

    // Existing file — get metadata from provider, content from Storage
    final files = ref.read(fileListProvider).valueOrNull ?? [];
    final file  = files.firstWhere((f) => f.id == widget.fileId,
        orElse: () => throw Exception('File not found'));

    _title        = file.name;
    _storagePath  = file.storagePath;

    final content = await ref.read(fileServiceProvider)
        .getFileContent(file.storagePath);

    setState(() {
      _content    = content;
      _isLoading  = false;
    });
  }

  Future<void> _save(String content) async {
    setState(() => _isSaving = true);

    try {
      final fileService  = ref.read(fileServiceProvider);
      final indexService = ref.read(indexServiceProvider);

      String fileId;

      if (widget.fileId == null) {
        // Create new file
        final file = await fileService.createFile(_title, content);
        fileId       = file.id;
        _storagePath = file.storagePath;
      } else {
        // Update existing file
        fileId = widget.fileId!;
        await fileService.updateFile(fileId, _storagePath!, content);
      }

      // Index (auto mode will index, manual mode will skip)
      await indexService.indexIfAuto(fileId);

      if (mounted) context.go(AppRoutes.home);
    } catch (e) {
      if (mounted) {
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(content: Text('Error saving: $e')));
      }
    } finally {
      if (mounted) setState(() => _isSaving = false);
    }
  }

  @override
  Widget build(BuildContext context) {
    if (_isLoading) {
      return const Scaffold(
        body: Center(child: CircularProgressIndicator()));
    }

    return EditorScaffold(
      title:          _title,
      initialContent: _content,
      isSaving:       _isSaving,
      onSave:         _save,
    );
  }
}
```

### 📁 `lib/ui/molecules/offline_banner.dart`

```dart
import 'package:flutter/material.dart';
import '../../core/constants/app_strings.dart';
import '../atoms/app_colors.dart';

class OfflineBanner extends StatelessWidget {
  const OfflineBanner({super.key});

  @override
  Widget build(BuildContext context) {
    return Container(
      width: double.infinity,
      color: AppColors.error,
      padding: const EdgeInsets.symmetric(vertical: 6, horizontal: 16),
      child: SafeArea(
        bottom: false,
        child: Row(
          children: [
            const Icon(Icons.wifi_off, color: Colors.white, size: 16),
            const SizedBox(width: 8),
            Expanded(
              child: Text(
                AppStrings.offlineMessage,
                style: const TextStyle(color: Colors.white, fontSize: 12),
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

### 📁 `lib/core/network/connectivity_service.dart`

```dart
import 'package:connectivity_plus/connectivity_plus.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';

final isOnlineProvider = StreamProvider<bool>((ref) {
  return Connectivity().onConnectivityChanged.map(
    (result) => result != ConnectivityResult.none,
  );
});
```

### ✅ Checkpoint — Phase 4 Complete

- [ ] App compiles with no errors
- [ ] File list screen shows with empty state
- [ ] Tap FAB → name dialog → enter name → editor opens with that name
- [ ] Type content → tap Save → file appears in list with "Syncing…" → "Indexed ✓" (backend not yet — shows failed, that's fine)
- [ ] Tap file → editor opens with existing content
- [ ] Edit + save → file updated, updatedAt changes in list
- [ ] Delete file → gone from list
- [ ] Go offline → red banner appears

---

---

# PHASE 5 — Python Backend

---

## Step 31 — Backend Project Structure

### What

Create the FastAPI backend project with all folders and empty files.

```bash
mkdir -p backend/core backend/rag backend/routers backend/services
cd backend

touch main.py requirements.txt Procfile .env.example
touch core/__init__.py core/config.py core/firebase_admin.py core/dependencies.py
touch rag/__init__.py rag/chunker.py rag/embedder.py rag/vector_store.py rag/chat_engine.py
touch routers/__init__.py routers/index_router.py routers/chat_router.py
touch services/__init__.py services/storage_service.py
```

### 📁 `backend/requirements.txt`

```
fastapi==0.115.0
uvicorn[standard]==0.30.6
httpx==0.27.2
firebase-admin==6.5.0
chromadb==0.5.3
sentence-transformers==3.1.1
langchain==0.3.1
langchain-community==0.3.1
python-dotenv==1.0.1
pydantic==2.9.2
```

### 📁 `backend/Procfile`

```
web: uvicorn main:app --host 0.0.0.0 --port $PORT
```

### 📁 `backend/.env.example`

```bash
OPENROUTER_API_KEY=
FIREBASE_CREDENTIALS=
CHROMA_PATH=/chroma_data
EMBEDDING_MODEL=all-MiniLM-L6-v2
LLM_MODEL=google/gemini-flash-1.5
LLM_BASE_URL=https://openrouter.ai/api/v1
TOP_K_RESULTS=4
CHUNK_SIZE=500
CHUNK_OVERLAP=100
MAX_HISTORY_MESSAGES=10
```

Copy to `.env` and fill in real values. Never commit `.env`.

### ✅ Checkpoint

Project structure created. `pip install -r requirements.txt` runs without errors.

---

## Step 32 — Config + Firebase Admin

### 📁 `backend/core/config.py`

```python
import os
from dotenv import load_dotenv

load_dotenv()

def _require(key: str) -> str:
    value = os.getenv(key)
    if not value:
        raise RuntimeError(f"Missing required env var: {key}")
    return value

OPENROUTER_API_KEY    = _require("OPENROUTER_API_KEY")
FIREBASE_CREDENTIALS  = _require("FIREBASE_CREDENTIALS")
CHROMA_PATH           = os.getenv("CHROMA_PATH", "/chroma_data")
EMBEDDING_MODEL       = os.getenv("EMBEDDING_MODEL", "all-MiniLM-L6-v2")
LLM_MODEL             = os.getenv("LLM_MODEL", "google/gemini-flash-1.5")
LLM_BASE_URL          = os.getenv("LLM_BASE_URL", "https://openrouter.ai/api/v1")
TOP_K_RESULTS         = int(os.getenv("TOP_K_RESULTS", "4"))
CHUNK_SIZE            = int(os.getenv("CHUNK_SIZE", "500"))
CHUNK_OVERLAP         = int(os.getenv("CHUNK_OVERLAP", "100"))
MAX_HISTORY_MESSAGES  = int(os.getenv("MAX_HISTORY_MESSAGES", "10"))
```

### 📁 `backend/core/firebase_admin.py`

```python
import json
import firebase_admin
from firebase_admin import credentials
from core.config import FIREBASE_CREDENTIALS

def init_firebase():
    if not firebase_admin._apps:
        cred_dict = json.loads(FIREBASE_CREDENTIALS)
        cred = credentials.Certificate(cred_dict)
        firebase_admin.initialize_app(cred, {
            'storageBucket': cred_dict['project_id'] + '.appspot.com'
        })

# Call this once at startup
init_firebase()
```

### ✅ Checkpoint

Config loads. Firebase initialises without error.

---

## Step 33 — Verify Token Dependency

### 📁 `backend/core/dependencies.py`

```python
from fastapi import Header, HTTPException
import firebase_admin.auth as firebase_auth
import core.firebase_admin  # ensure firebase is initialised

async def verify_token(authorization: str = Header(...)) -> str:
    """
    FastAPI dependency injected into every protected route.
    Verifies the Firebase ID token from the Authorization header.
    Returns the uid on success. Raises 401 on failure.
    """
    if not authorization.startswith("Bearer "):
        raise HTTPException(status_code=401, detail="Missing Bearer token")

    token = authorization.removeprefix("Bearer ")

    try:
        decoded = firebase_auth.verify_id_token(token)
        return decoded["uid"]
    except Exception as e:
        raise HTTPException(status_code=401, detail=f"Invalid token: {e}")
```

### ✅ Checkpoint

Dependency can be imported with no errors.

---

## Step 34 — Storage Service

### 📁 `backend/services/storage_service.py`

```python
from firebase_admin import storage

def download_file(storage_path: str) -> str:
    """
    Downloads a .md file from Firebase Storage and returns its content as a string.
    storage_path: e.g. "users/{uid}/files/{fileId}.md"
    """
    bucket = storage.bucket()
    blob   = bucket.blob(storage_path)
    return blob.download_as_text(encoding='utf-8')
```

### ✅ Checkpoint

Function can be imported. No errors.

---

## Step 35 — Chunker

### 📁 `backend/rag/chunker.py`

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter
from core.config import CHUNK_SIZE, CHUNK_OVERLAP

_splitter = RecursiveCharacterTextSplitter(
    chunk_size=CHUNK_SIZE,
    chunk_overlap=CHUNK_OVERLAP,
    length_function=len,
)

def chunk(text: str) -> list[str]:
    """
    Splits markdown text into overlapping chunks.
    chunk_size=500, chunk_overlap=100 by default.
    Returns a list of text strings.
    """
    if not text.strip():
        return []
    return _splitter.split_text(text)
```

### ✅ Checkpoint

`chunk("hello world " * 100)` returns a list of strings.

---

## Step 36 — Embedder

### 📁 `backend/rag/embedder.py`

```python
from sentence_transformers import SentenceTransformer
from core.config import EMBEDDING_MODEL

# Load model once at module import — not per request
# This takes ~10s on first run (downloads model), then cached
print(f"Loading embedding model: {EMBEDDING_MODEL}")
_model = SentenceTransformer(EMBEDDING_MODEL)
print("Embedding model loaded.")

def embed(texts: list[str]) -> list[list[float]]:
    """
    Converts a list of text strings into embedding vectors.
    Returns a list of float lists (one vector per text).
    """
    if not texts:
        return []
    vectors = _model.encode(texts, convert_to_numpy=True)
    return vectors.tolist()
```

### ✅ Checkpoint

`embed(["hello world"])` returns a list containing one float list.

---

## Step 37 — Vector Store

### 📁 `backend/rag/vector_store.py`

```python
import chromadb
from core.config import CHROMA_PATH

# Persistent client — writes to Railway volume
_client = chromadb.PersistentClient(path=CHROMA_PATH)


def get_or_create_collection(uid: str):
    """
    Gets or creates a ChromaDB collection for this user.
    Collection name = uid → hard isolation between users.
    Created automatically on first call for new users.
    """
    return _client.get_or_create_collection(
        name=uid,
        metadata={"hnsw:space": "cosine"}
    )


def upsert(uid: str, file_id: str, file_name: str,
           chunks: list[str], vectors: list):
    """
    Stores chunks and their vectors in the user's collection.
    Always call delete_by_file_id first to avoid duplicates.
    """
    collection = get_or_create_collection(uid)
    ids = [f"{file_id}_chunk_{i}" for i in range(len(chunks))]
    collection.upsert(
        ids=ids,
        embeddings=vectors,
        documents=chunks,
        metadatas=[{
            "uid":        uid,
            "fileId":     file_id,
            "fileName":   file_name,
            "chunkIndex": i
        } for i in range(len(chunks))]
    )


def delete_by_file_id(uid: str, file_id: str):
    """
    Removes all vectors for a specific file.
    Used before re-indexing and on file delete.
    """
    collection = get_or_create_collection(uid)
    results = collection.get(where={"fileId": file_id})
    if results["ids"]:
        collection.delete(ids=results["ids"])


def search(uid: str, query_vector: list,
           top_k: int = 4, file_ids: list[str] = None) -> list[dict]:
    """
    Finds the most relevant chunks for a query vector.
    file_ids: if provided, only searches within those files.
    Returns list of {chunk, fileName} dicts.
    """
    collection = get_or_create_collection(uid)

    where_filter = None
    if file_ids:
        where_filter = {"fileId": {"$in": file_ids}}

    results = collection.query(
        query_embeddings=[query_vector],
        n_results=min(top_k, collection.count()),
        where=where_filter,
        include=["documents", "metadatas"]
    )

    if not results["documents"][0]:
        return []

    return [
        {"chunk": doc, "fileName": meta["fileName"]}
        for doc, meta in zip(
            results["documents"][0],
            results["metadatas"][0]
        )
    ]
```

### ✅ Checkpoint

Import works. No errors.

---

## Step 38 — Index Router

### 📁 `backend/routers/index_router.py`

```python
from fastapi import APIRouter, Depends
from core.dependencies import verify_token
from services.storage_service import download_file
from rag.chunker import chunk
from rag.embedder import embed
from rag import vector_store
from cloud_firestore import firestore  # for getting storagePath
from firebase_admin import firestore as fs

router = APIRouter()


def _get_storage_path(uid: str, file_id: str) -> str:
    """Get storagePath from Firestore metadata."""
    doc = fs.client().collection('users').document(uid)\
        .collection('files').document(file_id).get()
    if not doc.exists:
        raise ValueError(f"File {file_id} not found in Firestore")
    return doc.to_dict()['storagePath']


def _do_index(uid: str, file_id: str) -> dict:
    storage_path = _get_storage_path(uid, file_id)
    content      = download_file(storage_path)
    file_name    = storage_path.split('/')[-1]  # e.g. "abc123.md"

    chunks  = chunk(content)
    vectors = embed(chunks)

    vector_store.delete_by_file_id(uid, file_id)
    vector_store.upsert(uid, file_id, file_name, chunks, vectors)

    return {"chunks": len(chunks)}


@router.post("/index/{file_id}")
async def index_file(file_id: str, uid: str = Depends(verify_token)):
    result = _do_index(uid, file_id)
    return {"status": "indexed", **result}


@router.put("/index/{file_id}")
async def reindex_file(file_id: str, uid: str = Depends(verify_token)):
    result = _do_index(uid, file_id)
    return {"status": "reindexed", **result}


@router.delete("/index/{file_id}")
async def delete_index(file_id: str, uid: str = Depends(verify_token)):
    vector_store.delete_by_file_id(uid, file_id)
    return {"status": "deleted"}
```

### ✅ Checkpoint

Router imports cleanly. Test with Postman in Step 39.

---

## Step 39 — Test Indexing with Postman

### Setup Postman

1. Get a Firebase ID token: in Flutter, call `await FirebaseAuth.instance.currentUser?.getIdToken()` and print it. Copy the token.
2. In Postman: add header `Authorization: Bearer {your_token}`
3. Run backend locally: `uvicorn main:app --reload`

### Test Flow

- [ ] `POST http://localhost:8000/index/{a_real_file_id}` → `{ "status": "indexed", "chunks": N }`
- [ ] `PUT http://localhost:8000/index/{same_file_id}` → `{ "status": "reindexed", "chunks": N }`
- [ ] `DELETE http://localhost:8000/index/{same_file_id}` → `{ "status": "deleted" }`

### ✅ Checkpoint

All three endpoints return correct responses.

---

## Step 40 — Chat Engine

### 📁 `backend/rag/chat_engine.py`

```python
import httpx
from core.config import OPENROUTER_API_KEY, LLM_BASE_URL, LLM_MODEL

SYSTEM_PROMPT = """You are a helpful assistant for a personal knowledge base.
Answer ONLY using the provided context from the user's notes.
If the context does not contain enough information to answer, say exactly:
"I don't have that in your notes."
Never use your general training knowledge to answer.
Always cite which note the information came from."""


def build_messages(query: str, context_chunks: list[dict],
                   history: list[dict]) -> list[dict]:
    """
    Builds the messages array for the LLM API call.
    """
    # Format context chunks with source labels
    context_text = "\n\n".join(
        f"[From: {c['fileName']}]\n{c['chunk']}"
        for c in context_chunks
    )

    messages = [
        {"role": "system", "content": SYSTEM_PROMPT},
        {"role": "system", "content": f"Context from user's notes:\n\n{context_text}"},
    ]

    # Add conversation history
    messages.extend(history)

    # Add current question
    messages.append({"role": "user", "content": query})

    return messages


async def call_llm(messages: list[dict]) -> dict:
    """
    Calls OpenRouter with the built messages.
    Returns {answer: str, sources: list[str]}.
    """
    async with httpx.AsyncClient(timeout=30.0) as client:
        response = await client.post(
            f"{LLM_BASE_URL}/chat/completions",
            headers={
                "Authorization": f"Bearer {OPENROUTER_API_KEY}",
                "Content-Type": "application/json",
            },
            json={
                "model":    LLM_MODEL,
                "messages": messages,
            },
        )
        response.raise_for_status()
        data = response.json()

    answer = data["choices"][0]["message"]["content"]
    return {"answer": answer}
```

### ✅ Checkpoint

Import works. No errors.

---

## Step 41 — Chat Router

### 📁 `backend/routers/chat_router.py`

```python
from fastapi import APIRouter, Depends
from pydantic import BaseModel
from core.dependencies import verify_token
from core.config import TOP_K_RESULTS, MAX_HISTORY_MESSAGES
from rag.embedder import embed
from rag import vector_store
from rag.chat_engine import build_messages, call_llm

router = APIRouter()


class ChatRequest(BaseModel):
    query:   str
    fileIds: list[str] = []      # empty = search all user's files
    history: list[dict] = []     # list of {role, content} dicts


@router.post("/chat")
async def chat(request: ChatRequest, uid: str = Depends(verify_token)):
    # Embed the query
    query_vector = embed([request.query])[0]

    # Search user's ChromaDB collection
    file_ids = request.fileIds if request.fileIds else None
    chunks   = vector_store.search(
        uid         = uid,
        query_vector = query_vector,
        top_k        = TOP_K_RESULTS,
        file_ids     = file_ids,
    )

    if not chunks:
        return {
            "answer":  "I don't have that in your notes.",
            "sources": []
        }

    # Trim history to limit
    history = request.history[-MAX_HISTORY_MESSAGES:]

    # Build prompt and call LLM
    messages = build_messages(request.query, chunks, history)
    result   = await call_llm(messages)

    # Extract unique source file names
    sources = list(dict.fromkeys(c["fileName"] for c in chunks))

    return {
        "answer":  result["answer"],
        "sources": sources,
    }
```

### 📁 `backend/main.py`

```python
from fastapi import FastAPI
from routers import index_router, chat_router
import core.firebase_admin  # initialises Firebase on import
import rag.embedder         # loads embedding model on import

app = FastAPI(title="Dynamic RAG Backend")

app.include_router(index_router.router)
app.include_router(chat_router.router)


@app.get("/health")
async def health():
    return {"status": "ok"}
```

### ✅ Checkpoint

Server starts: `uvicorn main:app --reload`. `/health` returns `{"status": "ok"}`.

---

## Step 42 — Test Chat with Postman

```json
POST http://localhost:8000/chat
Authorization: Bearer {token}

{
  "query": "What did I write about risks?",
  "fileIds": [],
  "history": []
}
```

Expected: `{ "answer": "Based on your notes...", "sources": ["filename.md"] }`

### ✅ Checkpoint

Chat returns an answer grounded in your indexed content.

---

---

# PHASE 6 — Chat Tab

---

## Step 43 — Chat Message Model

### 📁 `lib/features/chat/models/chat_message.dart`

```dart
import 'package:cloud_firestore/cloud_firestore.dart';

enum MessageRole { user, assistant }

class ChatMessage {
  final String      id;
  final MessageRole role;
  final String      content;
  final List<String> sources;
  final DateTime    timestamp;

  const ChatMessage({
    required this.id,
    required this.role,
    required this.content,
    this.sources  = const [],
    required this.timestamp,
  });

  factory ChatMessage.fromMap(Map<String, dynamic> map, String id) {
    return ChatMessage(
      id:        id,
      role:      map['role'] == 'user'
          ? MessageRole.user
          : MessageRole.assistant,
      content:   map['content'] as String,
      sources:   List<String>.from(map['sources'] ?? []),
      timestamp: (map['timestamp'] as Timestamp).toDate(),
    );
  }

  Map<String, dynamic> toMap() => {
    'role':      role == MessageRole.user ? 'user' : 'assistant',
    'content':   content,
    'sources':   sources,
    'timestamp': Timestamp.fromDate(timestamp),
  };

  bool get isUser => role == MessageRole.user;
}
```

### ✅ Checkpoint

No errors.

---

## Step 44 — Chat Service

### 📁 `lib/features/chat/services/chat_service.dart`

```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:firebase_auth/firebase_auth.dart';
import 'package:uuid/uuid.dart';
import '../models/chat_message.dart';
import '../../../services/backend_service.dart';

class ChatService {
  final BackendService _backend;
  final _uuid     = const Uuid();
  final _firestore = FirebaseFirestore.instance;

  ChatService(this._backend);

  String get _uid => FirebaseAuth.instance.currentUser!.uid;

  DocumentReference get _chatDoc =>
      _firestore.collection('users').doc(_uid)
          .collection('chats').doc('main');

  // Load full chat history from Firestore
  Future<List<ChatMessage>> loadHistory() async {
    final doc = await _chatDoc.get();
    if (!doc.exists) return [];

    final data = doc.data() as Map<String, dynamic>?;
    final msgs = data?['messages'] as List<dynamic>? ?? [];

    return msgs.asMap().entries.map((e) =>
        ChatMessage.fromMap(
            e.value as Map<String, dynamic>,
            e.key.toString()
        )
    ).toList();
  }

  // Send a message — returns assistant response
  Future<Map<String, dynamic>> sendMessage({
    required String query,
    required List<ChatMessage> history,
    required List<String> fileIds,
  }) async {
    final historyMaps = history.map((m) => {
      'role':    m.isUser ? 'user' : 'assistant',
      'content': m.content,
    }).toList();

    return await _backend.post('/chat', body: {
      'query':   query,
      'fileIds': fileIds,
      'history': historyMaps,
    });
  }

  // Save full message list to Firestore
  Future<void> saveMessages(List<ChatMessage> messages) async {
    await _chatDoc.set({
      'updatedAt': Timestamp.fromDate(DateTime.now()),
      'messages':  messages.map((m) => m.toMap()).toList(),
    });
  }

  // Clear all chat history
  Future<void> clearHistory() async {
    await _chatDoc.delete();
  }
}
```

### ✅ Checkpoint

No analysis errors.

---

## Step 45 — Backend Service + Chat Provider

### 📁 `lib/services/backend_service.dart`

```dart
import 'package:dio/dio.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:firebase_auth/firebase_auth.dart';
import '../core/constants/app_strings.dart';

class BackendService {
  late final Dio _dio;

  BackendService() {
    _dio = Dio(BaseOptions(
      baseUrl: AppStrings.backendBaseUrl,
      connectTimeout: const Duration(seconds: 15),
      receiveTimeout: const Duration(seconds: 30),
    ));

    // Interceptor: automatically attach Firebase ID token to every request
    _dio.interceptors.add(
      InterceptorsWrapper(
        onRequest: (options, handler) async {
          final token = await FirebaseAuth.instance.currentUser
              ?.getIdToken();
          if (token != null) {
            options.headers['Authorization'] = 'Bearer $token';
          }
          handler.next(options);
        },
      ),
    );
  }

  Future<Map<String, dynamic>> post(String path,
      {Map<String, dynamic>? body}) async {
    final response = await _dio.post(path, data: body);
    return response.data as Map<String, dynamic>;
  }

  Future<Map<String, dynamic>> put(String path) async {
    final response = await _dio.put(path);
    return response.data as Map<String, dynamic>;
  }

  Future<void> delete(String path) async {
    await _dio.delete(path);
  }
}

final backendServiceProvider = Provider<BackendService>(
  (_) => BackendService());
```

### 📁 `lib/features/chat/providers/chat_provider.dart`

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:uuid/uuid.dart';
import '../models/chat_message.dart';
import '../services/chat_service.dart';
import '../../files/providers/file_list_provider.dart';
import '../../../services/backend_service.dart';

final chatServiceProvider = Provider<ChatService>((ref) {
  return ChatService(ref.read(backendServiceProvider));
});

class ChatNotifier extends Notifier<List<ChatMessage>> {
  final _uuid = const Uuid();
  List<String> _activeFileIds = [];

  @override
  List<ChatMessage> build() {
    _loadHistory();
    return [];
  }

  void setFileFilter(List<String> fileIds) {
    _activeFileIds = fileIds;
  }

  Future<void> _loadHistory() async {
    final messages = await ref.read(chatServiceProvider).loadHistory();
    state = messages;
  }

  Future<void> sendMessage(String query) async {
    // Add user message immediately
    final userMsg = ChatMessage(
      id:        _uuid.v4(),
      role:      MessageRole.user,
      content:   query,
      timestamp: DateTime.now(),
    );
    state = [...state, userMsg];

    try {
      final response = await ref.read(chatServiceProvider).sendMessage(
        query:   query,
        history: state.sublist(
            0, state.length > 1 ? state.length - 1 : 0),
        fileIds: _activeFileIds,
      );

      final assistantMsg = ChatMessage(
        id:        _uuid.v4(),
        role:      MessageRole.assistant,
        content:   response['answer'] as String,
        sources:   List<String>.from(response['sources'] ?? []),
        timestamp: DateTime.now(),
      );

      state = [...state, assistantMsg];
      await ref.read(chatServiceProvider).saveMessages(state);
    } catch (e) {
      final errorMsg = ChatMessage(
        id:        _uuid.v4(),
        role:      MessageRole.assistant,
        content:   'Error: Could not get response. Please try again.',
        timestamp: DateTime.now(),
      );
      state = [...state, errorMsg];
    }
  }

  Future<void> clearHistory() async {
    state = [];
    await ref.read(chatServiceProvider).clearHistory();
  }
}

final chatProvider =
    NotifierProvider<ChatNotifier, List<ChatMessage>>(ChatNotifier.new);
```

### ✅ Checkpoint

No analysis errors.

---

## Step 46–51 — Chat UI Components + Screen

### 📁 `lib/ui/organisms/chat_message_bubble.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_markdown/flutter_markdown.dart';
import '../../features/chat/models/chat_message.dart';
import '../../features/settings/providers/settings_provider.dart';
import '../atoms/app_colors.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';

class ChatMessageBubble extends ConsumerWidget {
  final ChatMessage message;
  const ChatMessageBubble({super.key, required this.message});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final showSources = ref.watch(settingsProvider).showSources;
    final isUser      = message.isUser;
    final theme       = Theme.of(context);

    return Align(
      alignment: isUser ? Alignment.centerRight : Alignment.centerLeft,
      child: Container(
        margin: const EdgeInsets.symmetric(horizontal: 16, vertical: 4),
        constraints: BoxConstraints(
            maxWidth: MediaQuery.of(context).size.width * 0.78),
        child: Column(
          crossAxisAlignment: isUser
              ? CrossAxisAlignment.end
              : CrossAxisAlignment.start,
          children: [
            Container(
              padding: const EdgeInsets.all(12),
              decoration: BoxDecoration(
                color: isUser
                    ? AppColors.primary
                    : theme.colorScheme.surface,
                borderRadius: BorderRadius.circular(16),
                border: isUser
                    ? null
                    : Border.all(color: theme.dividerColor),
              ),
              child: isUser
                  ? Text(
                      message.content,
                      style: const TextStyle(
                          color: Colors.white, fontSize: 14),
                    )
                  : MarkdownBody(
                      data: message.content,
                      styleSheet: MarkdownStyleSheet.fromTheme(theme),
                    ),
            ),

            // Sources
            if (!isUser &&
                showSources &&
                message.sources.isNotEmpty) ...[
              const SizedBox(height: 4),
              Wrap(
                spacing: 4,
                children: message.sources.map((s) => Chip(
                  label: Text(s, style: const TextStyle(fontSize: 10)),
                  padding: EdgeInsets.zero,
                  materialTapTargetSize: MaterialTapTargetSize.shrinkWrap,
                )).toList(),
              ),
            ],
          ],
        ),
      ),
    );
  }
}
```

### 📁 `lib/ui/molecules/chat_input_bar.dart`

```dart
import 'package:flutter/material.dart';
import '../atoms/app_colors.dart';
import '../../core/constants/app_strings.dart';

class ChatInputBar extends StatefulWidget {
  final bool isLoading;
  final bool isOnline;
  final void Function(String) onSend;

  const ChatInputBar({
    super.key,
    required this.onSend,
    this.isLoading = false,
    this.isOnline  = true,
  });

  @override
  State<ChatInputBar> createState() => _ChatInputBarState();
}

class _ChatInputBarState extends State<ChatInputBar> {
  final _controller = TextEditingController();

  void _send() {
    final text = _controller.text.trim();
    if (text.isEmpty) return;
    _controller.clear();
    widget.onSend(text);
  }

  @override
  Widget build(BuildContext context) {
    final disabled = widget.isLoading || !widget.isOnline;

    return Container(
      padding: const EdgeInsets.all(12),
      decoration: BoxDecoration(
        border: Border(
            top: BorderSide(color: Theme.of(context).dividerColor)),
      ),
      child: Row(
        children: [
          Expanded(
            child: TextField(
              controller: _controller,
              enabled: !disabled,
              onSubmitted: (_) => _send(),
              decoration: InputDecoration(
                hintText: AppStrings.chatHint,
                border: OutlineInputBorder(
                    borderRadius: BorderRadius.circular(24)),
                contentPadding: const EdgeInsets.symmetric(
                    horizontal: 16, vertical: 8),
              ),
            ),
          ),
          const SizedBox(width: 8),
          if (widget.isLoading)
            const SizedBox(
              width: 24, height: 24,
              child: CircularProgressIndicator(strokeWidth: 2),
            )
          else
            IconButton(
              onPressed: disabled ? null : _send,
              icon: const Icon(Icons.send),
              color: AppColors.primary,
            ),
        ],
      ),
    );
  }
}
```

### 📁 `lib/ui/organisms/chat_history.dart`

```dart
import 'package:flutter/material.dart';
import '../../features/chat/models/chat_message.dart';
import '../organisms/chat_message_bubble.dart';

class ChatHistory extends StatefulWidget {
  final List<ChatMessage> messages;
  const ChatHistory({super.key, required this.messages});

  @override
  State<ChatHistory> createState() => _ChatHistoryState();
}

class _ChatHistoryState extends State<ChatHistory> {
  final _scrollController = ScrollController();

  @override
  void didUpdateWidget(ChatHistory old) {
    super.didUpdateWidget(old);
    if (widget.messages.length != old.messages.length) {
      WidgetsBinding.instance.addPostFrameCallback((_) {
        if (_scrollController.hasClients) {
          _scrollController.animateTo(
            _scrollController.position.maxScrollExtent,
            duration: const Duration(milliseconds: 300),
            curve: Curves.easeOut,
          );
        }
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      controller: _scrollController,
      padding: const EdgeInsets.symmetric(vertical: 8),
      itemCount: widget.messages.length,
      itemBuilder: (_, i) =>
          ChatMessageBubble(message: widget.messages[i]),
    );
  }
}
```

### 📁 `lib/ui/molecules/file_filter_chip.dart`

```dart
import 'package:flutter/material.dart';
import '../atoms/app_colors.dart';

class FileFilterChip extends StatelessWidget {
  final String label;
  final bool   isSelected;
  final VoidCallback onTap;

  const FileFilterChip({
    super.key,
    required this.label,
    required this.isSelected,
    required this.onTap,
  });

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: onTap,
      child: AnimatedContainer(
        duration: const Duration(milliseconds: 150),
        padding: const EdgeInsets.symmetric(horizontal: 12, vertical: 6),
        decoration: BoxDecoration(
          color: isSelected
              ? AppColors.primary
              : AppColors.primary.withOpacity(0.08),
          borderRadius: BorderRadius.circular(20),
          border: Border.all(
            color: isSelected
                ? AppColors.primary
                : AppColors.primary.withOpacity(0.3),
          ),
        ),
        child: Text(
          label,
          style: TextStyle(
            fontSize: 12,
            color: isSelected ? Colors.white : AppColors.primary,
            fontWeight: FontWeight.w500,
          ),
        ),
      ),
    );
  }
}
```

### 📁 `lib/ui/organisms/chat_filter_bar.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import '../../features/files/providers/file_list_provider.dart';
import '../../features/chat/providers/chat_provider.dart';
import '../../core/constants/app_strings.dart';
import '../molecules/file_filter_chip.dart';

class ChatFilterBar extends ConsumerStatefulWidget {
  const ChatFilterBar({super.key});

  @override
  ConsumerState<ChatFilterBar> createState() => _ChatFilterBarState();
}

class _ChatFilterBarState extends ConsumerState<ChatFilterBar> {
  // Empty = "All Files" selected
  final Set<String> _selectedIds = {};

  void _toggle(String fileId) {
    setState(() {
      if (_selectedIds.contains(fileId)) {
        _selectedIds.remove(fileId);
      } else {
        _selectedIds.add(fileId);
      }
    });
    ref.read(chatProvider.notifier)
        .setFileFilter(_selectedIds.toList());
  }

  void _selectAll() {
    setState(() => _selectedIds.clear());
    ref.read(chatProvider.notifier).setFileFilter([]);
  }

  @override
  Widget build(BuildContext context) {
    final files = ref.watch(fileListProvider).valueOrNull ?? [];
    final indexedFiles = files.where((f) => f.indexed).toList();

    return SizedBox(
      height: 40,
      child: ListView(
        scrollDirection: Axis.horizontal,
        padding: const EdgeInsets.symmetric(horizontal: 12),
        children: [
          FileFilterChip(
            label:      AppStrings.allFiles,
            isSelected: _selectedIds.isEmpty,
            onTap:      _selectAll,
          ),
          ...indexedFiles.map((f) => Padding(
            padding: const EdgeInsets.only(left: 8),
            child: FileFilterChip(
              label:      f.name,
              isSelected: _selectedIds.contains(f.id),
              onTap:      () => _toggle(f.id),
            ),
          )),
        ],
      ),
    );
  }
}
```

### 📁 `lib/features/chat/screens/chat_screen.dart`

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import '../providers/chat_provider.dart';
import '../../../ui/organisms/chat_history.dart';
import '../../../ui/organisms/chat_filter_bar.dart';
import '../../../ui/molecules/chat_input_bar.dart';
import '../../../core/constants/app_strings.dart';
import '../../../core/network/connectivity_service.dart';

class ChatScreen extends ConsumerStatefulWidget {
  const ChatScreen({super.key});

  @override
  ConsumerState<ChatScreen> createState() => _ChatScreenState();
}

class _ChatScreenState extends ConsumerState<ChatScreen> {
  bool _isLoading = false;

  Future<void> _send(String query) async {
    setState(() => _isLoading = true);
    await ref.read(chatProvider.notifier).sendMessage(query);
    if (mounted) setState(() => _isLoading = false);
  }

  @override
  Widget build(BuildContext context) {
    final messages = ref.watch(chatProvider);
    final isOnline  = ref.watch(isOnlineProvider).valueOrNull ?? true;

    return Scaffold(
      appBar: AppBar(
        title: const Text(AppStrings.chatTab),
        actions: [
          IconButton(
            icon: const Icon(Icons.delete_outline),
            tooltip: AppStrings.clearChat,
            onPressed: () => ref.read(chatProvider.notifier).clearHistory(),
          ),
        ],
      ),
      body: Column(
        children: [
          const ChatFilterBar(),
          const Divider(height: 1),
          Expanded(child: ChatHistory(messages: messages)),
          ChatInputBar(
            isLoading: _isLoading,
            isOnline:  isOnline,
            onSend:    _send,
          ),
        ],
      ),
    );
  }
}
```

### ✅ Checkpoint — Phase 6 Complete

- [ ] Chat tab shows empty state
- [ ] Type message → sends → user bubble appears instantly
- [ ] Assistant response appears with sources
- [ ] Filter chips show indexed files
- [ ] Select a file chip → subsequent messages only use that file
- [ ] Clear Chat button clears all messages

---

---

# PHASE 7 — Polish + Deploy

---

## Step 53–56 — Final Polish

By this point offline banner is already wired in from Phase 4. What remains:

### Loading States

All async operations should show a spinner while waiting and never silently fail.

- File save → `isSaving` state in editor (already done)
- File delete → show loading in tile while delete is in progress
- Chat send → `isLoading` state in input bar (already done)

### Error Handling Checklist

- [ ] File create fails → SnackBar with error message
- [ ] File delete fails → SnackBar
- [ ] Index fails after 3 retries → badge shows "Sync failed — Retry"
- [ ] Chat request fails → error bubble in chat
- [ ] Login fails → error text below button

### Firebase Rules

Apply the security rules from DOCUMENTATION.md Section 6 in Firebase Console before deploying.

---

## Step 57 — Deploy Backend to Railway

### Steps

```
1. Create GitHub repo, push backend/ folder
2. Railway → New Project → Deploy from GitHub
3. Select your repo → set Root Directory to "backend"
4. Add Volume:
   Settings → Volumes → Add → Mount path: /chroma_data
5. Add environment variables:
   Settings → Variables → add each from .env.example with real values
6. For FIREBASE_CREDENTIALS: paste the entire service account JSON
   as a single line (minified JSON)
7. Deploy → watch logs for:
   "Loading embedding model..."
   "Embedding model loaded."
   "Application startup complete."
8. Copy the Railway URL
9. Update AppStrings.backendBaseUrl in Flutter to the Railway URL
```

### Verify Persistence

After first deploy, index a file. Then manually redeploy. Then chat about that file. Answer should still work — proving the volume is persisting ChromaDB.

### ✅ Checkpoint

- [ ] Railway logs show clean startup
- [ ] Health check: `GET https://your-app.railway.app/health` → `{"status":"ok"}`
- [ ] Index a file → chat about it → correct answer
- [ ] Redeploy → chat again → still correct answer (volume working)

---

## Step 58–59 — Final End-to-End Tests

### Android (Real Device)

- [ ] Install APK on real Android device
- [ ] Login with Google → onboarding → home
- [ ] Create a file with some content → auto-indexed
- [ ] Chat: ask something from the file → correct grounded answer
- [ ] Edit file (add new content) → reindexed → chat about new content → correct
- [ ] Delete file → gone from list and from ChromaDB
- [ ] Settings: change theme → change reindex to manual → test manual sync
- [ ] Go offline → red banner → all actions disabled → go online → banner gone

### Chrome (Web)

- [ ] Same checklist as above
- [ ] Verify Google Sign-In uses popup (not redirect) on web

---

## ✅ Level 1 Complete

When all checkboxes above are ticked, Level 1 is done:

```
✅ Android app working end-to-end
✅ Web app working end-to-end
✅ Google Sign-In on both platforms
✅ Onboarding shown once on first login
✅ Create / Edit / Delete Markdown files
✅ Files auto-indexed or manually synced (user choice)
✅ Chat grounded in user's own files
✅ Chat filter by specific files
✅ Settings: theme, reindex mode, chat history limit, sources toggle
✅ Profile screen with sign out
✅ Offline banner
✅ Backend deployed on Railway with persistent volume
✅ Per-user isolation: Firestore + Storage rules + ChromaDB collections
```

**Next: Level 2 — Desktop platforms + responsive layout + offline-first + folders**