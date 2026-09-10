# Run API Dash locally

API Dash is a Flutter/Dart monorepo managed with [Melos](https://pub.dev/packages/melos). These instructions are for running the application from source.

## Step 1: Install prerequisites

Install the following tools before cloning the repository:

- Flutter `3.41.2` or newer
- Dart `3.11.0` or newer, included with Flutter
- Git
- The native tooling required by your target platform

The SDK constraints are defined in the root [pubspec.yaml](../../pubspec.yaml). Verify the installation with:

```bash
flutter --version
flutter doctor -v
```

Resolve any required issues reported by `flutter doctor` before continuing. Platform-specific setup is documented in [platform_specific_instructions.md](platform_specific_instructions.md).

## Step 2: Get the source

Fork the repository if you plan to contribute, then clone your fork or the upstream repository:

```bash
git clone https://github.com/<your-account>/apidash.git
cd apidash
```

If the platform directory you want to run is missing, generate it once from the repository root. Replace `<platform>` with `macos`, `windows`, `linux`, `android`, or `ios`:

```bash
flutter create --platforms=<platform> .
```

Do not run this command when the required platform directory already exists unless you understand the native files it may regenerate.

## Step 3: Install dependencies

API Dash contains multiple local Dart/Flutter packages. Install Melos and bootstrap the workspace:

```bash
dart pub global activate melos
melos bootstrap
melos pub-get
flutter pub get
```

If the shell cannot find `melos` after activation, add Dart's global executable directory to your `PATH`, then reopen the terminal.

## Step 4: Complete platform setup

Complete the relevant native setup before the first run:

- **macOS:** Install CocoaPods with `brew install cocoapods`, run `pod setup`, and apply the network and file entitlements described in [platform_specific_instructions.md](platform_specific_instructions.md).
- **Android:** Install an Android SDK/device. Android builds may also require JDK 17 and compatible Gradle/Android Gradle Plugin versions.
- **iOS:** Install Xcode, select its command-line tools, and run CocoaPods setup if required by Flutter.
- **Windows/Linux:** Install the desktop toolchain listed by `flutter doctor -v` and enable the corresponding Flutter desktop platform.

## Step 5: Run the application

List available devices, then run on a specific device when more than one is available:

```bash
flutter devices
flutter run -d <device-id>
```

Examples:

```bash
flutter run -d macos
flutter run -d linux
flutter run -d windows
```

On desktop, API Dash may ask you to choose a workspace folder on first launch. This folder is used for local Hive data.

## Step 6: Verify the setup

Run a focused test or analyze the workspace after the first successful launch:

```bash
flutter test test/widget_test.dart
melos analyze
```

For the complete test workflow, see [testing.md](testing.md).

## Step 7: Troubleshooting

### Dependencies or package links are stale

Run the dependency setup again from the repository root:

```bash
melos bootstrap
melos pub-get
flutter pub get
```

If the build still uses stale artifacts, clean the app and repeat dependency installation:

```bash
flutter clean
flutter pub get
```

### A generated-file error appears

Freezed and JSON serialization outputs are generated files. Regenerate them instead of editing generated files manually:

```bash
dart run build_runner build --delete-conflicting-outputs
```

For all workspace packages, use:

```bash
melos build-gen
```

### No device is available

Run `flutter devices` and confirm that the target platform is enabled. For a desktop target, enable the relevant platform if necessary. For example, to enable macOS:

```bash
flutter config --enable-macos-desktop
```

Use `--enable-linux-desktop` or `--enable-windows-desktop` instead when targeting Linux or Windows.

Then run `flutter doctor -v` again and address the reported native-toolchain issue.

### macOS reports a network permission error

Check CocoaPods and the entitlements in `macos/Runner/DebugProfile.entitlements` and `macos/Runner/Release.entitlements`. The required entries are listed in [platform_specific_instructions.md](platform_specific_instructions.md).

### The app starts but requests fail

Confirm that the request URL is valid and that the platform network permissions are configured. On macOS, missing network entitlements can prevent requests even when the application launches successfully.

### The application does not start after a dependency or platform change

Try the following from the repository root:

```bash
flutter clean
melos bootstrap
melos pub-get
flutter pub get
flutter run -d <device-id>
```

If the problem persists, include the output of `flutter doctor -v`, the target device, and the failing command when opening an issue.
