```dart
import 'package:flutter/material.dart';
import 'package:seafarer/seafarer.dart';

void main() async {
  Routes.createRoutes();
  runApp(App());
}

class App extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Compass Example',
      home: Home(),
      onGenerateRoute: Routes.seafarer.generator(),
      navigatorObservers: [
        SeafarerLoggingObserver(),
      ],
    );
  }
}

class Home extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('First Page'),
      ),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: <Widget>[
            RaisedButton(
              child: Text('Open Second Page'),
              onPressed: () async {
                final response = await Routes.seafarer.navigate<bool>(
                  context,
                  "/secondPage",
                );

                print("Response from SecondPage: $response");
              },
            ),
            RaisedButton(
              child: Text('Open Multi Page (Second and Third)'),
              onPressed: () async {
                final responses =
                    await Routes.Seafarer.navigateMultiple(context, [
                  RouteArgsPair("/secondPage", SecondPageArgs("Multi Page!")),
                  RouteArgsPair("/thirdPage", ThirdPageArgs(10)),
                ]);

                print("Second Page Response ${responses[0]}");
                print("Third Page Response ${responses[1]}");
              },
            ),
          ],
        ),
      ),
    );
  }
}

class SecondPageArgs extends BaseArguments {
  final String text;

  SecondPageArgs(this.text) : assert(text != null);
}

class SecondPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final args = seafarer.arguments<SecondPageArgs>(context);

    return Scaffold(
      appBar: AppBar(
        title: Text('Second Page'),
      ),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: <Widget>[
            Text(args?.text ?? 'Second Page'),
            RaisedButton(
              child: Text('Close Page'),
              onPressed: () {
                Navigator.of(context).pop(true);
              },
            ),
          ],
        ),
      ),
    );
  }
}

class ThirdPageArgs extends BaseArguments {
  final int count;

  ThirdPageArgs(this.count);
}

class ThirdPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final args = seafarer.arguments<ThirdPageArgs>(context);

    return Scaffold(
      appBar: AppBar(
        title: Text('Third Page'),
      ),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: <Widget>[
            Text("Count from args is :${args?.count}"),
            RaisedButton(
              child: Text('Close Page'),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
          ],
        ),
      ),
    );
  }
}

class Routes {
  static final seafarer = seafarer(
    options: seafarerOptions(
      handleNameNotFoundUI: true,
    ),
  );

  static void createRoutes() {
    seafarer
      ..addRoute(seafarerRoute(
        name: "/secondPage",
        defaultArgs: SecondPageArgs('From default arguments!'),
        builder: (context, args) {
          return SecondPage();
        },
      ))
      ..addRoute(seafarerRoute(
        name: "/thirdPage",
        builder: (BuildContext context, BaseArguments args) {
          return ThirdPage();
        },
      ));
  }
}

```