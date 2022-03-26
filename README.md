summary: Flutter Codelab / State Management in Flutter the Easy, yet Robust Way
id: flutter-codelab-state-mgmt
categories: Flutter
tags: flutter
status: Published 
authors: Roman Jaquez
Feedback Link: https://romanjustcodes.web.app

# Flutter Codelab / State Management in Flutter the Easy, yet Robust Way
<!-- ------------------------ -->
## Overview 
Duration: 0

### What You’ll Learn in this codelab: 
- Multiple levels at which you can manage state in your Flutter apps
  - Using **StatefulWidgets** and the **setState** method to trigger changes
  - Using **ValueNotifier** and **ValueListenableBuilder** 
  - Sharing data between widgets
  - Triggering rebuilds on one widget from another widget
  - "Lifting the State" using **Provider**
  - Leveraging more than one service using **MultiProvider**
  - Communication between provided services
  - Listening to changes and rebuilding using **ChangeNotifierProvider** and **ChangeNotifier** services in conjunction with **Consumer** widgets

![Details Page](assets/flutter-codelab-state-mgmt/img9.png)

![Details Page](assets/flutter-codelab-state-mgmt/state_mgmt9.gif)

## Pre-requisites
Duration: 0
All you need to complete these codelabs is the following:
- A computer with a browser
- An internet connection

Positive
: We'll be using [DartPad](https://dartpad.dev/), a web-based, open-source tool that offers an online playground for learning Dart and Flutter. For more info, visit [this link](https://dart.dev/tools/dartpad).

## Set up your project

First things first, let's set up our project by importing the required packages (we'll be using the **material** package for simplicity).

Add the **main** method, which wraps the **runApp**, which will load the root widget of our app, the **MaterialApp** widget. Set the **debugShowCheckedModeBanner** to **false** and set as the **home** property a new widget we'll be creating called **MyApp**:

```
import 'package:flutter/material.dart';

void main() {
  runApp(
    MaterialApp(
      debugShowCheckedModeBanner: false,
     home: MyApp()
    )
  );
}
```

