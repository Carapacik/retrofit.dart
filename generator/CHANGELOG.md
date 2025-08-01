## 10.0.1

- Fix dependencies versions

## 10.0.0

- Migrate to `Element2`
- Updates minimum supported SDK version to Dart 3.8

## 9.7.0

- Upgrade build_runner to 2.5.4

## 9.6.0

 - Updates minimum supported SDK version to Dart 3.6

## 9.5.0

- Migrate enum value name resolve from `.name` to `.toString()`
  `.name` is pretty limited to in terms of adjusting the value. Having resolve through `.toString()`
  gives you high level of flexibility on changing the resulting request value. Another improvement
  that this fix synchronizes the way of resolving values for individual enum values and for the list
  of entities. Previously individual values where resolved through `.name` and list of enums via `.toString`
  deeper inside `dio` client

## 9.3.0

- Added `@BodyExtra` annotation: Add individual fields to request body without defining complete DTO classes
  - Support for adding dynamic fields to existing request bodies
  - `expand` parameter (default: false) to control object field flattening behavior

  Example(Combine fields):

  ```dart
  @http.POST('/path/')
  Future<String> updateValue(@BodyExtra('id') int id, @BodyExtra('value') String value);
  ```

  Request body result:
  
  ```json
  {"id": 123, "value": "some value"}
  ```

  Example(Combine objects):

  ```dart
  // pseudocode
  class User {
    int userId;
    String userName;
  }

  class Settings {
    int a;
    int b;
  }

  @http.POST('/path/')
  Future<String> updateValue(
    @BodyExtra('user', expand: true) User user, 
    @BodyExtra('settings', expand: true) Settings settings,
  );
  ```

  Request body result:
  
  ```json
  {"userId": 123, "userName": "hhh", "a": 1, "b": 2}
  ```

## 9.2.0

- Update protobuf version to 4.0.0

## 9.1.9

- Fixed issue with `@Part` annotation with toJson() enum values.

  Example:

  ```dart
  import 'package:json_annotation/json_annotation.dart';

  @JsonEnum()
  enum TestEnumWithToJson { 
    @JsonValue('A')
    A('A'), 
    @JsonValue('B')
    B('B'); 

    const TestEnumWithToJson(this.json);
    
    final String? json;

    String? toJson() => json;
  }

  @RestApi()
  abstract class TestModelList {
    @POST('/')
    Future<void> testEnumWithToJsonType(@Part() TestEnumWithToJson enumValue);
  }
  ```

## 9.1.8

- Fixed bug of callAdapter using yield/return incorrectly
- Fixed issue which generated invalid code for the same path parameter appears multiple times.

  Example:

  ```dart
  @GET('/image/{imageType}/{id}/{id}_XL.png')
  Future<HttpResponse<dynamic>> getImage(
    @Path('imageType') ImageType imageType,
    @Path('id') String id,
  );
  ```

## 9.1.7

- Introduced CallAdapters, This feature allows adaptation of a Call with return type R into the type of T.
  e.g. Future<User> to Future<Result<User>>

  Code Example:

```dart
  class MyCallAdapter<T> extends CallAdapter<Future<T>, Future<Either<ApiError, T>>> {
    @override
    Future<Either<ApiError, T>> adapt(Future<T> Function() call) async {
      try {
        final response = await call();
        return Either.right(response);
      }
      catch (e) {
        return Either.left(ApiError(e));
      }
    }
  }

  @RestApi()
  abstract class RestClient {
    factory RestClient(Dio dio, {String? baseUrl}) = _RestClient;

    @UseCallAdapter(MyCallAdapter)
    @GET('/')
    Future<Either<ApiError, User>> getTasks();
  }
```

## 9.1.6

- Update `analyzer`, `dart_style` and `source_gen` dependencies to allow upper versions

## 9.1.5

- Add support for nested object of non-primitive types in `TypedExtras`.

  Example :

  ```dart
  @RestApi()
  abstract class TypedExtrasTest {
    @DummyTypedExtras(
      id: '1234',
      config: Config(
        date: '24-10-2024',
        type: 'analytics',
        shouldReplace: true,
        subConfig: {'date': '24-11-2025'},
      ),
    )
    @GET('path')
    Future<void> list();
  }
  ```

