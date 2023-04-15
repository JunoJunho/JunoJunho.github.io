---
title: Flutter Provider를 Stateful Widget에서 사용하기
excerpt: 약간의 삽질을 통해 얻은 Code Snippet

toc: true
toc_sticky: true
tags: [Mobile, Software Engineering, Flutter]
categories:
  - Learning
last_modified_at: 2023-04-15T12:01:00
---

Flutter에 새로운 라이브러리 사용해보기
--------

모바일 개발을 도전하면서 이것저것 시도해보다 보니 Flutter의 State전달 라이브러리인 Provider를 사용하게 되었습니다.

예제를 찾아보니 Stateless Widget은 Usecase도 맞고 예제가 많이 나와있는데, Stateful Widget은 찾기 어려워서 이번기회에 공유합니다.

Example - App
--------

```
class ProviderApp extends StatelessWidget {
  const ProviderApp({super.key});

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    final app = MaterialApp(
        title: 'Provider Example!',
        theme: ThemeData(
          primarySwatch: Colors.blueGrey,
        ),
        home: const MyHomePage(title: "Provider!"),
      );
    return MultiProvider( // Wrap application to use provider in build context
      providers: [
        ChangeNotifierProvider(create: (context) => WidgetSelector(), child: app,)
      ],
      child: app,
    );
  }
}
```

Example - Stateful Widget
```
class MyHomePage extends StatefulWidget {
  const MyHomePage({super.key, required this.title});

  final String title;

  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _selectedIndex = 0;
  List<dynamic> screens = [
    const HomeWidget(),
    const HistoryWidget(),
    const SettingsWidget()
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Provider!'),
      ),
      body: Center(
        child: screens.elementAt(_selectedIndex),
      ),
      bottomNavigationBar: BottomNavigationBar(
        items: const <BottomNavigationBarItem>[
          BottomNavigationBarItem(
            icon: Icon(Icons.home_outlined),
            label: 'Home',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.analytics_outlined),
            label: 'History',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.settings_outlined),
            label: 'Settings',
          ),
        ],
        currentIndex: _selectedIndex,
        selectedItemColor: Colors.amber[800],
        onTap: (int index) {
          // Get Index from Provider with proper one and update the selected Index
          Provider.of<WidgetSelector>(context, listen: false).selectedIndex = index; 
          setState(() {
            // Set actual mobile state
            _selectedIndex = index;
          });
        }
      ),
    );
  }
}
```

Example - Provider
-----------
```
class WidgetSelector extends ChangeNotifier {
  int _selectedIndex = 0;
  int get selectedIndex => _selectedIndex;

  set selectedIndex(int index) {
    _selectedIndex = index;
    notifyListeners();
  }
}
```