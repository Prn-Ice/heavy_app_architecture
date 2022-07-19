# Heavy repository

Heavy repositories consist of the following

* Json models
* A client
* A repository contract and implementation
* Model classes
* Interceptors (optional)
* Extensions (optional)

A typical heavy repository will look like 👇

```
my_repository
├── .json_models
│   └── response.jsonc
├── lib
│   ├── my_repository.dart
│   └── src
│       ├── client
│       │   ├── my_repository_client.dart
│       │   └── my_repository_client.g.dart
│       ├── models
│       │   ├── models.dart
│       │   └── user
│       │       ├── data.dart
│       │       ├── data.freezed.dart
│       │       ├── data.g.dart
│       │       ├── user.dart
│       │       ├── user.freezed.dart
│       │       └── user.g.dart
│       ├── i_my_repository.dart
│       └── my_repository.dart
├── README.md
├── analysis_options.yaml
├── pubspec.lock
├── pubspec.yaml
└── test
    └── src
        └── my_repository_test.dart
```

### [Json Models](https://github.com/hiranthaR/Json-to-Dart-Model#convert-from-directory)

**Json models** are a snapshot of a `json response` you expect to recieve from the server. These **json models** are then used to generate the **model classes** used by our repository.

Currently the code is generated using [this](https://github.com/hiranthaR/Json-to-Dart-Model) vscode plugin, but there a plans to create a dart plugin for this.

Say you had an endpoint to fetch the details for a user `/user/me`. An example response could be 👇

```json
{
  "status": "success",
  "message": "",
  "data": {
    "firstName": "prince",
    "lastName": "nna"
  }
}
```

To add this to our json models, simple create a new `.jsonc` file in the `.json_models` folder, for this example I'll go with `user_response.jsonc`, that file would look something like 👇

```json
[
    {
        "__className": "user",
        "__path": "/lib/src/models/user",
        "status": "success",
        "message": "",
        "data": {
            "firstName": "prince",
            "lastName": "nna"
        }
    }
]
```

There are a few differences between this and the original json file

* Its in a list `[]`: Thats because one `.jsonc` file can containe one or more json models.
* We added a `__className`: This tells the generator the name of the model class. We chose user to the model will have a class name `User`.
* We added a `__path`: This tells the generator where we want the model to be generated.

## Client

Clients in this arhitecture are basically dart clases that use [retrofit](https://pub.dev/packages/retrofit) to generate http requests with [dio](https://pub.dev/packages/dio). Clients are instantiated and used exlusively in repositories. An example of a client class 👇

```dart
import 'package:dio/dio.dart';
import 'package:my_repository/my_repository.dart';
import 'package:retrofit/retrofit.dart';

part 'my_repository_client.g.dart';

@RestApi()
abstract class MyRepositoryClient {
  factory MyRepositoryClient(Dio dio, {String baseUrl}) = _MyRepositoryClient;

  @GET('/user/me')
  Future<User> getUser();
}
```

## Repository

Repositories are responsible for communicating with external data sources such as `server` via an `api`, they are also responsible transforming, decoding, caching, etc, the returned data.

Repositories must return either a valid response or a parsed error message to the calling class. For me, this is achieved using the `Either` type from [`fp-dart`](https://pub.dev/packages/fpdart).

Ideally your should have

* one repository abstract class eg `i_my_repository.dart`,
* one concrete implementation of abstract class eg `my_repository.dart`.

Example abstract class 👇

```dart
part of 'my_repository.dart';

/// An interface for MyRepository
abstract class IMyRepository{ 
  static const String cacheKey = '__my_repository__';
  
  /// A description for myUser
  TaskEither<String, User> myUser();
  

  /// Cached data
  Future<User?> get userData;

  /// Stream of cached data
  Stream<User?> get userDataStream;
}
```

Example implementation 👇

```dart
import 'package:dio/dio.dart';
import 'package:fpdart/fpdart.dart';
import 'package:my_repository/my_repository.dart';
import 'package:my_repository/src/client/my_repository_client.dart';
import 'package:rxdart/rxdart.dart';
import 'package:stash/stash_api.dart';

part 'i_my_repository.dart';

/// {@template my_repository}
/// MyRepository description
/// {@endtemplate}
class MyRepository implements IMyRepository { 
  /// {@macro my_repository}
  MyRepository({
    required Cache<User> cache,
    MyRepositoryClient? client,
  })  : _cache = cache,
        _client = client ?? MyRepositoryClient(Dio());

  final Cache<User> _cache;
  final MyRepositoryClient _client;

  @override
  TaskEither<String, User> myUser() {
    //TODO: Add Logic
    return TaskEither<String, User>.tryCatch(
      () async {
        final response = await _client.getUser();
        await _cache.put(IMyRepository.cacheKey, response);

        return response;
      },
      (error, stackTrace) => 'error',
    );
  }

  @override
  Future<User?> get userData {
    return _cache.get(IMyRepository.cacheKey);
  }

  @override
  Stream<User?> get userDataStream async* {
    final initial = await userData;

    yield* _cache
        .on<CacheEntryUpdatedEvent<User>>()
        .map((event) => event.newEntry.value as User?)
        .shareValueSeeded(initial);
  }
}
```

## Models

Models are dart classes that "model" the response we expect or requests we send to the API.

Model callses should have the following properties

* Should support value equality
* Should be serializable
* Models should also contain business logic that express how they are meant to be modified. More on that [here](https://codewithandrea.com/articles/flutter-app-architecture-domain-model/#business-logic-in-the-model-classes).

## Interceptors

Repositories can contain http interceptors, they can be useful if you want to carry out actions in response to certain requests. More information about interceptors can be found [here](../\[Interceptors]\(https:/pub.dev/packages/dio/#interceptors\)).

Interceptors should be located in `src/interceptors/`.

## Extensions

Extension methods are a nice way to add extra utility to our model classes.

Extensions should be located in `src/extensions/`.

## 🧱 Template

Creating heavy repositories manually requires quite a bit of boilerplate, I've created a brick template to help you create one faster.

Installation and usage instructions can be found [here](https://brickhub.dev/bricks/heavy\_repository\_package).
