# REACT NATIVE Reanimated + Gesture Handler

#### This is the completed code snippet I will be using throughout this post:
```javascript

```

## useState Is Not Enough
React Native Reanimated optimizes animations by executing animation code on a separate "UI thread".

useState lets data persist across component re-renders in the main "JS thread", but the UI thread does not have access to these JS thread memory locations.

Long story short, updating a useState variable from a Reanimated worklet will have no effect on the variable.

This is where useSharedValue comes in...


## useSharedValue Is Essentially useState
useSharedValue is like useState in the Reanimated ecosystem.

Reanimated knows how to access a SharedValue's memory location in both the JS thread and the UI thread.
And this value can then be paired with useAnimatedStyle to put your animations entirely in the hands of Reanimated.

Another long story short, SharedValues are a developer's bridge between app logic and animation logic.

But all is not good in the hood just yet...

## REACT Does Not React To SharedValue Changes
SharedValues are modifiable from either the JS thread or the UI thread, but React does not respond to a SharedValue change:
For whatever reason, useEffect will not execute and component elements will not re-render when a SharedValue changes.

So what if you are using Gesture Handler and you need to display a value depending on the position of the last gesture?
Or you need to perform some custom calculation based on the final state of the last animation?

You will need to know that the SharedValue has changed...

???

useSharedValue alone is enough in the case that it is paired with other methods/hooks provided by Reanimated/Gesture Handler.
But it is not enough to perform custom computations with the SharedValues' values, since React does not respond to SharedValue changes.

Enter

## runOnJS Widens The Bridge
Reanimated provides a method, "runOnJS".

With this method, you can call a useState setter and pass in a SharedValue.
Since the code will execute in the JS thread, the application will actually have access the the setter's memory location and be able to to use it to update state.

The SharedValues are still necessary to pair with Reanimated's optimized animation methods.
But now, you can add effects to the SharedValues and perform actions when they change.

Just don't execute "runOnJS" every frame. That sounds like it'll throw away the performance benefits of running animations and gestures on the UI thread.
Instead, perform some check before calling your setter to execute an effect: After a gesture passes a threshold or maybe once a gesture has changed by some delta.