Now create a custom widget called **MyApp** as a [**StatelessWidget**](https://api.flutter.dev/flutter/widgets/StatelessWidget-class.html).

**StatelessWidgets** are the most basic type of widgets you can build on your own, and they do nothing more than describe part of the user interface by building a constellation of other widgets under it that describe the user interface more concretely, creating a hierarchy. 

Your custom **StatelessWidget** should returns the following:
- a Scaffold widget
- a Center widget as the body of the Scaffold
- a widget called **MyWidget** as the child of the **Center** widget

Your **MyApp** widget should look like this:

```
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: MyWidget(),
      ),
    );
  }
}
```

Still the **MyWidget** doesn't exist, so let's make it also as a **StatelessWidget**. A **StatelessWidget** doesn't maintain any state - its contents are pretty much rendered once on the screen and don't change at all. Let's build this widget as a very simple widget that contains the following:

- an internal property called **count**, type **int**, with an initial value of 0.
- a **Column** widget with a **mainAxisSize** of **MainAxisSize.min**.
- as the column's children, a simple **Text** widget with the content "Hello World, $count!" (notice the interpolated value in the string) and style it using the inherited text theme of **headline4**.

Your code should look as follows:


```
class MyWidget extends StatelessWidget {
  
  final int count = 0;
  
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Text(
          'Hello, World, $count!',
            style: Theme.of(context).textTheme.headline4,
        )
      ]
    );
  }
}
```

Nothing exciting about this widget; it just renders the string "Hello World, 0!" on the screen - that's all it does. If you run this through **DartPad**, you will see the following:

![Details Page](assets/flutter-codelab-state-mgmt/img1.png)

Now, with that in place, let's start diving into State Management, shall we?

### Complete Code for this step

```
import 'package:flutter/material.dart';

void main() {
  runApp(
    MaterialApp(
      debugShowCheckedModeBanner: false,
     home: MyApp()
    )
  );
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: MyWidget(),
      ),
    );
  }
}


class MyWidget extends StatelessWidget {

  final int count = 0;
  
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Text(
          'Hello, World, $count!',
            style: Theme.of(context).textTheme.headline4,
        )
      ]
    );
  }
}
```

## State Management using StatefulWidgets


[**StatefulWidgets**](https://api.flutter.dev/flutter/widgets/StatefulWidget-class.html), in contrast with **StatelessWidgets**, maintain state between widget rebuilds in a separate class called **State**. State is information that (1) can be read synchronously when the widget is built and (2) might change during the lifetime of the widget. It is the responsibility of the widget implementer to ensure that the State is promptly notified when such state changes, using State's method **setState**.

Positive
: Using **StatefulWidgets** and its corresponding **State** is the simplest way you can manage a widget's internal state, while bringing interactivity to a widget on the cheap, and a way to encapsulate changes to only this widget.  The only drawback is that the State only belongs to this widget and this widget only.

![Details Page](assets/flutter-codelab-state-mgmt/img2.png)

Let's now turn our **StatelessWidget** **MyWidget** into a **StatefulWidget**.

Start by replacing **StatelessWidget** by **StatefulWidget** from the **extends** clause in the **MyWidget** class, and override the **createState** method, which creates the widget's corresponding state. Name it by convention **MyWidgetState**, as such:

```
class MyWidget extends StatefulWidget {
  
  @override
  MyWidgetState createState() => MyWidgetState();
}
```

Then, create the corresponding state class (**MyWidgetState**) and move both the **build** method and the **count** property to this new class; remove the **final** attribute from the **count** property as the state will modify it now.

Your updated class from **StatelessWidget** to **StatefulWidget** should look like this:

```
class MyWidget extends StatefulWidget {
  
  @override
  MyWidgetState createState() => MyWidgetState();
}

class MyWidgetState extends State<MyWidget> {
  int count = 0;
  
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Text(
          'Hello, World, $count!',
            style: Theme.of(context).textTheme.headline4,
        )
      ]
    );
  }
}
```

Now let's bring in some interactivity. 

Add a **TextButton** widget to the **Column** widget, under the **Text** widget, with the following specs:
- a **Text** widget as its direct child, with the label "Click me to update!"
- an **onPressed** event; inside this event, then call the **setState** method, available to the **State**, and inside of which you'll increment the **count** by one everytime someone presses it.

Your updated **MyWidgetState** class should look like this:

```
class MyWidgetState extends State<MyWidget> {
  int count = 0;
  
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Text(
          'Hello, World, $count!',
            style: Theme.of(context).textTheme.headline4,
        ),
        TextButton(
          onPressed: () {
            setState(() {
              count++;
            });
          },
          child: const Text('Click me to update!')
        )
      ]
    );
  }
}
```

Running this code through **DartPad** should get you the following output:

![Details Page](assets/flutter-codelab-state-mgmt/state_mgmt1.gif)

And this is pretty much what happens:

![Details Page](assets/flutter-codelab-state-mgmt/img3.png)

Upon calling **setState** on the **State** class after updating the **count** property, this notifies the framework that the widget needs to rebuild itself in order to reflect the changed occurred on the state; Flutter then calls the widget's **build** method and the UI gets repainted with the update.

Positive
: You can even update the **count** property first, then call the **setState** method with an empty callback, and it would still work, but the norm is to perform the state change in the **setState**'s callback method.


### Complete Code for this section

```
import 'package:flutter/material.dart';

void main() {
  runApp(
    MaterialApp(
      debugShowCheckedModeBanner: false,
     home: MyApp()
    )
  );
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: MyWidget(),
      ),
    );
  }
}

class MyChildWidget extends StatelessWidget {
  
  final Function? onWidgetClicked;
  
  const MyChildWidget({ this.onWidgetClicked });
  
  @override
  Widget build(BuildContext context) {
    
    return TextButton(
      onPressed: () {
        onWidgetClicked!();
      },
      child: const Text('Click me to update!')
    );
  }
}


class MyWidget extends StatefulWidget {
  
  @override
  MyWidgetState createState() => MyWidgetState();
}

class MyWidgetState extends State<MyWidget> {
  int count = 0;
  
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Text(
          'Hello, World, $count!',
            style: Theme.of(context).textTheme.headline4,
        ),
        TextButton(
          onPressed: () {
            setState(() {
                  count++;
                });
          },
          child: const Text('Click me to update!')
        )
      ]
    );
  }
}

```

## Triggering a widget rebuild from another child widget

Right now I showed you how you trigger the widget rebuild by calling **setState** from inside the same widget, but what if you want to trigger the **setState** from a child down the hierarchy (i.e. a child widget)? 

One way to do it is using a callback method that you feed into your child widget, which will trigger the connected event up the hierarchy.

Negative
: I'm only showing this approach as a way for you to see how this can also be accomplished, but sometimes this is not the best approach, because this tightens the relationship between the parent and child widgets and you should always strive for a decoupled relationship between widgets, unless stricly necessary. Be aware.

So this is what we'll try to accomplish: 

![Details Page](assets/flutter-codelab-state-mgmt/img4.png)

Let's create a new custom widget called **MyChildWidget** that extends **Stateless** (this one won't persist any state, just trigger an event up the chain) with the following specs:

- a constructor parameter type **Function** called **onWidgetClicked**, which will serve as the function pointer we'll be passing down to this widget for it to be invoked
- move the **TextButton** from the **MyWidget** to this **MyChildWidget**'s **build** method. Inside the **onPressed** event, call the **onWidgetClicked** callback.

Your newly created widget should look like this:

```
class MyChildWidget extends StatelessWidget {
  
  final Function? onWidgetClicked;
  const MyChildWidget({ this.onWidgetClicked });
  
  @override
  Widget build(BuildContext context) {
    
    return TextButton(
      onPressed: () {
        onWidgetClicked!();
      },
      child: const Text('Click me to update!')
    );
  }
}
```

Now go back to the **MyWidget** widget class, and right where the **TextButton** was, now add our newly created widget **MyChildWidget**, and while instantiating it, hook up a callback to its **onWidgetClicked** constructor parameter. Inside, then is when you'll call the **setState** method to increment the **count** property.

Your updated **MyWidget** class should look like this:

```
class MyWidgetState extends State<MyWidget> {
  int count = 0;
  
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Text(
          'Hello, World, $count!',
            style: Theme.of(context).textTheme.headline4,
        ),
        MyChildWidget(
          onWidgetClicked: () {
            setState(() {
              count++;
            });
          }
        )
      ]
    );
  }
}
```

Recap: We created a new widget called **MyChildWidget** which is a child of the **MyWidget** widget. We pass into it a callback method via the **onWidgetClicked** parameter. From inside the **MyChildWidget**, on the **TextButton**'s onPressed method, we invoke this function, which calls up to the parent and on behalf of itself it triggers the **setState** method.

This causes a chain reaction, causing the whole hierarchy (including the **MyChildWidget**) to rebuild, because it is a child of the parent widget **MyWidget** which just got rebuilt.

If you run this on **DartPad** you should not be seeing any differences in behavior, all we're doing is calling the **setState** method from inside the hierarchy.

This approach is not recommended as you can easily fall into the "callback-hell" trap, so avoid it, or use it with caution and consciously.

### Confirm that the state is encapsulated to the MyWidget widget

Now let's prove that the state within a **StatefulWidget** instance is encapsulated within itself and not shared across several instances.

Let's refactor our structure to add more than one **MyWidget** instance.

Back to our **MyApp** widget, replace the **body** of the **Scaffold** (currently a **Center** widget that encloses our **MyWidget**) by a **Column** widget with two equaly spaced out regions (using **Expanded** widgets), each of which encapsulates a **Center** widget with our **MyWidget** widget inside, as such:

```
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        children: [
          Expanded(
            child: Center(
              child: MyWidget()
            )
          ),
          Expanded(
            child: Center(
              child: MyWidget()
            )
          ),
        ]
      ),
    );
  }
}
```

Run this through **DartPad** and you should have the following output:

![Details Page](assets/flutter-codelab-state-mgmt/state_mgmt2.gif)

Notice how each state is maintained separate per widget, not colliding with each other and encapsulated within itself. Neat!

Cool, but what if now I want to share the same state across both widget instances, so they both know about the same count, and be able to incrementally in a coordinated fashion? Let's see that on our next step.

### Complete Code for this step

```
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}



class MyApp extends StatelessWidget {
  
  final ValueNotifier<int> count = ValueNotifier(0);
  
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      home: Scaffold(
        body: Column(
          children: [
            Expanded(
              child: Center(
                child: MyWidget(count: count)
              )
            ),
            Expanded(
              child: Center(
                child: MyWidget(count: count)
              )
            ),
          ]
        ),
      ),
    );
  }
}

class MyChildWidget extends StatelessWidget {
  
  final Function? onWidgetClicked;
  
  const MyChildWidget({ this.onWidgetClicked });
  
  @override
  Widget build(BuildContext context) {
    
    return TextButton(
      onPressed: () {
        onWidgetClicked!();
      },
      child: const Text('Click me to update!')
    );
  }
}

class MyWidget extends StatefulWidget {
  
  final ValueNotifier? count;
  
  const MyWidget({ this.count });
  
  @override
  MyWidgetState createState() => MyWidgetState();
}

class MyWidgetState extends State<MyWidget> {
  
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        ValueListenableBuilder(
          valueListenable: widget.count!,
          builder: (context, value, child) {
            return Text(
              'Hello, World, $value!',
              style: Theme.of(context).textTheme.headline4,
            );
          }
        ),
        MyChildWidget(
          onWidgetClicked: () {
            setState(() {
              widget.count!.value++;
            });
          }
        )
      ]
    );
  }
}
```

## Lifting the State: ValueNotifier and ValueListenableBuilder

Now we'll explore the concept of "lifting the state". As you may have noticed in the previous sections, the state is encapsulated within each widget via their corresponding **State** classes, which persists the values they are interested in preserving across multiple widget rebuilds. This time we will take out the state from inside the widget and "elevate" it, or "lift" it to a higher level, that way multiple widgets can access it, as opposed to being encapsulated, or trapped within a single widget, hence the term "lift".

For this we will use some constructs provided by **Flutter** out of the box called **ValueNotifier** and the **ValueListenableBuilder** widget.

[**ValueNotifier**](https://api.flutter.dev/flutter/foundation/ValueNotifier-class.html) is class that holds a single value; when its value is replaced with something that is not equal to the old value, this class notifies any entity that is listening, thus causing an action, such as rebuilding.

One companion widget to the **ValueNotifier** is the [**ValueListenableBuilder**](https://api.flutter.dev/flutter/widgets/ValueListenableBuilder-class.html), which as the name implies, it "listens" for a value, and when it changes, it rebuilds its contents. This class automatically registers itself as a listener to a **ValueListenable** (i.e. a **ValueNotifier** object) and call its corresponding **builder** method with updated values when the value changes.

Let's see a schematic of how we'll do it as a way to lift the state out of the **MyWidget** widget:

![Details Page](assets/flutter-codelab-state-mgmt/img5.png)

Now let's do some refactoring. We won't be using **StatefulWidgets** anymore as the state won't be encapsulated within a single widget. We'll move the state to a higher level, from the **MyWidget** widget to the **MyApp** widget, that way the app can serve the listener down the hierarchy.

### Updating the MyWidget widget

Let's start from the **MyWidget** widget.

Make them back into a **StatelessWidget** by removing the **MyWidgetState** class portion and rename it back to **MyWidget**.

Create a **final** property called **count**, type **ValueNotifier** of type **int** and its corresponding constructor. This means we'll be feeding a **ValueNotifier** instance to be listened to.

```

final ValueNotifier<int>? count;
const MyWidget({ this.count });

```

Inside the **build** method, wrap the existing **Text** widget inside a **ValueListenableBuilder**, type **int**, with the following attributes:

- valueListenable: the **ValueListenable** instance (in our case, a **ValueNotifier** - count)
- builder: the callback that will be triggered upon changes to the count **ValueNotifier**


```flutter

ValueListenableBuilder<int>(
  valueListenable: count!,
  builder: (context, value, child) {
    return Text(
      'Hello, World, $value!',
      style: Theme.of(context).textTheme.headline4,
    );
  }
)

```

Inside the **MyChildWidget**'s **onWidgetClicked** method, get rid of the **setState** call, and instead, just increment the **value** property of the **count** **ValueNotifier**, which will increment its value and thus notify any listener about the change:

```
MyChildWidget(
  onWidgetClicked: () {
    count!.value++;
  }
)
```

The whole updated **MyWidget** class should look like this:

```
class MyWidget extends StatelessWidget {
  
  final ValueNotifier<int>? count;
  const MyWidget({ this.count });
  
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        ValueListenableBuilder<int>(
          valueListenable: count!,
          builder: (context, value, child) {
            return Text(
              'Hello, World, $value!',
              style: Theme.of(context).textTheme.headline4,
            );
          }
        ),
        MyChildWidget(
          onWidgetClicked: () {
           count!.value++;
          }
        )
      ]
    );
  }
}
```

###  Updating the MyApp Widget

Let's go back now up to the **MyApp** widget, and create a new **final** property, called **count** type **ValueNotifier** type **int**, initialized to 0:

```
final ValueNotifier<int> count = ValueNotifier(0);
```

Now, inside the body, go to each **MyWidget** instance and feed this **count** **ValueNotifier** to each of those instances via their constructor, as such:

```
Expanded(
  child: Center(
    child: MyWidget(count: count)
  )
)
```

The whole updated **MyApp** class should look like this:

```
class MyApp extends StatelessWidget {
  
  final ValueNotifier<int> count = ValueNotifier(0);
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
        body: Column(
          children: [
            Expanded(
              child: Center(
                child: MyWidget(count: count)
              )
            ),
            Expanded(
              child: Center(
                child: MyWidget(count: count)
              )
            )
          ]
        ),
      );
  }
}
```

Running this through DartPad, now notice how by clicking on either one of the widgets, the value changes on both sides, keeping both widgets in sync; this means both are able to listen to the same value, and respond to changes triggered by either one of them. Pretty cool!

![Details Page](assets/flutter-codelab-state-mgmt/state_mgmt3.gif)

### Complete Code for this section

```
import 'package:flutter/material.dart';

void main() {
  runApp(
    MaterialApp(
      debugShowCheckedModeBanner: false,
      home: MyApp()
    )
  );
}

class MyApp extends StatelessWidget {
  
  final ValueNotifier<int> count = ValueNotifier(0);
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
        body: Column(
          children: [
            Expanded(
              child: Center(
                child: MyWidget(count: count)
              )
            ),
            Expanded(
              child: Center(
                child: MyWidget(count: count)
              )
            )
          ]
        ),
      );
  }
}

class MyChildWidget extends StatelessWidget {
  
  final Function? onWidgetClicked;
  
  const MyChildWidget({ this.onWidgetClicked });
  
  @override
  Widget build(BuildContext context) {
    
    return TextButton(
      onPressed: () {
        onWidgetClicked!();
      },
      child: const Text('Click me to update!')
    );
  }
}

class MyWidget extends StatelessWidget {
  
  final ValueNotifier<int>? count;
  const MyWidget({ this.count });
  
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        ValueListenableBuilder<int>(
          valueListenable: count!,
          builder: (context, value, child) {
            return Text(
              'Hello, World, $value!',
              style: Theme.of(context).textTheme.headline4,
            );
          }
        ),
        MyChildWidget(
          onWidgetClicked: () {
           count!.value++;
          }
        )
      ]
    );
  }
}

```

## Lifting the State: Using Provider

Now that we've decoupled our state from the widgets that consume it, what if now I want to lift the state waaaaaaayyyy higher, let's say I want to share it across pages? That'd be pretty cool, that way the values can be shared not only across a handful of widgets but the whole application! Here comes **Provider** to the rescue!

**Provider** is a simple app state management approach that allows you to decouple the business logic that powers your application from the UI rendering logic that makes Flutter such an amazing framework to build dynamic and fluid UIs. Read up more on [**Provider**](https://docs.flutter.dev/development/data-and-backend/state-mgmt/simple) to get more info.

This functionality is conveniently packaged into a single package called **provider**, and in essence is nothing more than a wrapper around **InheritedWidget**, **InheritedNotifier** and **InheritedModel**, and more. 

Positive
: I won't be covering those here, because they are a bit low-level for what we're trying to do, and you may never have to use them in your apps only for very niche cases.

This is what we'll be accomplishing by using the **Provider**:

![Details Page](assets/flutter-codelab-state-mgmt/img6.png)

### Creating the Service

First off, we'll be creating a class that will hold the values that we're interested in persisting outside of the widgets and across widget rebuilds. For this, you just create a regular class with properties in it - we'll call it **ValueService** as it will be serving the purpose of encapsulating not only values, but functionality - sort of like a service, being "provided" by the application.

Whenever any interested entity down the hierarchy wants to have access to it, it uses the provided **BuildContext** from the framework, and search up the hierarchy until it finds the first reference of the provided service, using the **Provider.of** utility method to fetch it, provided the type of instance, the context, and an optional **listen** flag, as in:

```
ValueService valueService = Provider.of<ValueService>(context);
```

Let's start!

Import the required package (provider) at the top of the file:

```
import 'package:provider/provider.dart';
```

Create the **ValueService** class that will hold our value:

```
class ValueService {
  int count = 0;
}
```

Now, let's refactor the **main** method. Wrap the existing **Material** app widget inside the **runApp** method inside a **Provider**, with the following specs:
- set its **create** property to be a callback that returns an instance of the service to be provided (in our case, **ValueService**)
- child: The child that will serve as the starting point through which the instance of the provided services will be tricked down, in our case the **MaterialApp**, as below:

```flutter

void main() {
  runApp(
    Provider( 
      create: (_) => ValueService(),
      child: MaterialApp(
        debugShowCheckedModeBanner: false,
        home: MyApp()
      )
    )
  );
}
```

###  Updating the MyWidget widget

Let's clean up in here and get rid of the **ValueNotifier** called **count** altogether, along with its corresponding constructor parameter.

Instead, in order to differentiate the widget, let's add a **label** property, type **String** that will hold the label for the widget so we can uniquely identify it and make it more reusable.

```
final String? label;
const MyWidget({ this.label });
```

Inside the **build** method, since this is a descendant widget, you can access the provided service at the top by using the supplied **BuildContext** and using the **Provider.of** utility method to pull the service, as such:

```
ValueService valueService = Provider.of<ValueService>(context, listen: false);
```

Now that a reference to the service has been fetched, let's consume it. Replace the **ValueListenableBuilder** from the widget structure (we don't need it anymore) and replace it by just the **Text** widget that is inside of it. Replace the content of the **Text** widget by the provided **label** and interpolate the value from the service in the string, as shown below:

```
Text(
  '${label!}, ${valueService.count}',
  style: Theme.of(context).textTheme.headline4,
),
```

Next, inside the **MyChildWidget**'s callback method **onWidgetClicked()**, is where now we'll update the value of **count** from the **ValueService** reference by incrementing it:

```
MyChildWidget(
  onWidgetClicked: () {
    valueService.count++;
  }
)
```

Now this widget is ready to engage with the provided service. We'll come back in just a minute to do some additional adjustments, but for now we're all set.

Updated full **MyWidget** widget should look like this:

```flutter

class MyWidget extends StatelessWidget {
  
  final String? label;
  const MyWidget({ this.label });
  
  @override
  Widget build(BuildContext context) {
    
    var valueService = Provider.of<ValueService>(context, listen: false);
    
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Text(
          '${label!}, ${valueService.count}',
          style: Theme.of(context).textTheme.headline4,
        ),
        MyChildWidget(
          onWidgetClicked: () {
            valueService.count++;
          }
        )
      ]
    );
  }
}

```

### Updating the MyApp widget

With that in place, let's go back up to the **MyApp** widget, and update the **MyWidget** instances by supplying a unique label to each; to the top **MyWidget** instance, set the **label** property to "Top Widget", and to the second one set it to "Bottom Widget", as such:

```flutter

  Expanded(
    child: Center(
      child: MyWidget(
        label: 'Top Widget')
    )
  ),
  Expanded(
    child: Center(
      child: MyWidget(
        label: 'Bottom Widget')
    )
  ),

```

Updated **MyApp** widget looks like this:

```flutter

class MyApp extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    
    return Scaffold(
        body: Column(
          children: [
            Expanded(
              child: Center(
                child: MyWidget(
                  label: 'Top Widget')
              )
            ),
            Expanded(
              child: Center(
                child: MyWidget(
                  label: 'Bottom Widget')
              )
            ),
          ]
        ),
      );
  }
}
```

Now, run it through DartPad to see what this does:

![Details Page](assets/flutter-codelab-state-mgmt/state_mgmt4.gif)

Uh-oh, something is broken!

Not really - we are just not listening to the changes that are being set on the **count** property, and no one is notifying of those changes. And since this page didn't rebuild based on those changes being made, we don't see the updates.

Let's do an experiment to test.

### Add an additional page

We'll add a separate page, from where we'll access the provided service **ValueService** after the changes have been made.

Create a widget class called **NextPage** with the following content:

```
class NextPage extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    
    var valueService = Provider.of<ValueService>(context, listen: false);

    return Scaffold(
      appBar: AppBar(),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
           Text('This the value on another page!'),
           Text('${valueService.count}',
            style: Theme.of(context).textTheme.headline4
           )
          ],
        )
      )
    );
  }
}
```

Notice how in the **build** method I'm pulling the provided service **ValueService** and consuming the **count** value from it. I'm also adding an **AppBar** widget to the **Scaffold** widget so I have the ability to go back to the page we navigate from.

Now, go back to the **MyApp** page, and under the two **MyWidget** widgets, let's add another **TextButton** widget to allow us to navigate to that other page we created.

```
TextButton(
  onPressed: () {
    Navigator.of(context).push(
      MaterialPageRoute(builder: (context) => NextPage())
    );
  },
  child: const Text('Go to another page')
)
```

Also add an **AppBar** widget to the **Scaffold** widget. The updated **MyApp** widget class should look like this:

```
class MyApp extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    
    return Scaffold(
        appBar: AppBar(),
        body: Column(
          children: [
            const Expanded(
              child: Center(
                child: MyWidget(
                  label: 'Top Widget')
              )
            ),
            const Expanded(
              child: Center(
                child: MyWidget(
                  label: 'Bottom Widget')
              )
            ),
            TextButton(
              onPressed: () {
                Navigator.of(context).push(
                  MaterialPageRoute(builder: (context) => NextPage())
                );
              },
              child: const Text('Go to another page')
            )
          ]
        ),
        
      );
  }
}
```

Now, let's give it another shot. Let's do it again. Click on the "Click me to update!" button on both widgets. Click 2 times on the top widget, 2 times on the bottom widget, for a total of 4. Now, navigate to the **NextPage** widget by clicking on the newly added **TextButton** that says "Go to another page".
And there you should see a value of 4 under the label "This is the value on another page!".  

![Details Page](assets/flutter-codelab-state-mgmt/state_mgmt5.gif)

So just to recap - the **Provider** allows you to hold values for you in memory and makes them available to all descendants of the providing widget, but does not notify of any changes. We'll look into widgets that do broadcast or "notify" listeners, which then rebuild to reflect the changes made.

### Complete Code for this section

```
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

void main() {
  runApp(
    Provider( 
      create: (_) => ValueService(),
      child: MaterialApp(
        debugShowCheckedModeBanner: false,
        home: MyApp()
      )
    )
  );
}


class ValueService {
  int count = 0;
}


class MyApp extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    
    return Scaffold(
        appBar: AppBar(),
        body: Column(
          children: [
            Expanded(
              child: Center(
                child: MyWidget(
                  label: 'Top Widget')
              )
            ),
            Expanded(
              child: Center(
                child: MyWidget(
                  label: 'Bottom Widget')
              )
            ),
            TextButton(
              onPressed: () {
                Navigator.of(context).push(
                  MaterialPageRoute(builder: (context) => NextPage())
                );
              },
              child: const Text('Go to another page')
            )
          ]
        ),
        
      );
  }
}

class NextPage extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    
    var valueService = Provider.of<ValueService>(context, listen: false);

    return Scaffold(
      appBar: AppBar(),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
           Text('This is the value on another page!'),
           Text('${valueService.count}',
            style: Theme.of(context).textTheme.headline4
           )
          ],
        )
      )
    );
  }
}

class MyChildWidget extends StatelessWidget {
  
  final Function? onWidgetClicked;
  
  const MyChildWidget({ this.onWidgetClicked });
  
  @override
  Widget build(BuildContext context) {
    
    return TextButton(
      onPressed: () {
        onWidgetClicked!();
      },
      child: const Text('Click me to update!')
    );
  }
}

class MyWidget extends StatelessWidget {
  
  final String? label;
  const MyWidget({ this.label });
  
  @override
  Widget build(BuildContext context) {
    
    var valueService = Provider.of<ValueService>(context, listen: false);
    
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Text(
          '${label!}, ${valueService.count}',
          style: Theme.of(context).textTheme.headline4,
        ),
        MyChildWidget(
          onWidgetClicked: () {
            valueService.count++;
          }
        )
      ]
    );
  }
}
```

## Lifting the State: Using ChangeNotifier

We saw the benefits of lifting up the state to a much higher level by using the **Provider** approach, and we noticed how we were able to access provided services not just within a widget, but within even separate pages. But we didn't get notified of changes as soon as they happened - instead only at first build of a widget when it pulls the initial values.

For that, there are two constructs that provide the capability to notify listeners on values changing, and widgets that respond to those changes by rebuilding themselves - sort of like the **ValueNotifier** and **ValueListenableBuilder** (which would also be used within this context, but only on a single value). If you want to apply this concept at a class-wide level, you should create a class that extends from **ChangeNotifier**.

![Details Page](assets/flutter-codelab-state-mgmt/img7.png)

[**ChangeNotifier**](https://api.flutter.dev/flutter/foundation/ChangeNotifier-class.html) is a class that can be extended or mixed in that provides a change notification API using **VoidCallback** for notifications, and it is provided just like the **Provider**, but using another construct called **ChangeNotifierProvider**.

Let's make some changes to our code.

At the top of the code, replace **Provider** by **ChangeNotifierProvider**:

```
void main() {
  runApp(
    ChangeNotifierProvider( 
      create: (_) => ValueService(),
      child: MaterialApp(
        debugShowCheckedModeBanner: false,
        home: MyApp()
      )
    )
  );
}
```

Negative
: We're not done yet! You should be getting an error by now, because the **ValueService** does not extend **ChangeNotifier** thus cannot be provided by the **ChangeNotifierProvider**. Let's make that change now.

Go to the **ValueService** class and let's make the following changes:

- make the **ValueService** class extend **ChangeNotifier**. This is the class that gives it the broadcasting / notification capabilities to notify whoever is listening so as to update itself. It has a special method called **notifyListeners** which does exactly as its name implies.

Instead of just being a plain property called **count**, make a getter and a setter around that property. Inside its **setter** method, right after setting its value, call the **notifyListeners()** inherited method.

Your updated class should look as follows:

```
class ValueService extends ChangeNotifier {
  
  int _count = 0;
  int get count => _count;
  
  set count(int value) {
    _count = value;
    notifyListeners();
  }
}
```

Now, every time someone attempts to set the value of count, this will call its internal **notifyListeners** method, and whoever is consuming this service, will trigger either its build method, or in the special widgets, its **builder** callback method. 

Now, in order to consume this newly provided service, there's a special widget that does just that - the [**Consumer**](https://pub.dev/documentation/provider/latest/provider/Consumer-class.html) widget, whose only job is to listen to changes on the consumed service in question and runs its builder method.

Let's go back to the **MyWidget** widget and do some refactoring.

Remove the provided service **ValueService** instance at the top of the build method. Wrap the returned **Column** inside a **Consumer** widget of type **ValueService** with the following specs:
- set the **Consumer** of type **ValueService**
- on its **builder** method, set the 3 required parameters: a context of type **BuildContext**, the provided service **ValueService**, and a child **Widget**, for optimization purposes. Return the existing **Column** structure out of this **builder** method.

The updated **MyWidget** widget should look like this:

```
class MyWidget extends StatelessWidget {
  
  final String? label;
  const MyWidget({ this.label });
  
  @override
  Widget build(BuildContext context) {
    
    return Consumer<ValueService>(
      builder: (context, valueService, child) {
        return Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Text(
              '${label!}, ${valueService.count}',
              style: Theme.of(context).textTheme.headline4,
            ),
            MyChildWidget(
              onWidgetClicked: () {
                valueService.count++;
              }
            )
          ]
        );
      },
    );
  }
}
```

Let's try the same exercise as before; this time since are using the **Consumer** widget that listens to the **ValueService**, and this service notifies all its listeners and instructs them to rebuild, now we should see the change occur in real time.

![Details Page](assets/flutter-codelab-state-mgmt/state_mgmt6.gif)

Notice how all widgets are in sync, even when you navigate to another page, come back, etc. How cool is that!

Now you can even create other widgets that consume this same service in a variety of ways. The code below shows a new custom widget called **NotificationWidget** that shows a badge notification on the **AppBar** widget in the **MyApp** widget:

```
class NotificationWidget extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    return Container(
      height: 60,
      margin: const EdgeInsets.only(right: 10),
      child: Row(
        children: [
          const Icon(
            Icons.notifications,
            color: Colors.white,
            size: 25
          ),
          Container(
            height: 30,
            padding: const EdgeInsets.only(left: 10, right: 10),
            alignment: Alignment.center,
            decoration: BoxDecoration(
              borderRadius: BorderRadius.circular(20),
              color: Colors.red
            ),
            child: Consumer<ValueService>(
              builder: (context, vService, child) {
                return Text(vService.count.toString());
              }
            )
          )
        ] 
      )
    );
  }
}
```

Grab this widget and place it on the **MyAppBar**, inside the **Scaffold**'s **AppBar**, as a children of its **actions** property, as such:

```
Scaffold(
  appBar: AppBar(
    actions: [
      NotificationWidget()
    ]
  ),
  body: // ... rest of the code omitted for brevity
}
```

Updated **MyApp** code should look as follows:

```
class MyApp extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    
    return Scaffold(
      appBar: AppBar(
        actions: [
          NotificationWidget()
        ]
      ),
        body: Column(
          children: [
            const Expanded(
              child: Center(
                child: MyWidget(
                  color: Colors.green,
                  label: 'Top Widget')
              )
            ),
            const Expanded(
              child: Center(
                child: MyWidget(
                  color: Colors.purple,
                  label: 'Bottom Widget')
              )
            ),
            TextButton(
              onPressed: () {
                Navigator.of(context).push(
                  MaterialPageRoute(builder: (context) => NextPage())
                );
              },
              child: const Text('Go to another page')
            )
          ]
        ),
    );
  }
}
```

Run it on DartPad, and click on the buttons in any of the **MyWidget** widgets, notice the new **NotificationWidget** at the top right corner, listening to the same changes the other widgets are:

![Details Page](assets/flutter-codelab-state-mgmt/state_mgmt7.gif)

### Complete code for this section

```
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

void main() {
  runApp(
    ChangeNotifierProvider( 
      create: (_) => ValueService(),
      child: MaterialApp(
        debugShowCheckedModeBanner: false,
        home: MyApp()
      )
    )
  );
}


class ValueService extends ChangeNotifier {
  
  int _count = 0;
  int get count => _count;
  
  set count(int value) {
    _count = value;
    notifyListeners();
  }
}


class MyApp extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    
    return Scaffold(
        appBar: AppBar(
          actions: [
            NotificationWidget()
          ]
        ),
        body: Column(
          children: [
            const Expanded(
              child: Center(
                child: MyWidget(
                  label: 'Top Widget')
              )
            ),
            const Expanded(
              child: Center(
                child: MyWidget(
                  label: 'Bottom Widget')
              )
            ),
            TextButton(
              onPressed: () {
                Navigator.of(context).push(
                  MaterialPageRoute(builder: (context) => NextPage())
                );
              },
              child: const Text('Go to another page')
            )
          ]
        ),
        
      );
  }
}

class NextPage extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    
    var valueService = Provider.of<ValueService>(context, listen: false);

    return Scaffold(
      appBar: AppBar(),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
           const Text('This is the value on another page!'),
           Text('${valueService.count}',
            style: Theme.of(context).textTheme.headline4
           )
          ],
        )
      )
    );
  }
}

class MyChildWidget extends StatelessWidget {
  
  final Function? onWidgetClicked;
  
  const MyChildWidget({ this.onWidgetClicked });
  
  @override
  Widget build(BuildContext context) {
    
    return TextButton(
      onPressed: () {
        onWidgetClicked!();
      },
      child: const Text('Click me to update!')
    );
  }
}

class MyWidget extends StatelessWidget {
  
  final String? label;
  const MyWidget({ this.label });
  
  @override
  Widget build(BuildContext context) {
    
    return Consumer<ValueService>(
      builder: (context, valueService, child) {
        return Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Text(
              '${label!}, ${valueService.count}',
              style: Theme.of(context).textTheme.headline4,
            ),
            MyChildWidget(
              onWidgetClicked: () {
                valueService.count++;
              }
            )
          ]
        );
      },
    );
  }
}

class NotificationWidget extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    return Container(
      height: 60,
      margin: const EdgeInsets.only(right: 10),
      child: Row(
        children: [
          const Icon(
            Icons.notifications,
            color: Colors.white,
            size: 25
          ),
          Container(
            height: 30,
            padding: const EdgeInsets.only(left: 10, right: 10),
            alignment: Alignment.center,
            decoration: BoxDecoration(
              borderRadius: BorderRadius.circular(20),
              color: Colors.red
            ),
            child: Consumer<ValueService>(
              builder: (context, vService, child) {
                return Text(vService.count.toString());
              }
            )
          )
        ] 
      )
    );
  }
}
```

## Lifting the State: Adding more than one Provided Service

Of course the mayority of the apps out there rely on more than just a single service; services encapsulate business logic that shouldn't live on the widget classes, and this promotes a good separation of concerns. Services are usually divided by features, functionality or app domains, and just a simple way on your path to achieving a clean architecture and good distribution of your code.

In this section we'll do more than one service, just to illustrate our point.

![Details Page](assets/flutter-codelab-state-mgmt/img8.png)

Instead of providing a single service to the descendants of this app, we'll add an additional service. There's no limit in how many services you can provide down the hierarchy.

Let's create a new class called **ColorService**, which will also extend **ChangeNotifier**. This service will be provided to the descendants but only consumable by the **NotificationWidget** as it will be used to change the color on the notification badge programmatically. Add the following properties:
- a propery called **color** with a getter and a setter, with a default color of **Colors.red**. Make sure to call **notifyListeners** upon setting the color:


```flutter

class ColorService extends ChangeNotifier {
  
  Color _color = Colors.red;
  
  Color get color =>  _color;
  
  set color(Color value) {
    _color = value;
    notifyListeners();
  }
}
```

Now, how do we add more than one **ChangeNotifierProvider** at the top of our hierarchy so they trickle down? Using another special widget called **MultiProvider** which does just that: provides multiple providers down the hierarchy.

Wrap the whole **MaterialApp** widget inside a **MultiProvider** widget, set the **MaterialApp** as the child of this widget. 

For its **providers** property, set both **ValueService** and now the **ColorService** wrapped inside **ChangeNotifierProvider**s, as the below updated **void main** method illustrates:

```
void main() {
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(
          create: (_) => ValueService()
        ),
        ChangeNotifierProvider(
          create: (_) => ColorService()
        )
      ],
      child: MaterialApp(
        debugShowCheckedModeBanner: false,
        home: MyApp()
      )
    )
  );
}
```

And as simple as that, you are all set, ready to consume this newly provided service.

Down on the **NotificationWidget**, let's consume this service.

Inside its **build** method, wrap the existing **Consumer&lt;ValueService&gt;** service in yet another consumer that listens to the **ColorService**. Set the color of the existing **Container** widget to the color that comes from the **ColorService** supplied to this **Consumer**, as such:

```
Consumer<ColorService>(
  builder: (context, cService, child) {
    return Container(
      height: 30,
      padding: const EdgeInsets.only(left: 10, right: 10),
      alignment: Alignment.center,
      decoration: BoxDecoration(
        borderRadius: BorderRadius.circular(20),
        color: cService.color
      ),
      child: Consumer<ValueService>(
        builder: (context, vService, child) {
          return Text(vService.count.toString());
        }
      )
    );
  }
)
```

Now we need to provide the way to change the value of the color in this service.

Let's refactor the **MyWiget** widget, to now not only take a **label** but a **Color** parameter, that way each widget has a unique way to set the color of the **NotificationWidget** badge.

Add a **Color** property, called **color**; add its corresponding constructor parameter:

```

final String? label;
final Color? color;

const MyWidget({ this.label, this.color });

```

Add an additional custom button using the **MyChildWidget** custom widget and providing a label and a callback for its **onWidgetClicked** event; inside this callback, invoke the **ColorService** by pulling it using the **Provider.of**, then setting its **color** value based on the supplied **color** property, as such:

```
MyChildWidget(
  label: "Click to update color",
  onWidgetClicked: () {
    var colorService = Provider.of<ColorService>(context, listen: false);
    colorService.color = color!;
  }
)
```

Updated **MyWidget** widget class should mimic this one:

```
class MyWidget extends StatelessWidget {
  
  final String? label;
  final Color? color;
  
  const MyWidget({ this.label, this.color });
  
  @override
  Widget build(BuildContext context) {
    
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Consumer<ValueService>(
          builder: (context, vService, child) {
            return Text(
              '${label!}, ${vService.count}',
              style: Theme.of(context).textTheme.headline4,
            );
          }
        ),
        MyChildWidget(
          label: "Click to update number!",
          onWidgetClicked: () {
            var valueService = Provider.of<ValueService>(context, listen: false);
            valueService.count++;
          }
        ),
        MyChildWidget(
          label: "Click to update color",
          onWidgetClicked: () {
            var colorService = Provider.of<ColorService>(context, listen: false);
            colorService.color = color!;
          }
        )
      ]
    );
  }
}

```

Back on the **MyApp**, populate the newly created constructor parameter to the **MyWidget** widgets by passing a color (i.e. green for the top widget, purple for the second widget):


```
const Expanded(
  child: Center(
    child: MyWidget(
      color: Colors.green,
      label: 'Top Widget')
  )
),
const Expanded(
  child: Center(
    child: MyWidget(
      color: Colors.purple,
      label: 'Bottom Widget')
  )
),
```

Updated **MyApp** widget should look like the code below:

```
class MyApp extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    
    return Scaffold(
      appBar: AppBar(
        actions: [
          NotificationWidget()
        ]
      ),
        body: Column(
          children: [
            const Expanded(
              child: Center(
                child: MyWidget(
                  color: Colors.green,
                  label: 'Top Widget')
              )
            ),
            const Expanded(
              child: Center(
                child: MyWidget(
                  color: Colors.purple,
                  label: 'Bottom Widget')
              )
            ),
            TextButton(
              onPressed: () {
                Navigator.of(context).push(
                  MaterialPageRoute(builder: (context) => NextPage())
                );
              },
              child: const Text('Go to another page')
            )
          ]
        ),
    );
  }
}
```

Now let's repeat the exercise and tap on the newly provided buttons to trigger a change in the notification badge color:

![Details Page](assets/flutter-codelab-state-mgmt/state_mgmt8.gif)

### Complete code for this section

```

import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

void main() {
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(
          create: (_) => ValueService()
        ),
        ChangeNotifierProvider(
          create: (_) => ColorService()
        )
      ],
      child: MaterialApp(
        debugShowCheckedModeBanner: false,
        home: MyApp()
      )
    )
  );
}


class ValueService extends ChangeNotifier {
  
  int _count = 0;
  
  int get count => _count;
  
  set count(int value) {
    _count = value;
    notifyListeners();
  }
  
}

class ColorService extends ChangeNotifier {
  
  Color _color = Colors.red;
  
  Color get color =>  _color;
  
  set color(Color value) {
    _color = value;
    notifyListeners();
  }
}

class MyApp extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    
    return Scaffold(
      appBar: AppBar(
        actions: [
          NotificationWidget()
        ]
      ),
        body: Column(
          children: [
            const Expanded(
              child: Center(
                child: MyWidget(
                  color: Colors.green,
                  label: 'Top Widget')
              )
            ),
            const Expanded(
              child: Center(
                child: MyWidget(
                  color: Colors.purple,
                  label: 'Bottom Widget')
              )
            ),
            TextButton(
              onPressed: () {
                Navigator.of(context).push(
                  MaterialPageRoute(builder: (context) => NextPage())
                );
              },
              child: const Text('Go to another page')
            )
          ]
        ),
    );
  }
}

class NextPage extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    
    return Scaffold(
      appBar: AppBar(),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            const Text("This is another page!"),
            TextButton(
              onPressed: () {
                Navigator.of(context).push(
                  MaterialPageRoute(builder: (context) => MyApp())
                );
              },
              child: const Text('Go to the First Page again!')
            )
          ],
        )
      )
    );
  }
}

class MyChildWidget extends StatelessWidget {
  
  final Function? onWidgetClicked;
  final String? label;
  const MyChildWidget({ this.onWidgetClicked, this.label });
  
  @override
  Widget build(BuildContext context) {
    
    return TextButton(
      onPressed: () {
        onWidgetClicked!();
      },
      child: Text(label!)
    );
  }
}

class MyWidget extends StatelessWidget {
  
  final String? label;
  final Color? color;
  
  const MyWidget({ this.label, this.color });
  
  @override
  Widget build(BuildContext context) {
    
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Consumer<ValueService>(
          builder: (context, vService, child) {
            return Text(
              '${label!}, ${vService.count}',
              style: Theme.of(context).textTheme.headline4,
            );
          }
        ),
        MyChildWidget(
          label: "Click to update number!",
          onWidgetClicked: () {
            var valueService = Provider.of<ValueService>(context, listen: false);
            valueService.count++;
          }
        ),
        MyChildWidget(
          label: "Click to update color",
          onWidgetClicked: () {
            var colorService = Provider.of<ColorService>(context, listen: false);
            colorService.color = color!;
          }
        )
      ]
    );
  }
}

class NotificationWidget extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    return Container(
      height: 60,
      margin: const EdgeInsets.only(right: 10),
      child: Row(
        children: [
          const Icon(
            Icons.notifications,
            color: Colors.white,
            size: 25
          ),
          Consumer<ColorService>(
            builder: (context, cService, child) {
              return Container(
                height: 30,
                padding: const EdgeInsets.only(left: 10, right: 10),
                alignment: Alignment.center,
                decoration: BoxDecoration(
                  borderRadius: BorderRadius.circular(20),
                  color: cService.color
                ),
                child: Consumer<ValueService>(
                  builder: (context, vService, child) {
                    return Text(vService.count.toString());
                  }
                )
              );
            }
          )
        ] 
      )
    );
  }
}

```

## Access a Provided Service from Another Service

![Details Page](assets/flutter-codelab-state-mgmt/img9.png)

Sometimes you want to consolidate all actions inside a single service; there are cases in which a combined set of features is required to complete a given task. Instead of putting all functionality inside the same service for the sake of convenience, it is best to keep related functionality separate, but only tap into the required functionality when needed.

For that, we will create a common service, called **CombinedService** that actually pulls all available services and tap into their functionality without them having to know about each other, keeping the separation of concerns pristine and decoupled.

Create a new class called **CombinedService** which will serve as the proxy to call the other services. Inside this class, we'll create a method called **setBothColorAndValue**, with the following specs:
- add the required parameters to set a color and a value, as well as a **BuildContext** parameter; we will need this to pull the services out of the **Provider**.

Your new class' code should look like this:

```
class CombinedService {
  
  void setBothColorAndValue(BuildContext context, Color c, int v) {
    
    var colorService = Provider.of<ColorService>(context, listen: false);
    var valueService = Provider.of<ValueService>(context, listen: false);
    
    colorService.color = c;
    valueService.count = v;
  }
}
```

Refactor the **main** method by adding a new provided service, wrapping our new **CombinedService** class inside a **Provider**; this one will trigger other services, so no need to be a **ChangeNotifierProvider**:

```
Provider(
  create: (_) => CombinedService()
)
```

Your updated **main** method should look like this:

```
void main() {
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(
          create: (_) => ValueService()
        ),
        ChangeNotifierProvider(
          create: (_) => ColorService()
        ),
        Provider(
          create: (_) => CombinedService()
        )
      ],
      child: MaterialApp(
        debugShowCheckedModeBanner: false,
        home: MyApp()
      )
    )
  );
}
```

Now let's add the functionality to trigger this action.

Back on the **MyApp** widget, add a new **TextButton** under the second **MyWidget** widget wrapped by an **Expanded**. This button will require the context of the current page widget, and we'll pass an orange color, as well as the fixed value of 100. When the user taps on this button, this service will call the two other services (**ValueService** and **ColorService**), each of which in turn will execute their corresponding actions, notify listeners and rebuild accordingly.

Your new button should look like this:


```
TextButton(
  onPressed: () {
    var combinedService = Provider.of<CombinedService>(context, listen: false);
    combinedService.setBothColorAndValue(context, Colors.orange, 100);
  },
  child: const Text('Change Both')
),
```

Updated **MyApp** widget should look like this:

```
class MyApp extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    
    return Scaffold(
      appBar: AppBar(
        actions: [
          NotificationWidget()
        ]
      ),
        body: Column(
          children: [
            const Expanded(
              child: Center(
                child: MyWidget(
                  color: Colors.green,
                  label: 'Top Widget')
              )
            ),
            const Expanded(
              child: Center(
                child: MyWidget(
                  color: Colors.purple,
                  label: 'Bottom Widget')
              )
            ),
            TextButton(
              onPressed: () {
                var combinedService = Provider.of<CombinedService>(context, listen: false);
                combinedService.setBothColorAndValue(context, Colors.orange, 100);
              },
              child: const Text('Change Both')
            ),
            TextButton(
              onPressed: () {
                Navigator.of(context).push(
                  MaterialPageRoute(builder: (context) => NextPage())
                );
              },
              child: const Text('Go to another page')
            )
          ]
        ),
    );
  }
}
```

Following the same exercise and tapping on our newly added button to change both color and value from a combined service that taps into other services should yield the following output on DartPad:

![Details Page](assets/flutter-codelab-state-mgmt/state_mgmt9.gif)

And with that, we are fully done with this workshop! Kudos to you for making it all the way to the end; although we went through some simple examples, these strategies can be utilized in production-level and enterprise-level apps, both small and large, while at the same time promoting good code distribution, separation of concerns and a clean architecture runway.

Thanks again and hope you've learned a lot from this experience.

See you on the next one!

## Wrap-up

In this codelab, we accomplished the following:
- Multiple levels at which you can manage state in your Flutter apps
  - Using **StatefulWidgets** and the **setState** method to trigger changes
  - Using **ValueNotifier** and **ValueListenableBuilder** 
  - Sharing data between widgets
  - Triggering rebuilds on one widget from another widget
  - "Lifting the State" using **Provider**
  - Leveraging more than one service using **MultiProvider**
  - Communication between provided services
  - Listening to changes and rebuilding using **ChangeNotifierProvider** and **ChangeNotifier** services in conjunction with **Consumer** widgets


#### Please don't forget to follow me on social media:

- On Twitter ([@drcoderz](https://www.twitter.com/drcoderz))
- On YouTube ([Roman Just Codes](https://www.youtube.com/channel/UCKsp3r1ERjCpKJtD2n5WtPg))
- On My [Personal Portfolio](https://romanjustcodes.web.app/#/home)
- On [Medium](https://medium.com/@romanejaquez)
- On [LinkedIn](https://www.linkedin.com/in/roman-jaquez-8941a424/)

## Complete Code 

```flutter

import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

void main() {
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(
          create: (_) => ValueService()
        ),
        ChangeNotifierProvider(
          create: (_) => ColorService()
        ),
        Provider(
          create: (_) => CombinedService()
        )
      ],
      child: MaterialApp(
        debugShowCheckedModeBanner: false,
        home: MyApp()
      )
    )
  );
}


class ValueService extends ChangeNotifier {
  
  int _count = 0;
  
  int get count => _count;
  
  set count(int value) {
    _count = value;
    notifyListeners();
  }
  
}

class ColorService extends ChangeNotifier {
  
  Color _color = Colors.red;
  
  Color get color =>  _color;
  
  set color(Color value) {
    _color = value;
    notifyListeners();
  }
}

class CombinedService {
  
  void setBothColorAndValue(BuildContext context, Color c, int v) {
    
    var colorService = Provider.of<ColorService>(context, listen: false);
    var valueService = Provider.of<ValueService>(context, listen: false);
    
    colorService.color = c;
    valueService.count = v;
  }
}

class MyApp extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    
    return Scaffold(
      appBar: AppBar(
        actions: [
          NotificationWidget()
        ]
      ),
        body: Column(
          children: [
            const Expanded(
              child: Center(
                child: MyWidget(
                  color: Colors.green,
                  label: 'Top Widget')
              )
            ),
            const Expanded(
              child: Center(
                child: MyWidget(
                  color: Colors.purple,
                  label: 'Bottom Widget')
              )
            ),
            TextButton(
              onPressed: () {
                var combinedService = Provider.of<CombinedService>(context, listen: false);
                combinedService.setBothColorAndValue(context, Colors.orange, 100);
              },
              child: const Text('Change Both')
            ),
            TextButton(
              onPressed: () {
                Navigator.of(context).push(
                  MaterialPageRoute(builder: (context) => NextPage())
                );
              },
              child: const Text('Go to another page')
            )
          ]
        ),
    );
  }
}

class NextPage extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    
    return Scaffold(
      appBar: AppBar(),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            const Text("This is another page!"),
            TextButton(
              onPressed: () {
                Navigator.of(context).push(
                  MaterialPageRoute(builder: (context) => MyApp())
                );
              },
              child: const Text('Go to the First Page again!')
            )
          ],
        )
      )
    );
  }
}

class MyChildWidget extends StatelessWidget {
  
  final Function? onWidgetClicked;
  final String? label;
  const MyChildWidget({ this.onWidgetClicked, this.label });
  
  @override
  Widget build(BuildContext context) {
    
    return TextButton(
      onPressed: () {
        onWidgetClicked!();
      },
      child: Text(label!)
    );
  }
}

class MyWidget extends StatelessWidget {
  
  final String? label;
  final Color? color;
  
  const MyWidget({ this.label, this.color });
  
  @override
  Widget build(BuildContext context) {
    
    return Column(
      mainAxisSize: MainAxisSize.min,
      children: [
        Consumer<ValueService>(
          builder: (context, vService, child) {
            return Text(
              '${label!}, ${vService.count}',
              style: Theme.of(context).textTheme.headline4,
            );
          }
        ),
        MyChildWidget(
          label: "Click to update number!",
          onWidgetClicked: () {
            var valueService = Provider.of<ValueService>(context, listen: false);
            valueService.count++;
          }
        ),
        MyChildWidget(
          label: "Click to update color",
          onWidgetClicked: () {
            var colorService = Provider.of<ColorService>(context, listen: false);
            colorService.color = color!;
          }
        )
      ]
    );
  }
}

class NotificationWidget extends StatelessWidget {
  
  @override
  Widget build(BuildContext context) {
    return Container(
      height: 60,
      margin: const EdgeInsets.only(right: 10),
      child: Row(
        children: [
          const Icon(
            Icons.notifications,
            color: Colors.white,
            size: 25
          ),
          Consumer<ColorService>(
            builder: (context, cService, child) {
              return Container(
                height: 30,
                padding: const EdgeInsets.only(left: 10, right: 10),
                alignment: Alignment.center,
                decoration: BoxDecoration(
                  borderRadius: BorderRadius.circular(20),
                  color: cService.color
                ),
                child: Consumer<ValueService>(
                  builder: (context, vService, child) {
                    return Text(vService.count.toString());
                  }
                )
              );
            }
          )
        ] 
      )
    );
  }
}


```
