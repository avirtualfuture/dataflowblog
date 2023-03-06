### https://github.com/avirtualfuture
### [twitter](https://twitter.com/avirtualfuture)
# Dataflow patterns part I
## intro
This article is about some dataflow graph patterns, some simple and obvious, and others not so much. My hope is that these graphs inspire solutions and conversations.
### "string of beads"
## description:
This is a very basic pattern, it seems obvious, but the idea is to connect one node after the other. Similar to UNIX pipes, or nested function calls, however since a node can have multiple outputs the "string of beads" can have additional logic for multiple "lanes" of data.
I'm including this as a starter.
