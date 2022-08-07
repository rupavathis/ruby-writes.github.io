---
layout: post
title: Are you infinetly stuck or missing dependency in useEffect?
---

If you have used class-based components in React, you would probably be aware of the lifecycle methods. In functional React, a page is rendered if the state is changed. 


>you can think of useEffect Hook as componentDidMount, componentDidUpdate, and componentWillUnmount combined.[More](https://reactjs.org/docs/hooks-effect.html)


useEffect hook runs after every render. So, how does the hook know when to run? useEffect has a parameter to let us mention the array of dependencies, if there's a change in the dependencies, the function within the useEffect will be called again. 

``` useEffect(() => {Function calls or definitions}, [Array of depedencies])```

If you want to run the useEffect only once after the first render, it is logical to leave behind the second parameter which will hold the list of dependencies, which when changed happens to trigger the useEffect again.

```
    function fetchAll(){
        //
    }
    useEffect(() => {fetchAll();});
```
Will this be a right approach? No, there are chances to land in infinite looping of useEffect.


## Infinite useEffect calls
Let's do a simple fetch and see how a simple mistake will hurt the performance!


```
export default function Gender() {

    const [genders, setGenders] = React.useState([]);

    const fetchGenders = async () => {
    const res = await fetch("/genders");
    const resJson = await res.json();
    console.log("I'm in fetchSetGenders");
    setGenders(resJson);
    };
  .....
  }
```

In the above function, we will fetch genders by making an API call and use useState to set the values fetched. The route of the API is defined elsewhere in the code which will not be covered in this article. Now, say we need to call the function ```fetchGenders``` within ```useEffect``` only once.

```
    useEffect(() => {
        fetchGenders
    });
```

Let's see what happens in the browser. 

![Infinte loops in the browser](/images/fetchGenders-Error.gif)

Oops! we have set-off the grenade! It runs infinitely!


It is not a wise idea to remove the dependencies. Let's add an empty array to the useEffect to tell there'e no need to run over and over again. 

```
  useEffect(() => {
    (async () => {
      const res = await fetch("/genders");
      const resJson = await res.json();
      setGenders(resJson);
      console.log("I'm in an anonymous func within useEffect");
    })();
  },[]);
```
Is that it? May be. There are situations this will break. To give an example, say a function is called within a function. Let's get back to ```fetchGenders``` function, and include one more function which will get called from ```fetchGenders``` 

```
     const fetchFromAPI = async () => {
        const res = await fetch("/genders");
        const resJson = await res.json();
        return resJson;
    });

    const fetchGenders = async () => {
        const gendersFetched = fetchGenders();
        setGenders(gendersFetched);
    };

    useEffect(() => {
        fetchFromAPI();
    },[]);
  ```

This will result in the below warning

```sh
    React Hook useEffect has a missing dependency: 'fetchFromAPI'. 
    Either include it or remove the dependency array
```



## Missing dependency error

 Check out what Dan Abramov has to say about lying about dependencies to React [here](https://overreacted.io/a-complete-guide-to-useeffect/#dont-lie-to-react-about-dependencies). 

>... if you specify deps, all values from inside your component that are used by the effect must be there. Including props, state, functions — anything in your component.

Some common suggestions is to disable the lint warning by adding ```// eslint-disable-next-line```. But no, we are not looking for workarounds!

We can fix this with ```useCallback``` or ```useMemo``` hooks. Let's stick with ```useCallback```


>when passing callbacks to optimized child components that rely on reference equality to prevent unnecessary renders. [More](https://reactjs.org/docs/hooks-reference.html)

```
    const fetchFromAPI = useCallback(async () => {
        const res = await fetch("/genders");
        const resJson = await res.json();
        return resJson;
    }),[]);
```

And finally let's add the depedency to the useEffect.

```
    useEffect(() => {
        fetchFromAPI();
    },[fetchFromAPI]);
```

Now, it's all good! I guess you need a little walk!







