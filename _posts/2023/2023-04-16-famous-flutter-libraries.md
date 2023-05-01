---
title: Famous Flutter Libraries for Mobile Development
excerpt: Most Widely used Open Source Flutter Libraries

toc: true
toc_sticky: true
tags: [Mobile, Software Engineering, Flutter]
categories:
  - Learning
last_modified_at: 2023-04-16T21:12:00
---

Flutter ?
--------
Flutter is a popular mobile application development framework that enables developers to create high-performance, cross-platform apps for iOS, Android, and the web. One of the biggest advantages of using Flutter is the availability of a wide range of open-source libraries that can help you develop mobile applications more efficiently. In this post, we'll take a closer look at the top three open-source libraries available in Flutter: Flutter Bloc, Provider, and GetX.

Flutter Bloc
---------
Flutter Bloc is a popular state management library for Flutter that uses the BLoC (Business Logic Component) design pattern to provide a predictable and scalable way to manage app state. With Flutter Bloc, you can separate your app's business logic from the UI, making your code more organized and easier to maintain. Flutter Bloc also provides a robust set of tools for managing app state, including streams, events, and transformers.

```
import 'package:flutter_bloc/flutter_bloc.dart';

class CounterBloc extends Bloc<int, int> {
  CounterBloc() : super(0);

  @override
  Stream<int> mapEventToState(int event) async* {
    yield state + event;
  }
}

class CounterPage extends StatelessWidget {
  final CounterBloc counterBloc = CounterBloc();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Counter')),
      body: BlocBuilder<CounterBloc, int>(
        bloc: counterBloc,
        builder: (context, count) {
          return Center(
            child: Text('$count'),
          );
        },
      ),
      floatingActionButton: Column(
        mainAxisAlignment: MainAxisAlignment.end,
        crossAxisAlignment: CrossAxisAlignment.end,
        children: <Widget>[
          FloatingActionButton(
            onPressed: () => counterBloc.add(1),
            child: Icon(Icons.add),
          ),
          SizedBox(height: 8),
          FloatingActionButton(
            onPressed: () => counterBloc.add(-1),
            child: Icon(Icons.remove),
          ),
        ],
      ),
    );
  }
}
```

CounterBloc class that extends the Bloc class. The CounterBloc class manages the state of the counter and emits new states in response to events. 

Flutter Provider
---------
Provider is a lightweight and easy-to-use state management library for Flutter that uses the Provider design pattern. The Provider pattern is a way of managing state that allows you to create "providers" that hold data and provide it to other widgets in the app. Providers are essentially objects that hold data and notify other widgets when that data changes. When a provider notifies other widgets of a change, those widgets can update their UI to reflect the new state.

```
import 'package:flutter/foundation.dart';

class CounterProvider with ChangeNotifier {
  int _count = 0;

  int get count => _count;

  void increment() {
    _count++;
    notifyListeners();
  }

  void decrement() {
    _count--;
    notifyListeners();
  }
}
```

In this example, a `CounterProvider` class that holds an integer `_count` and provides two methods for count. The `notifyListeners` method is called whenever the count changes, which notifies any listeners that the count has changed.

To use the `CounterProvider` in app, we need to wrap our app with a `ChangeNotifierProvider` widget:

```
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ChangeNotifierProvider(
      create: (context) => CounterProvider(),
      child: MaterialApp(
        title: 'Provider Example',
        home: CounterPage(),
      ),
    );
  }
}

class CounterPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final counterProvider = Provider.of<CounterProvider>(context);

    return Scaffold(
      appBar: AppBar(title: Text('Counter')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              '${counterProvider.count}',
              style: Theme.of(context).textTheme.headline4,
            ),
            Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: <Widget>[
                FloatingActionButton(
                  onPressed: () => counterProvider.increment(),
                  tooltip: 'Increment',
                  child: Icon(Icons.add),
                ),
                SizedBox(width: 16),
                FloatingActionButton(
                  onPressed: () => counterProvider.decrement(),
                  tooltip: 'Decrement',
                  child: Icon(Icons.remove),
                ),
              ],
            )
          ],
        ),
      ),
    );
  }
}
```

A `ChangeNotifierProvider` widget and passed in a `CounterProvider` object. then, used the `Provider.of` method to get access to the `CounterProvider` object.

GetX
-------
GetX is a lightweight and easy-to-use state management library for Flutter that provides a range of features such as routing, dependency injection, and reactive programming. The main feature of GetX is the ability to use reactive programming to update the UI when app state changes. Reactive programming allows developers to easily bind UI elements to data, so when the data changes, the UI automatically updates.

```
import 'package:get/get.dart';

class CounterController extends GetxController {
  var count = 0;

  void increment() {
    count++;
    update();
  }

  void decrement() {
    count--;
    update();
  }
}
```

Similar as Provider, GetX also requires an wrapper to use state.

```
import 'package:flutter/material.dart';
import 'package:get/get.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GetMaterialApp(
      title: 'GetX Example',
      home: CounterPage(),
    );
  }
}

class CounterPage extends StatelessWidget {
  final CounterController counterController = Get.put(CounterController());

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Counter')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            GetBuilder<CounterController>(
              builder: (_) => Text(
                '${counterController.count}',
                style: Theme.of(context).textTheme.headline4,
              ),
            ),
            Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: <Widget>[
                FloatingActionButton(
                  onPressed: () => counterController.increment(),
                  tooltip: 'Increment',
                  child: Icon(Icons.add),
                ),
                SizedBox(width: 16),
                FloatingActionButton(
                  onPressed: () => counterController.decrement(),
                  tooltip: 'Decrement',
                  child: Icon(Icons.remove),
                ),
              ],
            )
          ],
        ),
      ),
    );
  }
}
```

You may notice that GetX and Provider have similar approach to access their internal state.

Provider is a dependency injection and state management library that is built on top of the InheritedWidget and ChangeNotifier classes in Flutter. It uses a simple, flexible, and lightweight approach to managing app state, and it's designed to be easy to learn and use.

On the other hand, GetX is a more comprehensive library that provides a range of features beyond state management, such as routing, dependency injection, and reactive programming. GetX uses reactive programming to update the UI when app state changes, which allows developers to easily bind UI elements to data.

While both Provider and GetX can be used for state management in Flutter, GetX may be a better choice for larger, more complex apps that require additional features beyond simple state management. However, Provider may be a better choice for smaller apps or for developers who prefer a simpler approach to state management. Ultimately, the choice between Provider and GetX depends on the specific needs of your app and your personal preferences as a developer.