# Learn Redux

Learn how to use Redux to write Predictable / Testable web apps.

> Note: these notes are aimed at people who already have "***intermediate***" ***JavaScript experience***.  
> If you are just starting out on your programming journey, we recommend you read:  
> https://github.com/nelsonic/learn-javascript *first* 
and then come *back* here!  
> :star: this GitHub repo so you don't forget where it is!


## Why?

Redux is a *logical* way to write *simplified* front-end web applications.
While there is an ***initial learning curve*** we feel the *elegance*
of the single store (*snapshot of your app's state*) offers a significant
benefit over other ways of organising your code.

> *Please, don't take our word for it,
skim through the notes we have made and*
***always decide for yourself***.

## What?

Redux<sup>1</sup> *borrows the* ***reducer pattern*** *from*
[***Elm*** Architecture](https://github.com/evancz/elm-architecture-tutorial/)
which simplifies writing web apps.
If you have *never heard of Elm*, don't worry,
you don't need to go read another doc before you can understand this...
Just keep reading this page and (*hopefully*) everything will become clear.


<sup>1</sup> <small> ***Redux*** *the JavaScript Library should not to be confused with the Redux Framework [PHP framework](https://github.com/reduxframework/redux-framework)  
... naming collisions are inevitable in the world of code.
naming things is a [hard problem](http://martinfowler.com/bliki/TwoHardThings.html)*</small>

### Three Principals

Redux is based on three principals.  
see: http://rackt.org/redux/docs/introduction/ThreePrinciples.html

#### 1. *Single* Source of Truth

The state of your whole application is stored in a single object tree; the "Store".  
This makes it *much* easier to keep track of the "*State*" of your application
at any time and roll back to any previous state.

> If its not *intermediately* obvious why this is a good thing,
we *urge* you to have faith and keep reading...

#### 2. State is *Read-Only* ("*Immutable*")

Instead of directly updating data in the store, we describe the update
as a function which gets applied to the existing store and returns a new version.

#### 3. Changes are made Using *Pure Functions*

To change the state tree we use "*actions*" called "*reducers*",
these are simple functions which perform a *single* action.


<br />

#### tl;dr

Read more about JavaScript's Reduce (Array method):
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce  
and how to reduce an array of Objects:
http://stackoverflow.com/questions/5732043/javascript-reduce-on-array-of-objects  
understanding these two things will help you grasp why Redux is so simple.

You will see this abbreviated/codified as `(state, action) => state`  
to understand what this means, watch: [youtu.be/xsSnOQynTHs?t=15m51s](https://youtu.be/xsSnOQynTHs?t=15m51s)


## How?


### *Video Tutorials* by Dan Abramov (*the Creator of Redux*)

The *fastest* way to get started with Redux is to watch the video tutorials
recoded by Dan Abramov (Creator of Redux) for
[egghead.io](https://egghead.io/series/getting-started-with-redux)

We have made a set of *comprehensive* notes:
[egghead.io_**video_tutorial**_***notes***.md](https://github.com/nelsonic/learn-redux/blob/master/egghead.io_video_tutorial_notes.md)

If you have the notes open while you are watching the videos you can
go a *lot* faster.  

*Please* give feedback and suggest improvements by creating issues on GitHub:
https://github.com/nelsonic/learn-redux/issues
*Thanks*!

#### 10. Avoiding Object Mutations with Object.assign() and ...spread

> Video: https://egghead.io/lessons/javascript-redux-avoiding-object-mutations-with-object-assign-and-spread

Like in the previous example I use `expect` and `deepFreeze` libraries
from NPM to make test assertions.
And this time I'm testing a function called `toggleTodo`
that takes a todo `Object` and *flips* its "*completed*" field.
So if `completed` was `false` it should be `true` in the returned value
or if it was `true` it should be `false`

```js
const toggleTodo = (todo) => {
	
};

const testToggleTodo = () => {
  const todoBefore = {
  	id: 0,
  	text: 'Learn Redux',
  	completed: false
  }
  const todoAfter  = {
  	id: 0,
  	text: 'Learn Redux',
  	completed: true
  }
  expect(
  	toggleTodo(todoBefore)
  ).toEqual(todoAfter);
}

testToggleTodo(); // run the test

```

Just like in the last lesson, I'm going to start by writing 
a *mutating* version that passes the current test.
So a *mutating* version just flips the `completed` field and re-assigns it on the passed `Object` (*the `todo`):

```js
const toggleTodo = (todo) => {
  todo.completed = !todo.completed;
  return todo;
}
```
And while it works, 
we know that *mutations* are ***not allowed*** in Redux.
So to *enforce* this, I'm calling `deepFreeze` on my `todo` Object 
(*in the case of the test `todoBefore`*) 
and I'm *not allowed* to change its `completed` field anymore.

One way out of this would be to create a new object 
with every field copied from the original object
*except* the `completed` field which would be flipped:

```js
const toggleTodo = (todo) => {
  return {
  	id: todo.id,
  	text: todo.text,
  	completed: !todo.completed
  }
}
```

However if we later add new properties to the `todo` object,
we might *forget* to update this piece of code to include them.

This is why I suggest that you use the **ES6**
[`Object.assign`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 
method which is *new* to **ES6** 
and it lets you `assign` properties of *several* objects onto the target object. Note how the `Object.assign` order corresponds to that of the JavaScript assignment operator. 
The left (*first*) argument is the one who's properties 
are going to be assigned, so its going to be *mutated*.
This is why we are passing an *empty* `Object` as the *frist* argument
so we don't *mutate* any *existing* data.
Every further argument to `Object.assign` will be considered a 
"*source*" `Object` who's properties will be copied to the target object.

It is ***important*** that if several *sources* specifiy 
different values for the *same* property, the ***last*** one "*wins*".
and this is what we use to *overwite* the `completed` field 
despite what the original object says.

```js
const toggleTodo = (todo) => {
  return Object.assign({}, todo, {
    completed: !todo.completed
  });
};
```

*Finally* you need to *remember* that 
[`Object.assign`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 
is a *new* method in **ES6** so it is *not natively available* in 
all the Browsers (*specifically not supported in Internet Explorer*).

![object assign-browser-compatibility](https://cloud.githubusercontent.com/assets/194400/12080810/6fbc745c-b25f-11e5-9e58-ed860ea2872a.png)

You should use a "*pollyfill*" either the one that ships with ***Babel***
or a *standalone* pollyfill to use it (`Object.assign`) without risking
crashing your website.

Another option that does not require a pollyfill is use 
the new [`Object` ***spread*** operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)
which is ***not part of ES6*** however it is *proposed* for **ES7**
it is "*fairly popular*" and it is *enabled* in Babel 
if you use the "*stage 2 preset*":

```js
const toggleTodo = (todo) => {
  return {
  	...todo,
  	completed: !todo.completed
  }
};
```

> Code at the *end* of Video 10:
[`index.html`]()

> **Note**: we have *not* used the `Object` spread operator in
our code because it does not run in *any* browser!!

<br />

## Background Reading / Watching / Listening

+ GitHub Project: https://github.com/rackt/redux
+ Online Documentation: http://redux.js.org/  
+ ***Interview*** with [@gaearon](https://github.com/gaearon) (*Dan Abramov - creator of Redux*)
on The **Changelog** Podcast: https://changelog.com/187 -
Good history and insight into his motivations for learning to program
and the journey that lead him to writing Redux.
+ Redux: Simplifying Application State in JavaScript -
https://youtu.be/okdC5gcD-dM (*good overview by* [**Tim Griesser**](https://github.com/tgriesser) December 2015)
+ Full-Stack Redux Tutorial (Redux, React & Immutable.js) by
[@teropa](https://github.com/teropa)
http://teropa.info/blog/2015/09/10/full-stack-redux-tutorial.html - really good but takes 4h+!
+ Single source of truth: https://en.wikipedia.org/wiki/Single_Source_of_Truth
+ Redux Undo: https://github.com/omnidan/redux-undo


## Notes:

At the time of writing, the *minified* version of redux is 5.4kb and has
No Dependencies.
[![Dependency Status](https://david-dm.org/rackt/redux.svg)](https://david-dm.org/rackt/redux)  
We like this. It means the Library is *self-contained* ("*stand-alone*") and you can read/understand it quite easily.

... Unidirectional Data Flow (*why is this better than bi-directional e.g: Angular.js*)

## Kudos

> Props to [Rafe](https://github.com/rjmk) for telling us about Redux and Elm: https://github.com/rjmk/reducks *before* it was *cool*   
> Thanks to [Milo](https://github.com/bananaoomarang) for his 
*fantastic* demo: https://github.com/bananaoomarang/isomorphic-redux  
> and *love* to [Niki](https://github.com/nikhilaravi) for her enthusiasm
while explaining it all to us ... 
