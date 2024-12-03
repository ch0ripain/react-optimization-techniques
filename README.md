<h1 align="center">🧙‍♂️ React optimization techniques 🧙‍♂️</h1> 
In this project i put hands-on working in React optimization techniques going through <code>memo()</code>, <code>useMemo()</code>, clever structuring and one optimization compiler called MillionJS

## ⚙️ Using <code>memo()</code> ⚙️
<code>memo()</code> is a utility provided by React that helps prevent unnecessary re-renders of a component when its props remain unchanged.
```javascript
const IconButton = memo(function IconButton({ children, icon, ...props }) {
  ...
});

export default IconButton;
```

In the example above, <code>memo()</code> takes a component function as an argument and memoizes it. When a parent component re-renders, the memoized component won't re-render unless its props change.
- children ➡️ This is static and does not change.
- icon ➡️ This is an external SVG component, always the same.
- ...props ➡️ Includes an onClick handler that could change on every render due to function recreation.
- 
To address unnecessary re-renders caused by recreated functions, you can wrap the function in useCallback():

  const handleDecrement = useCallback(function handleDecrement() {
    ...
  }, []);

With this approach, <code>memo()</code> is used effectively.

> [!NOTE]
> It's common practice to wrap functions passed as props in useCallback() when using <code>memo()</code> or useEffect()

## 🧩️ Clever Structuring 🧩️
Sometimes it can be normal to want to wrap all our components in a <code>memo()</code> but that will be unproductive and highly inadvisable since memo only needs to be used in a key component that groups a few or a group of components that do not need to be re-rendered. We can handle all that behavior in a common component with <code>memo()</code> if we think it is the better approach.
```javascript
const Counter = memo(function Counter({ initialCount }) {
...some components
}
export default Counter;
```
This Counter component in my React app will always be modified by that property, since the counter property is always changing because that's the behavior I want in my app.
The use of <code>memo()</code> here is a bit pointless because there are no useful cases to avoid re-rendering.

In this case a better approach will be a clever structure

```javascript
//CODE WITH MEMO
function App() {
  log('<App /> rendered');

  const [enteredNumber, setEnteredNumber] = useState(0);
  const [chosenCount, setChosenCount] = useState(0);

  function handleChange(event) {
    setEnteredNumber(+event.target.value);
  }

  function handleSetClick() {
    setChosenCount(enteredNumber);
    setEnteredNumber(0);
  }

  return (
    <>
      ...
      <section id="configure-counter">
          <h2>Set Counter</h2>
          <input type="number" onChange={handleChange} value={enteredNumber} />
          <button onClick={handleSetClick}>Set</button>
        </section>
        <Counter initialCount={chosenCount} /> //Notice that this component is being re-rendered caused that chosenCount prop is changing frequently caused by some state
      ...
    </>
```
```javascript
//CODE WITH CLEVER STRUCTURING
function App() {
  log("<App /> rendered");

  const [chosenCount, setChosenCount] = useState(0);

  function handleSetCount(newCount) {
    setChosenCount(newCount);
  }

  return (
    <>
      <Header />
      <main>
        <ConfigureCounter onSet={handleSetCount} /> //Now all that state behaviour are in a separate component.
        <Counter initialCount={chosenCount} /> //Counter now only re-renders when its props actually change.
      </main>
    </>
```

## ⚙️ useMemo ⚙️ 
It is very common to have some complex and long functions that consume some computational time, so re-executing that function every time a component is re-rendered could lead to a performance downgrade.
```javascript
const initialCountIsPrime = useMemo(
    () => isPrime(initialCount),
    [initialCount]
  );
```
<code>useMemo()</code> needs a function that should return the computed value we want to memoize and an array of dependencies (deps) to re-execute that function only when its dependencies change.

## ⚡️ Million JS - Speed up your website by 70% ⚡️
Million.js replace React's virtual DOM with a faster, lightweight virtual DOM, enabling more efficient updates and reducing rendering overhead.

<code>npx million@latest</code>

On installation, you'll be prompted to install the Million Lint build plugin and VSCode extension.
That's it! You're all set up 🎉

[See MillionJS docs](https://million.dev/docs)


That's all for this project, in conclusion:
- <code>memo()</code> ➡️ try to memoize a common component and keep an eye on the properties to avoid unnecessary renderings like function recreations (useCallback()) or frequently state changes (clever structuring)
- <code>useMemo()</code> ➡️ memoize the computed value of a long and complex function that can take some time and only re-execute when a dependency changes.
- Clever Structuring ➡️ Allow me to make more agile and readable code by taking advantage of React's way of thinking and avoiding unnecessary hooks.

<p align="center">🌟 This project is a practice exercise I learned from the <a href='https://www.udemy.com/course/react-the-complete-guide-incl-redux/?couponCode=ST7MT110524'>Academind's React Course</a> 🌟</p>
