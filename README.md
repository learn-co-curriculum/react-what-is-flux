What the Flux?
==============

In this lesson, we will learn about the Flux architecture for building web
applications. By the end of the lesson you will be able to:

* Describe the basics of a flux application.
* Explain how data moves around in a flux application.
* Explain how the stores different from "models" in MVC.
* Explain the role of the dispatcher.

## Architecture?

Above, we stated that we'll be learning about a thing called "Flux architecture."
You may have heard of Flux, but you may be wondering why we are calling it an
"architecture". Perhaps this sounds a bit grandiose.

And yet what we are dealing with here is rather grandiose! Because unlike React
components, which are small modular parts of a UI, with Flux we have moved to a 
different problem: namely, the way events and data will circulate through our 
entire application.

![Colorful Circuit Animation](https://media.giphy.com/media/xTiTnxpQ3ghPiB2Hp6/giphy.gif)

## So What is Flux?

To understand what Flux is take a look at the animation shown above. It's a beautiful
system to look at, but it's very (very!) complex. If we were asked to look at this
animation and then reproduce its structure from memory, chances are that 99%
of us would fail at the task. You simply can't keep the "idea" of this architecture
in your head.

By contrast, Flux is a system _architecture_ that strives for simplicity. The idea
is that we should be able to keep the basic idea of the system in our heads.
So what is this simple structure? Here it is in a nutshell:

![Simplest Flux Diagram](https://ezmiller.s3.amazonaws.com/public/images/flux-simplest.jpg)

Several things may jump out at you here, especially if you have worked with the
**M**odel **V**iew **C**ontroller pattern in the past. What on earth is a "store"?
Don't worry, we'll get to that. What I want to highlight for the moment is the
way data flows through this system.

Take a look at the arrows. Notice that they are all moving in the same direction.
What we are seeing here is nothing less than the core idea of the Flux architecture:
data and events in Flux only ever move in one direction. When an action is
triggered, by a user, its effects are propagated through the dispatcher, to the store, and
finally back to the views.

Did you get that? No...but really did you? Do you see how crazy simple that is?
Let's put it another way: the only way any action will ever move through a Flux
app is in this way. So no matter what it is that needs to be built, or debugged,
or improved in the app, the only pathway that the developer will ever have to
understand and tweak is this one. Therein lies the beauty and simplicity of Flux.

## What is Flux to React?

Given that this is a course on React, you may well be wondering what Flux has
to do with React. The short answer is that the two do not necessarily need to
be paired together. Flux doesn't need React, and React certainly doesn't depend
on Flux.

That said, both React and Flux were initially developed and open-sourced by Facebook
and Flux was conceived with React in mind. The blog post in which Facebook announced
Flux, moreover, was called ["An Application Architecture for React"](http://facebook.github.io/react/blog/2014/05/06/flux.html).

So how are they related? If we think about it a bit, moreover, it's easy to see
why they fit so well together. Can you guess why?

Remember that one of the key characteristics of React components is that we always
pass data down into them via `props`. In other words, React, like Flux, enforces
the  key idea of unidirectional data flow. That means that React fits snugly into
place in our Flux diagram as the "view" layer:

![Flux With React Diagram](https://ezmiller.s3.amazonaws.com/public/images/flux-with-react2.jpg)

## Of Actions, Dispatchers, and Stores

Now that we know that React often plays the role of the view layer in a Flux
application, you are probably wondering about stores, actions, and the dispatcher.
Let's go through these one by one:

#### Actions

Actions are more or less what they sound like: entities that represent some sort
of action that is going to propagate through the system, producing changes in the
UI. One thing that we should keep in mind, however, is that an action can result
either from user interaction &mdash; for example, a user click -- or an action 
can result from some sort of data update on the backend. Because this is Flux,
though, no matter where the action originates it will always move through the
system in the same way: it will be "dispatched" to the dispatcher.

#### The Dispatcher

The dispatcher is probably the most important piece of the Flux architecture. As
Facebook's Flux documentation says, it is "the central hub that manages all
data flow in a Flux application." The key role that the dispatcher plays is to
control when an action is pushed through the system. Essentially, it is the 
traffic cop of the Flux system.

![Dancing Traffic Cop](https://media.giphy.com/media/jxooXMa2kAiys/giphy.gif)

As a traffic controller, the dispatcher's overriding concern is to ensure one
single rule: **only one action can move
through the system at a time.** As such, once the dispatcher has dispatched
an action to the stores, it will block any other action from being dispatched until
the stores have updated. If the dispatcher receives an action, while another action
is still processing, it will queue that action to be dispatched at a later moment.

Now here we should pause and reflect a bit. This constraint is in fact
quite significant. By guaranteeing that only one action moves through
the system at a time, we add whole new degree of stability and
predictability to our system. Think about it. Let's say we have an Ecommerce app.
A user using the app adds an item to their cart, but because the user is so
enthusiastic about their purchase, they immediately click the checkout button.
Now imagine that due to some sluggishness on the server, the new product had
not actually been added to their cart at the point the checkout action propagated
through the system. Now we're in trouble. Our user, who was once so excited,
has instead become frustrated and confused. Not good.

In older web architectures, including the MVC architecture, we would have had to build
in a whole set of checks to see if an "add to cart" action was still ongoing before
allowing the checkout to proceed. With Flux this is not necessary! Why? Because
the dispatcher **always** ensures that every previous action has fully updated
the state of application before another moves through the system.

Put in slightly more technical terms, what this means is that state changes
in the app are stable and discrete. Each action puts the app into a new state,
and therefore as each action moves through the system it can expect that nothing
else will alter the data that it seeks to update.

## Stores

The last piece of the Flux architecture that we need to learn about are the
Stores. The quick description of a store is simple: they contain application
state and business logic. Easy. There's a bit more to it though.

If you have ever worked with the MVC pattern, you might be wondering
if Flux "stores" are the same as "models." They are similar, but there are key
differences. Whereas models usually represented an ORM-type object (e.g. a UserModel),
stores tend to take responsibility for a specific **domain** of application's state.
For example, in a ToDo app, you would most likely have a ToDos Store. Rather than
being a representation of a database ORM object, the ToDos store manages a collection
of ToDos. It manages that part of the applications state.

Then there is the matter of how the Stores fit into the flow of data through the
system. We've already learned that the Dispatcher is responsible for dispatching
actions, one at a time. When the Dispatcher dispatches an action in a Flux application,
it sends the action to the store(s). The way this works is that each store registers
itself with the dispatcher. When the dispatcher dispatches, each registered store 
is notified of the new action. If the action is one that the store needs to react to,
then it will execute all the necessary business logic, updating its state if
necessary. Finally, when the results of the action have been fully processed,
the store emits an event indicating that it has updated, indicating that a new
action can move through the system.

## Summing Up

So here we have a broad overview of the Flux architecture. We'll get a chance to
familiarize ourselves with the individual pieces. For now focus on the big ideas:
* Data flow is unidirectional in Flux architecture.
* Only one action can move through a Flux system at a time.

## Resources

- Facebook React Blog, [An Application Architecture for React](http://facebook.github.io/react/blog/2014/05/06/flux.html)
- Facebook Flux Documentation, [The Flux Application Architecture](https://facebook.github.io/flux/docs/overview.html)
- Facebook Flux Github Readme, [How Flux Works](https://github.com/facebook/flux#how-flux-works)
