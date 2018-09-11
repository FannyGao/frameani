# frameani.js

frameani.js is a high degree of freedom animation library pulled from the concrete realization of the animation. According to the given animation speed curve, it help you to complete the "frame-value" numerical calculation, and how to achieve the animation is decided by developers freely.

In addition, it also provides a series of methods for you to better organize and manage the animation queue, help you to make more complex animations, or even to achieve a combination of multiple animation.

​

## Feature

- Hign degree of freedom. it help you to complete the "frame-value" numerical calculation according to the given animation speed curve, developers can decide how to achieve the animation freely.
- Support for basic event listening. such as `onPlay`、`onStop`、`onReset`、`onEnd`
- Support the active trigger animation of the various states, such as `play`、`stop`、`reset`、`end`
- Support custom path animation
- Support trigger multiple animation sequentially

  ​

## Quick start

```javascript
const ele = document.querySelector("#target");
const frameani = new Frameani({
  value: [0, 500],
  duration: 1000,
  timingFunction: "easeOut",
  render: function(value) {
    ele.style.transform = `translateX(${value}px)`;
  },
  onPlay: () => console.log("begin!"),
  onEnd: () => console.log("end! haha...")
});

frameani.play();
```

The parameters are set as follows:

- `duration`  
  optional / number / total time，the default is 1000ms

- `timingFunction`  
  optional / string / ease function，the default is linear

- `render`  
  required / function / render function，the parameter is the motion value corresponding to the current time progress

- various events  
  optional / function / callback

  ​

Note that the `value` is a special parameter that defines both the start and end of the animation, frameani will calculate the current value of current time progress, and return it as a parameter of the render function.

For example, to let the element pan right 300px within 3s in a steady state, when it reaches 2 seconds, the returned value is 200.

- When value is a unary array of length 2, the first is the starting value, and the second is the ending value

  ```javascript
  // the element is panned 300px to the right
  const frameani = new Frameani({
    value: [0, 300],
    duration: 1000,
    render: function(value) {
      ele.style.transform = `translateX(${value}px)`;
    }
  });
  ```

- When value is a binary array（each one is an array of length 2），frameani will calculate the current value of current time progress for each one (the length of the binary array can be infinitely expanded)

  ```Javascript
  // the element is panned 300px and 500px to the right and down respectively.
  const frameani = new Frameani({
    value: [[0, 300], [0, 500]],
    duration: 1000,
    render: function(value1, value2) {
      ele.style.transform = `translate(${ value1 }px, ${ value2 }px)`;
    }
  })
  ```

- When value is a "path object" constructed by `Frameani.path`, frameani will get the current point from the given path of current time progress, to achieve coustom path animation.

  ```javascript
  const frameani = new Frameani({
    value: Frameani.path("M0 0L23 34L60 90Q32 46 23 12Q234 565 234 645Z"),
    duration: 1000,
    render: function(value1, value2) {
      ele.style.transform = `translate(${value1}px, ${value2}px)`;
    }
  });
  ```

  ​

## To

If you want to trigger the next animation when an animation is finished，can use `to` method. And it support the chained calls.

```javascript
const ele1 = document.getElementById("target1");
const ele2 = document.getElementById("target2");
const frameani = new Frameani({
  target: document.getElementById("target1"),
  value: [0, 500],
  duration: 1000,
  timingFunction: "easeOut",
  render: function(value) {
    ele1.style.transform = `translateX(${value}px)`;
  }
}).to({
  target: document.getElementById("target2"),
  value: [-100, 300],
  render: function(value) {
    ele2.style.transform = `translateX(${value}px)`;
  }
});

frameani.play();
```

​

## Custom path animation

`Frameani.path` can help you to achieve the more complex path animation.

```javascript
const frameani = new Frameani({
  value: Aqueue.path("M0 0L23 34L60 90Q32 46 23 12Q234 565 234 645Z"),
  render: function(point) {
    ele.style.transform = `translate(${point.x}px,${point.y}px)`;
  },
  timingFunction: "easeOut",
  duration: 7000
});

frameani.play();
```

Notice that the parameter of the render function has changed: the `point` is an object and it save the coordinates of the current point.

​

## Events

frameani support the events of `onPlay`, `onReset`, `onStop` and `onEnd`, the one thing you should do is pass them into the animation configuration:

```javascript
const aq = new Aqueue({
  value: [0, 500],
  duration: 1000,
  timingFunction: "easeOut",
  render: function(value) {
    ele.style.transform = `translateX(${value}px)`;
  },
  onPlay: () => console.log("begin!"),
  onEnd: () => console.log("end! haha..."),
  onReset: () => console.log("reset!"),
  onStop: () => console.log("hoooo...it stop")
});
```

In addition, timline also allows you to manually trigger the following events:

- `play`  
  Animation begins execution
- `reset`  
  Reset the animation.
- `stop`  
  Stop the animation
- `end`  
  End the animation

Notice that, when the `reset`, `stop`和`end` is called, all animations saved in the queue, added via the `to`, will also be reset / paused / ended.