## 9.1.3

- Add support for multiple `TypedExtras`.

  Example :

  ```dart
  @TypedExtrasSubClass(
    id: 'abcd',
    fileType: FileType.json,
    destinations: [Destination.remote]
  )
  @AnotherTypedExtrasSubClass(
    state: 'Ohio',
    destinations: [Destination.remote]
  )
  @http.POST('/path/')
  Future<String> myMethod();
  ```

## 9.1.2

- Support passing Enums into `TypedExtras`.

  Example :

  ```dart
  @TypedExtrasSubClass(
    id: 'abcd',
    fileType: FileType.json,
    destinations: [Destination.remote]
  )
  @http.POST('/path/')
  Future<String> myMethod();
  ```

## 9.1.0

- Added `@TypedExtras` to pass extra options to dio requests using custom annotations.

  Example :

  ```dart
  @TypedExtrasSubClass(
    id: 'abcd',
    count: 5,
    shouldProceed: true,
  )
  @http.POST('/path/')
  Future<String> myMethod(@Extras() Map<String, dynamic> extras);
  ```

## 9.0.0

- Require Dart 3.3
- Fix issue with deprecated `getDisplayString(withNullability: true)`
- Update `analyzer: ^6.5.0`
- Update `lints: ^4.0.0` and fix issues
- Remove `tuple` dependency

## 8.1.0

- Added `@Extras` to pass extra options to dio requests, response, transformer and interceptors.

  Example :

  ```dart
  @http.POST('/path/')
  Future<String> myMethod(@Extras() Map<String, dynamic> extras);
  ```

## 8.0.6

- @useResult

## 8.0.5

