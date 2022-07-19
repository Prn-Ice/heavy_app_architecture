# Heavy Service

A Heavy service is a package that abstracts away reusable functionality eg 

- taking picture from a gallery or the camera
- displaying a loading indicator
- adding and removing files from storage

A typical heavy service looks like 👇

```sh
├── user_service
│   ├── lib
│   │   ├── src
│   │   │   ├── user_service.dart
│   │   │   └── i_user_service.dart
│   │   └── user_service.dart
│   ├── test
│   │   └── src
│   │       └── user_service_test.dart
│   ├── .gitignore
│   ├── analysis_options.yaml
│   ├── pubspec.yaml
│   └── README.md
└── ...
```



## 🧱 Template

Creating heavy services manually requires quite a bit of boilerplate, I've created a brick template to help you create one faster.

Installation and usage instructions can be found [here](https://brickhub.dev/bricks/heavy_service_package).