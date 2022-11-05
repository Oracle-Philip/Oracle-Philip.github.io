---
layout: post
title: 'Flutter_week10'
subtitle:   "."
date: '2022-11-05 00:00:51 +0900'
categories:
    - study
    - flutter
tags:
    - flutter
comments: true
published: true
hide_last_modified: true
sitemap: false
---

# Flutter w10 _Navigation


- Cookbook - Navigation
    - Animate a widget across screens
           
        <iframe width="100%" height="800px" loading="lazy" src="https://dartpad.dev/?id=c618b76fc6cd821971a2565dd3705a5e"></iframe>
   
    - Navigate to a new screen and back
       
        <iframe width="100%" height="800px" loading="lazy" src="https://dartpad.dev/?id=733813794a9a6717615b0bb2f5292f2c"></iframe>
        
        > In Android, a route is equivalent to an Activity. In iOS, a route is equivalent to a ViewController. In Flutter, a route is just a widget. This recipe uses the Navigator to navigate to a new route.
        >

        > To switch to a new route, use the [Navigator.push()](https://api.flutter.dev/flutter/widgets/Navigator/push.html) method. The `push()`method adds a `Route`to the stack of routes managed by the `Navigator`.
        > 

        > By using the [Navigator.pop()](https://api.flutter.dev/flutter/widgets/Navigator/pop.html) method. The `pop()`method removes the current `Route`from the stack of routes managed by the `Navigator`.


    - Navigate with named routes
          
        <iframe width="100%" height="800px" loading="lazy" src="https://dartpad.dev/?id=0f3dc262bee67fa4769f5de19af6e218"></iframe>
        
        > To work with named routes, use the [Navigator.pushNamed()](https://api.flutter.dev/flutter/widgets/Navigator/pushNamed.html) function. This example replicates the functionality from the original recipe, demonstrating how to use named routes using the following steps:
        
        1. Create two screens.
        2. Define the routes.
        3. Navigate to the second screen using `Navigator.pushNamed()`.
        4. Return to the first screen using `Navigator.pop()`.

    - Pass arguments to a named route
             
        <iframe width="100%" height="800px" loading="lazy" src="https://dartpad.dev/?id=07da451651abfca52c7246bab3aa9924"></iframe>

    - Return data from a screen
                 
        <iframe width="100%" height="800px" loading="lazy" src="https://dartpad.dev/?id=eca8be9da060895d4e03daf299a6d764"></iframe>
        
        > Show a snackbar on the home screen with the selection. Now that you’re Launching a selection screen and awaiting the result, you’ll want to do something with the information that’s returned. In this case, show a snackbar displaying the result by using the _navigateAndDisplaySelection() method in SelectionButton:


        ```dart
            // A method that launches the SelectionScreen and awaits the result from
            // Navigator.pop.
            Future<void> _navigateAndDisplaySelection(BuildContext context) async {
              // Navigator.push returns a Future that completes after calling
              // Navigator.pop on the Selection Screen.
              final result = await Navigator.push(
                context,
                MaterialPageRoute(builder: (context) => const SelectionScreen()),
              );

              // When a BuildContext is used from a StatefulWidget, the mounted property
              // must be checked after an asynchronous gap.
              if (!mounted) return;

              // After the Selection Screen returns a result, hide any previous snackbars
              // and show the new result.
              ScaffoldMessenger.of(context)
                ..removeCurrentSnackBar()
                ..showSnackBar(SnackBar(content: Text('$result')));
            }
        ```

    - Send data to a new screen
                     
        <iframe width="100%" height="800px" loading="lazy" src="https://dartpad.dev/?id=a160eaa4f86d97585438ad6f25d26430"></iframe>
