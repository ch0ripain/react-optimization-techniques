![image](https://github.com/user-attachments/assets/f5ac2d73-0e50-45f2-abf0-3f696f936484)<h1 align="center">🧙‍♂️ React optimization techniques 🧙‍♂️</h1> 
In this project i put hands-on working in React optimization techniques going throught memo(), useMemo(), clever structuring and one optimization compiler called Million js

## ⚙️ Using memo() ⚙️
Memo is a react library functionality that allow me to avoid re-renders when the props doesn't change.
const IconButton = memo(function IconButton({ children, icon, ...props }) {
  log("<IconButton /> rendered", 2);

  const Icon = icon;
  return (
    <button {...props} className="button">
      <Icon className="button-icon" />
      <span className="button-text">{children}</span>
    </button>
  );
});

export default IconButton;

How it seen above memo need our component function as a argument to work and that's all. 
What memo do is to memoize our component so in the next re-renders caused by dad components this memoized component will not be re-rendered as long as their props don't change.
Looking at their props we see
- children => who never changes since it's a static value
- icon => also never changes cause it's a external svg component wich is always the same icon
- ...props => here we have a onClick functionality that leads to a function wich will change in each re-render

To avoid those unnecesary re-renders caused by function recreation we need to involve that function in a useCallback()

  const handleDecrement = useCallback(function handleDecrement() {
    setCounter((prevCounter) => prevCounter - 1);
  }, []);

And with all that memo is going to be used in the right way

> [!NOTE]
> It's a common practice to wrap our functions props in useCallback() whilst using memo

## 🧩️ Clever Structuring 🧩️
Sometimes it could be normal to want to wrap all our components in a memo() function but that will result unproductive and highly unrecommended since memo just need to be use in the most topside component that group few or a bunch of components that don't need to be re-rendered and we can manage that behaviour on a common component with memo().
const Counter = memo(function Counter({ initialCount }) {
...
}
export default Counter;

This Counter component on my react app will always be changed by that prop since the counter prop is always changing because that the behaviour i want in my app.
Using memo() here it's kind of useless because there is no useful cases to avoid re-renders.

In this case we can opt for use a clever structuring way.
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
        <Counter initialCount={chosenCount} /> //notice that this component is always being re-rendered caused that chosenCount prop changing
      ...
    </>

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
        <ConfigureCounter onSet={handleSetCount} /> //now all that state behaviour are on their own component
        <Counter initialCount={chosenCount} /> //Counter now is not being re-rendered always just when it need it
      </main>
    </>

As you can see in the previous examples memo() and clever structuring could be very useful in some scenaries and more powerfull to improve our react app perfomance.

## ⚙️ useMemo ⚙️ 
In our react app it's very common to have some complex and longe functions which take some computational time so re-executed that function every time a component is re-rendered it could be a performance downgrade.
const initialCountIsPrime = useMemo(
    () => isPrime(initialCount),
    [initialCount]
  );
useMemo() need a function wich must return the computed value we want to memoize and the an array of dependencies (deps) to re-execute that function only when it's need it.

So that all for this project, in conclusion:
- memo() => try to memoize a commmon component and take a look to the props to avoid unnecesary re-renders like functions recreations or state always changing (clever structure)
- useMemo() => memoize the computed value of a longe and complex function that might take some time and only be re-executed when a deps change.
- Clever Structure => Let me to make a more leaner and readable code leveraging the react thinking way and avoiding unnecesary hooks.

<p align="center">🌟 This project is a practice exercise I learned from the <a href='https://www.udemy.com/course/react-the-complete-guide-incl-redux/?couponCode=ST7MT110524'>Academind's React Course</a> 🌟</p>
