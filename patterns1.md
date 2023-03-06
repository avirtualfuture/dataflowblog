### https://github.com/avirtualfuture
### [twitter](https://twitter.com/avirtualfuture)
# Dataflow patterns part I
## Intro
This article is about some dataflow graph patterns, some simple and obvious, and others not so much. My hope is that these graphs inspire solutions and conversations. None of the names or the graphs here are "established patterns", they are abstract examples to get you acquainted with some ideas around programming in this way.

# String of beads
![Screenshot 2023-03-06 002302.jpg]({{site.baseurl}}/Screenshot 2023-03-06 002302.jpg)
### Description:
This is a very basic pattern, it seems obvious, but the idea is to connect one node after the other. Similar to UNIX pipes, or nested function calls, however since a node can have multiple outputs the "string of beads" can have additional logic for multiple "lanes" of data.
I'm including this as a starter.


# Multiple inputs and outputs
![Screenshot 2023-03-06 002648.jpg]({{site.baseurl}}/Screenshot 2023-03-06 002648.jpg)
### Description:
Like a node, a graph can have multiple inputs and outputs anywhere. This can be hard go grasp at first coming from more traditional ways of programming. Sometimes it's desirable to separate a graph like this one into "string of beads" even if it means duplicating nodes for clarity.

### With node duplication:
![Screenshot 2023-03-06 005750.jpg]({{site.baseurl}}/Screenshot 2023-03-06 005750.jpg)

# Optional output
### Description:
Sometimes we want we want output a value only if another part of the graph succeeded. It could be writing to the output or triggering some expensive operation. "waitAll" is the kind of node that waits for all connected inputs to be ready before triggering the rest.
![Screenshot 2023-03-06 010717.jpg]({{site.baseurl}}/Screenshot 2023-03-06 010717.jpg)

# Subroutine
![Screenshot 2023-03-06 013058.jpg]({{site.baseurl}}/Screenshot 2023-03-06 013058.jpg)
### Description:
We can design nodes that delegate some operation to an external node as part of its design. This enables for more sophisticated graphs.
As an example consider a node that goes through an array of numbers and applies an operation on each element. If it wanted to do one operation at a time it could expose the value in a port, trigger the subroutine, and wait on the result to continue. This, like in any language that supports subroutines, provides a means for abstraction.

# Railway
![Screenshot 2023-03-06 024352.jpg]({{site.baseurl}}/Screenshot 2023-03-06 024352.jpg)

### Description:
This is a more abstract pattern with the goal if mediating the execution of a pipeline. Think of it as a string of beads where each "bead" is now a subroutine of a Wrap node. What the wrap node does is carry a result of a subroutine, and some context for the next Wrap node. This is inspired in functional programming concepts like Monads. In other words the Wrap node is a mechanism to redefine what edges between nodes in a string of beads mean. To make it more concrete, consider a string of beads where each node is doing some math operation and when one encounters an error the Wrap adds an entry to the context that there was an error. It can continue the pipeline with any value because at the end, if there are errors in the context, the graph can discard the output. The Wrap could also be logging anything that goes through it, passing along a secret key, and anything you can think of. It can let you do things like using nodes that have no side effects (pure functions), and do the IO at the end of the chain in the last Wrap, if everything went according to plan. It's a very powerful abstraction but the good news is that the power users will come up with reusable wrappers for anyone to use easily.
One of the ways to implement this in more concrete terms would be to use a "context" dictionary where each entry points to an array. When something is added, for example to the "error" key, it gets pushed into the array. This way we get a full history of what happened along the way.

# Output as configuration
![Screenshot 2023-03-06 013906.jpg]({{site.baseurl}}/Screenshot 2023-03-06 013906.jpg)
### Description:
An unintuitive way to configure a node is by connected outputs. Instead of configuring the node itself, we can use edges to tell the node the processing we need. It can also generate multiple results as we connect other outputs. If the node is aware of what is connected then it can process only what is needed. Coalescing the functionality to process several kinds of outputs can help with performance and graph simplicity. The node could be another graph itself or optimized code with several internal strategies.

# Timeout gate
![Screenshot 2023-03-06 014242.jpg]({{site.baseurl}}/Screenshot 2023-03-06 014242.jpg)
### description:
Dealing with asynchronous elements in a graph can create many issues. It can be IO or a human in the graph, etc. With a simple comparison we can control these issues. Not exactly a pattern but can inspire your own solutions.

# State Machine
![Screenshot 2023-03-06 014534.jpg]({{site.baseurl}}/Screenshot 2023-03-06 014534.jpg)
### Description:
A state machine is an useful abstraction for dealing with more complex logic in a stateful system. A lamp can be described as having two states like "on" or "off", however a semaphore could be described with four states: "off","red","yellow","green". Video games nest state machines for handling increasing complexity. A state can be "main menu", "loading", "paused", "playing", "game over", etc. For AI agents this approach has been in use for a long time. This pattern shows one of the ways to use state machines in a graph.
"stateRelay" in this case loads the current context that contains the current state and other state variables. If the current state is "A" then subgraph1 is triggered. stateRelay also provides the current context plus additional information to the decideNext node. Once subgraph1 is done, decideNext contains logic that based on outputs and current context decides the next state for the next event. The next step is storing the state.

## More patterns in the future.