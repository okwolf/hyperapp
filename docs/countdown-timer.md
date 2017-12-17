# Countdown Timer

In this example we'll learn how to trigger state updates from external events.

[Try it Online](https://codepen.io/hyperapp/pen/evOZLv?editors=0010)

```jsx
const SECONDS = 5

const pad = n => (n < 10 ? "0" + n : n)

const humanizeTime = t => {
  const hours = (t / 3600) >> 0
  const minutes = ((t - hours * 3600) / 60) >> 0
  const seconds = (t - hours * 3600 - minutes * 60) >> 0
  return `${pad(minutes)}:${pad(seconds)}`
}

const model = {
  state: {
    count: SECONDS,
    paused: true
  },
  actions: {
    tick: () => (state, actions) => {
      if (state.count === 0) {
        actions.reset()
        actions.toggle()
      } else if (!state.paused) {
        actions.drop()
      }
    },
    drop: () => state => ({ count: state.count - 1 }),
    reset: () => ({ count: SECONDS }),
    toggle: () => state => ({ paused: !state.paused })
  }
}

const view = ({ state, actions }) => (
  <main>
    <h1>{humanizeTime(state.count)}</h1>

    <button onclick={actions.toggle}>
      {state.paused ? "START" : "PAUSED"}
    </button>

    <button onclick={actions.reset}>RESET</button>
  </main>
)

const { actions } = app(model, view, document.body)

setInterval(actions.tick, 1000)
```

The `app()` function returns an object that has your model state and actions wired to the app.

```jsx
const { actions } = app({ ... })
```

The state consists of two properties: `count`, to track the seconds elapsed; and `paused`, to check if the clock is currently running.

```jsx
state: {
  count: SECONDS,
  paused: true
}
```

The view displays the seconds inside a `<h1>` element and binds two buttons to `actions.toggle` and `actions.reset` respectively.

```jsx
<button onclick={actions.toggle}>
  {state.paused ? "START" : "PAUSED"}
</button>

<button onclick={actions.reset}>RESET</button>
```

To track time we use [`setInterval`](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval) and call `tick` every second.

```jsx
setInterval(actions.tick, 1000)
```

Inside `tick`, we check the second count and if zero, reset the counter back to `SECONDS` and toggle the running clock.

If `state.count` is greater than zero and the clock is not paused, we decrement the count by one.

```jsx
if (state.count === 0) {
  actions.reset()
  actions.toggle()
} else if (!state.paused) {
  actions.drop()
}
```
