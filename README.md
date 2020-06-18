--- React Hooks

useState returns an array, this ALWAYS returns 2 elements
The first element will always be your current state so basically a snapshot of your state.
The second element is a function which allows us to change, override our current state

// Use destructuring (state, function to call to update state)
// You can name state1 / setState1 however you want
// state1 and setState1 elements are always returned in an array when we use useState
const [state1, setState1] = useState({
  test1: '1',
  test2: '2',
  test3: '3'
})

you can have multiple useStates per component

useState does not need to be an object, you can for example just have it as a string. either is ok but if updating object for exampl you will ned to merge state as before i.e. setState1({...state1, test2: 'blah'})

i.e.

const [anotherState, setAnotherState] = useState('light')

```
// Very Basic example

import React, { useState } from 'react'

const App = props => {
  const [userName, setUserName] = useState('Matt') <- Matt as default state

  // If you need componentWillMount you can simply add the code here.

  const [state2, setState2] = useState({
    test1: '1',
    test2: '2',
    test3: '3'
  })

  const changeNameHandler = name => {
    setUserName(name)
  }

  const changeState2Handler = () => {
    setState2({
      ...state2,
      test2: 'Updated'
    })
  }

  let content = (
    <React.Fragment>
      <button onClick={changeNameHandler.bind(null, 'Bob')}>
        Change name to Bob
      </button>

      <button onClick={changeState2Handler.bind(null)}>
        Change state2, test2 to updated
      </button>
    </React.Fragment>
  )

  return content
}
```
----
- useEffect / Class lifecycle methods

useEffect takes a function as a argument, this function will be executed by react on every render cycle AFTER the component has been rendered so it will render AFTER the return content in the example above

lifecycle method, componentdidmount etc use `useEffect`, it works a bit different in that componentDidMount will only run once, how ever useEffect will run with a re render aswell (When props or state changes), this means for example you will get an infinite loop if your fetching data and updating state here, this is on purpose but can be fixed / amended to what you need. See below

useEffect ALSO takes a second argument which is an array of dependancies we passed to the function argument of useEffect, so it's basically an array where we have to state the variables we use in the first arguments function or not, which will in turn decide whether we want to run the function or not. So anything that we pass to that array as a variable should basically decide if we run that function on a render or not. So if we pass a variable here and the variables value changes then the function you passed to useEffect should run again, so if you pass an empty array here it will work the same as componentDidMount and only call the function once because there are no variables in there and nothing is changing

See below if not clear:
https://reactjs.org/docs/hooks-effect.html
```
// This will work the same as componentDidMount, notice the empty array
useEffect(() => {
  // Function to call after every render cycle
}, [])
```

```
// This will run whenever test1 is changed and on initial render
useEffect(() => {
  // Function to call after every render cycle
}, [test1])
```

If you need `componentWillMount` you simply add the code before the component returns jsx content in the example above, as the code runs from top to bottom.


componentDidUpdate(prevProps)
this was responsible for listening for updates to the component i.e.
```
componentDidUpdate(prevProps) {
  if (prevProps.test1 !== props.test1) {
    // Do something
  }
}
```

Again we can use useEffect instead of this, but this time want it to update whenever props.test1 is changed.

So below here whenever props.test1 changes & on initial render we want to run the function passed in as first argument. You can add MULTIPLE useEffects
```
useEffect(() => {
  // props.test1 was updated so do something
}, [props.test1])
```

ComponentWillUnmount. - This can also be done with useEffect
useEffect can also return a function. This function will NOT be called on the first useEffect call. It runs right BEFORE useEffect runs the next time. So think of it like your passing that return function to the next call of useEffect.

call useEffect() first time (return function Doesn't run) -> Call useEffect() again (The return function is called BEFORE useEffect() is called), will always do this from here. So in effect this return function will always be the last thing the component runs before it's removed which is why you can add clean up here.
```
useEffect(() => {
  // props.test1 was updated so do something
  return () => {
    // For cleaning up, removing listeners etc.
  }
}, [props.test1])
```

So if you just want this to run when the component unmounts. you can make another useEffect. Remember the return function is NOT called the first time in this case when we mount. Only when we unmount, which an empty array will also be called when the component unmounts. having this in a seperate useEffect will mean this only runs twice

```
useEffect(() => {
    return () => {
      console.log('component did unmount')
    }
}, [])

```


ShouldComponentUpdate / React.memo
```
// Lets say for example the component should ONLY update if a number on someNumber has changed
ShouldComponentUpdate(nextProps, nextState) {
  console.log('shouldcomponentUpdate')
  return (
    nextProps.someNumber !== props.someNumber
  )
}
```

What we can use React.memo(SomeComponent) methos, this memorizes this component, which means it basically stores it and only when inputs to this component i.e. props change it will rerender this. Basically if you dont use a prop on this component but the prop is updated normally this would still rerender the component however with this it will only render if a prop used in the component is changed. It will just automaitically know when to update, rather than doing it manually with shouldcomponentupdate, like pure component

so
`export default Reacat.memo(SomeComponent)`

You can get more control by also passing a function as a second argument,
Note: the below is redundant in this example as its done automatically but good for reference.
This can be useful though for example if you pass in props to a component but dont want it to rerender on a prop change but 99% of the time you probably do.
`export default React.memo(SomeComponent, (prevProps, nextProps) => {
  // This should return TRUE if the props are EQUAL and SHOULDNT re render
  // Or return FALSE if it should re render
  return (
    // Notice this is the opposite of shouldComponentUpdate logic, so true is to NOT update rather than true being to update
    nextProps.someNumber === prevProps.someNumber
  )
})`

-- Custom Hooks
```
- components
  - somecomponent.js
- hooks
  -somehook.js
```
- Hooks MUST always be called on top level of functional component. (Cant NOT be used for example in a function of a functional component or an if / for statement), you ARE allowed to use other hooks though on the top level of your own hooks i.e. useEffect within your own hook
Look at https://github.com/academind/react-hooks-introduction/tree/custom-hooks custom hook here for fetching data, https://www.youtube.com/watch?v=-MlNBTSg_Ww - 48min
Hooks can make sharing stateful logic between components become easier

For example with http fetch requests
you should use `use` for custom hooks