- fix [#627](https://github.com/trevorwang/retrofit.dart/issues/627) where generic argument constructors on any class not directly marked with `@JsonSerializable(genericArgumentFactories: true)`

- fix some typos in this changelog.

## 8.0.2

- fix #630 Null check operator used on a null value

## 8.0.1

- Add option class-name to customize the name of retrofit generator

## 8.0.0

- Add protobuf support
- Add PreventNullToAbsent annotation to allow null sent to server

## 7.0.8

- Use `toJson()` instead of `.name` if enums have `toJson()`.

## 7.0.7

- Enums return types generated iterating over the enum values instead of calling `.toJson()` method
- Enums as query parameters generated with `.name` instead of `toJson()`

## 7.0.6

- Fix DateTime.toIso8601String() issue #586

## 7.0.3

- Add support for analyzer 6.0.0

## 7.0.2

- Fixed generation of `DateTime` used as `@Query` parameters (now it is converted to `String` using `toIso8601String`)

## 7.0.1

- Rename `retrofit` to `retrofit_generator` in build.yaml fix for build_runner ^2.4.4
- Add topics

## 7.0.0

- Fix deprecated warnings with analyzer 5.12.0
- **[BREAKING CHANGE]** Require Dart >= 2.19 for new analyzer

## 6.0.0

- **[BREAKING CHANGE]** Update min Dart sdk to 2.18
- Update example
- Fix link in readme

## 5.0.0+1

- Support multi parameter types for headers
- Make data null when it is empty

## 5.0.0

- Update dio to ^5.0.0

## 4.1.2

- add recommended dart lints to project

## 4.1.1

- fix error with code_builder version above 4.3.x

## 4.1.0+1

- use dart doc instead of dartdoc

## 4.1.0

- [BREAKING CHANGE] update analyzer to 4.7.x and Dart version to 2.17

## 4.0.3

- fix null compatibility

## 4.0.1

- fix: Code generation will fail if you use the DioResponseType annotation. #452

## 4.0.0

- [BREAKING CHANGE] update analyzer to 3.x and dart to 2.14

## 3.0.1

- Generate code for Multipart mime-type

## 3.0.0

- Support `Parser.FlutterCompute` for generating data on separate isolates (#408)

## 2.2.0

- rollback to dio from dip_http

## 2.1.0

- replace dio by dio_http

- fix generic use toJson method in Post @Body

- nullable list of int was added to generator

## 2.0.1

- add CacheControl

## 2.0.0-beta3

- Fixed CancelRequest generating invalid code (#322)
- Fixed onSendProgress generating invalid code (#325)

## 2.0.0-beta2

- Update to dio-prev3
- Update to source_gen 1.0.0

## 2.0.0-beta1

- Null safety support

## 1.4.1

- Add support for json serializable generic types

## 1.4.0

- compatible with analyzer 0.40.0

## 1.3.8

- Send list params duplication in multipart

## 1.3.7

- Add support for abstract classes with `toJson` defined in mixin/interface/superclass.
  This approach is used in [freezed](https://pub.dev/packages/freezed) package

## 1.3.6

- Add support of Stream return type.

## 1.3.5

- Add multiple `@Queries` decorators support
- Add serializable objects with `@Queries` decorator support

## 1.3.4

- Add dart json mapper deserialize support

## 1.3.3

- Add content type support to `Part`

## 1.3.1+4

- add workaround for [#143]

## 1.3.1

- Add custom options support to each request
- fix [#132](https://github.com/trevorwang/retrofit.dart/issues/132)

## 1.3.0+1

- Add support to post file as request body

## 1.3.0

- Bumped retrofit_generator's SDK requirement to >=2.6.0

## 1.2.3

- remove query parameter which is null

## 1.2.2

- stringify models in FormData #111

## 1.2.1

- Add `List<File>` support

## 1.2.0

- Add `HttpResponse` to handle the original response

## 1.1.0

- fix `FormUrlEncoded` issue

## 1.0.2

- Add `baseUrl` as a parameter of `RestClient`'s conductor
- Add base test for task api

## 1.0.1

- Add dio response type support

## 1.0.0

- Support dio 3.0

## 0.6.3+1

- Fix #53, Pin version of `dio` to `2.1.16`

## 0.6.3

- [BREAKING CHANGE] Requires `retrofit: ^0.6.3`
- Respect user option `autoCastResponse` (this will skip `.fromJson`)
- Allow custom classes without `.toJson` method (generator will throw warning)

## 0.6.2

- fix: fix bad cast exception (#47)
- add CancelToken, SendProgress, and ReceiveProgress (#46)

## 0.6.1

- Fixed inner type parsing [#44](https://github.com/trevorwang/retrofit.dart/issues/44)

## 0.6.0

- [BREAKING CHANGE] only works dart 2.2.2 and above
- Added support to multiple clients with different base urls.

## 0.5.0

## 0.4.3

- Minor fix

## 0.4.2

- Added support to generate strong mode compatible source code

## 0.4.1

- Added generic auto casting for `Map<String, List<Model>>` or `Map<String, Model>`

## 0.4.0

Added bean class support for `@Body()` annotation.

Here's the example.

```dart
    Future<String> createUser(@Body() User user);
```

```dart
    class User {
        Map<String, dynamic> toJson() => {};
    }
```

> please notice that:
> You have to provide a `toJson()` method to the bean classes to return a `Map<String, dynamic>`.

## 0.3.0

Added support for generic serialization.

> Please note:
>
> It doesn't support nested generics.

## 0.2.3

- Added support for `File` form fields. See example in `retrofit.dart` readme.

## 0.2.2

- bump to 0.2.2

## 0.2.1

- Fixed pub upload issue

## 0.2.0

- Added `@Extra` to pass extra options to dio requests, response, transformer and interceptors.

  Example :

  ```dart
  @http.POST('/path/')
  @dio.Extra({'my_key':'my_value'})
  Future<String> myMethod();
  ```

- Fixed general dart style and code conventions

- Automatically `null` check with `ArgumentError.checkNotNull` for required parameters

- Now SuperClasses can use forwarding/redirecting constructors instead of static `instance()` method

  Example :

  ```dart
  @RestApi(baseUrl: "https://httpbin.org/")
  abstract class RestClient {
      /// Forwarding constructor
      factory RestClient([Dio dio]) = _RestClient;
  }
  ```

## 0.1.0

- fix health issues

## 0.0.1

- init
