# 🏋 Heavy Flutter Architecture

The **heavy** architecture is the architecture used in creating **flutter** apps at **Chigisoft**.

An app architecture defines the boundaries between parts of the app and the responsibilities each part should have

The motivations for this and previous architectures were

* To separate the UI from the business logic: The view should handle as little logic as possible, all others should be handled in controllers, services, repositories or even models.
* To provide a well documented method of operation for mobile developers working on projects at chigisoft.
* To provide an architecture that allows for easy testing and separation of concerns.

The architecture looks a lot like the MVVM pattern, combining concepts from [Stacked](https://pub.dev/packages/stacked), the [GetX Pattern](https://github.com/kauemurakami/getx\_pattern), reso coders [Clean Architecture](https://resocoder.com/2019/08/27/flutter-tdd-clean-architecture-course-1-explanation-project-structure/), Andrea's [Riverpod Architecture](https://codewithandrea.com/articles/flutter-app-architecture-riverpod-introduction/) and the [BloC Architecture](https://bloclibrary.dev/#/architecture).

> This architecture, as of the last edit to this doc has only been implemented using [BloC](https://bloclibrary.dev/#/) so if you're not already familiar with it, head over to the package documentation and get busy 😏.

> This document is still in active development, so you might see some duplicated, jumbled up information. When you do please open a pull request with your correction.

## Directory Structure

```
├── .fvm
├── .vscode
│   ├── extensions.json
│   ├── launch.json
│   └── settings.json
├── android
├── assets
├── ios
├── lib
│   ├── app
│   │   ├── app.dart
│   │   └── view
│   │       └── app.dart
│   ├── bootstrap.dart
│   ├── features
│   │   └── counter
│   │       ├── counter.dart
│   │       ├── cubit
│   │       └── view
│   ├── gen
│   ├── generated_plugin_registrant.dart
│   ├── injection
│   │   ├── injection.dart
│   │   ├── ioc.config.dart
│   │   ├── ioc.dart
│   │   └── module.dart
│   ├── l10n
│   │   ├── arb
│   │   │   ├── app_en.arb
│   │   │   └── app_es.arb
│   │   └── l10n.dart
│   ├── main_development.dart
│   ├── main_production.dart
│   ├── main_staging.dart
│   ├── router
│   │   ├── app_router.dart
│   │   ├── app_router.gr.dart
│   │   └── router.dart
│   ├── utils
│   │   ├── log_utils
│   │   │   ├── log_utils.dart
│   │   │   └── simple_log_printer.dart
│   │   └── utils.dart
│   └── widgets
│       └── widgets.dart
├── packages
│   ├── app_ui
│   ├── auto_route_observer
│   └── bloc_logger
├── test
├── web
├── .pre-commit-config.yaml
├── analysis_options.yaml
├── coverage_badge.svg
├── flutter_native_splash.yaml
├── l10n.yaml
├── LICENSE
├── mason.yaml
├── melos_high_app.iml
├── melos.yaml
├── pubspec.lock
├── pubspec_overrides.yaml
├── pubspec.yaml
└── README.md
```

Your directory structure should look something like this if you follow the setup instructions correctly.

> If you want to see your current directory structure printed out like the one above then CD into the root of your flutter project and run `tree -L 3 -a --dirsfirst`.

## Project Setup

Requirements:

* Flutter 3.x.x
* Dart: Comes bundled with flutter, but if you have trouble using the bundled version, install dart.
* An IDE (Android Studio or VS Code preferred).
* [Mason-CLI](https://pub.dev/packages/mason\_cli).
* [FVM](https://pub.dev/packages/fvm).
* [pre-commit](https://pre-commit.com/)/commitzen
* [Melos](https://pub.dev/packages/melos)

If you're reading this, you should already have Flutter and your IDE of choice installed and working properly. If you are not already using Flutter 3 and sound null-safety, please upgrade and begin to do so.

Setting up a project to use this architecture can take a long time and it involves a lot of boilerplate. To help you and other developers get up to speed quicker, a [mason](https://pub.dev/packages/mason\_cli) starter app template has been created.

To generate a working app from the template, you'll need to have the following installed:

### [Flutter](https://flutter.dev)

You need flutter to run the app ... 🤯.

> To install flutter using fvm 👇
>
> ```
> fvm install stable
> ```
>
> ```
> fvm global stable
> ```
>
> This installs the latest stable version of flutter to `~/fvm/versions/stable`, so add that to your path and you'll be able to use the `flutter` command.

> For reference, this is how I added flutter to my path on [fish](https://fishshell.com/) on a Mac 👇
>
> ```
> # Flutter
> set PATH /Users/myuser/fvm/default/bin $PATH
>
> # Dart
> set PATH /Users/myuser/fvm/default/bin/cache/dart-sdk/bin $PATH
> ```
>
> You'll want to switch `myuser` with your username.

### [Mason-CLI](https://pub.dev/packages/mason\_cli)

The template is a [mason brick](https://pub.dev/packages/mason\_cli#creating-new-bricks), so yeah, you need mason to generate it. Mason lets you create and use dart temlates.

> To install mason-cli 👇
>
> ```
> dart pub global activate mason_cli
> ```

### [FVM](https://pub.dev/packages/fvm)

FVM is a cli for managing your flutter versions. We chose fvm over the default [flutter](https://docs.flutter.dev/get-started/install) installation method because we've had issues in the past with apps that had to stay locked to older versions of flutter. Managing multiple versions of flutter is a lot easier with fvm.

> To install fvm 👇 or check the [site](fvm.app) for more installation instructions
>
> ```
> dart pub global activate fvm
> ```

### [Pre-Commit](https://pre-commit.com/)

Pre-commit makes it easy to add git hooks to your project.

In this project, we have used it to install the [commitzen](https://github.com/commitizen-tools/commitizen) hook. This hook prevents commits that do not follow the [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/) standard.

> To install pre-commit 👇
>
> ```
> sudo pip3 install -U pre-commit
> ```

### [Commitzen](https://github.com/commitizen-tools/commitizen)

Commitzen helps you write commits that conform to the [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/) standard.

> To install commitzen 👇
>
> ```
> sudo pip3 install -U Commitizen
> ```

Example usage 👇

[![asciicast](https://asciinema.org/a/4kLGlSvV9wlrPyNRMuDH7fYWq.svg)](https://asciinema.org/a/4kLGlSvV9wlrPyNRMuDH7fYWq)

### [Melos](https://pub.dev/packages/melos)

This project uses melos for the following:

* It makes it easy to handle nested projects ie in the `packages` folder. The project is a [mono repo](https://en.wikipedia.org/wiki/Monorepo).
* Automatic versioning of packages.
* Automatic changelog generation.
* Easily write scripts that affect all packages, eg test root package and sub packages.

> To install melos 👇
>
> ```
> dart pub global activate melos
> ```

### [Dart Code Metrics](https://dartcodemetrics.dev/)

You don't need. to install this one globally, it's already a part of the generated project. I just thought I'd touch on what it does.

Basically, it adds more rules ... yeah. It adds a few more lint rules on top of those we get from [very\_good\_analysis](https://pub.dev/packages/very\_good\_analysis) which is also a part of the project. I'm talking about things like:

* Maximum function name
* First class name must match file name
* etc

### 🧱 Actual Installation

So, with all of that out of the way, to generate the app run the following 👇

```
mason add -g heavy_app
```

This installs the template system wide. And then finally 👇

```
mason make heavy_app
```

Example usage 👇, ps, it takes a while, around 5 mins so grab a cup of coffee.

[![asciicast](https://asciinema.org/a/HYJbNRUAxhHaB68NJjMbzVqyu.svg)](https://asciinema.org/a/HYJbNRUAxhHaB68NJjMbzVqyu)
