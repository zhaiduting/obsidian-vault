---
source: https://medium.com/@fvictorio/how-to-write-a-plugin-for-prettier-a0d98c845e70
author:
  - Franco Victorio
published: 2019-01-28
created: 2025-09-27
tags:
  - clippings
---

[![Franco Victorio](https://miro.medium.com/v2/resize:fill:64:64/1*BMq7JxUr_oklWLzdH3O5pg.png)](https://medium.com/@fvictorio?source=post_page---byline--a0d98c845e70---------------------------------------)
[Written by Franco Victorio](https://medium.com/@fvictorio?source=post_page---byline--a0d98c845e70---------------------------------------)

In this post I will show you how to build a very simple plugin for [Prettier](https://prettier.io/). You will learn how to structure, test and, of course, write a plugin. I’m far from being an expert on this, but I learned a lot by contributing to the [Solidity plugin](https://github.com/prettier-solidity/prettier-plugin-solidity/), and noticed that there doesn’t seem to be a lot of material on this subject, except for the official documentation.

We’ll be using [TOML](https://github.com/toml-lang/toml) as the example language. I chose TOML because it has an easy syntax and, as far as I know, there’s no Prettier plugin for it. The result won’t be a usable plugin, as you’ll see, but with any luck you will learn enough to develop a proper one.

Why would you want to learn this? Well, there are two strong reasons. First, you’ll be able to create a plugin for any language that is not supported yet (like TOML and Dockerfile, but there probably are many others). And second, you’ll be able to contribute to one of the [existing plugins](https://prettier.io/docs/en/plugins.html#official-plugins). Moreover, since the core of Prettier itself is written using the plugin API, you could even contribute to the main repository if you wanted to.

## How Prettier works?

At its core, what Prettier does is very simple: it takes some code (a string), converts it to an AST (Abstract Syntax Tree, a representation of the code) and then prints the code using only the AST. That means that the style of the original code is (almost) completely ignored. You can learn more in the [original blog post](https://jlongster.com/A-Prettier-Formatter).

For our purposes, the important part is that we need a parser that transforms the code to an AST, and a function that takes this and pretty-prints it. Our initial setup already has a configured parser, using [toml-node](https://github.com/BinaryMuse/toml-node), so we only need to worry about the printer function.

## Setup

First we’ll clone [this repository](https://github.com/fvictorio/prettier-plugin-toml) that has all the boilerplate you’ll need to start. I’ll explain its contents soon. After cloning it, go the top level directory and run `npm install` to install the dependencies. You should now be able to run the plugin in the example file (`example.toml`) with this command:

```sh
./node_modules/.bin/prettier --plugin . example.toml
```

There’s also a npm script for this, so `npm run example` should work too, but this shows how to run the plugin in any file you want.

After running the command, you won’t see any output, and that’s OK. For now, our plugin doesn’t emit anything: when the printer function receives the AST, it just returns an empty string.

There’s also an initial test that you can run with `npm test`. Our tests will be written in jest, using [snapshots](https://jestjs.io/docs/en/snapshot-testing), but since the setup is already there the only thing you have to do is to add new fixtures. This initial test will format the contents of `tests/StringAssignements/example.toml` and compare the result with the expected output in the snapshot. All of our tests will be like this one: a TOML file and a snapshot with the correct format. This test will fail, of course, but our first goal is to make it pass.

All the code we’ll write will be in the `src/index.js` file. In fact, everything will be inside a single function: `printToml`. You can take a look at the rest of the file, but don’t worry about the details. If you are curious, it’s all explained [here](https://github.com/prettier/prettier/blob/master/docs/plugins.md#developing-plugins).

And if you want to read the finished code instead of writing it, just checkout the `finished` branch instead.

## The printer function

The `printToml` function is very simple. It takes three arguments:

- `path`, that represents a node in the AST
- `options`, that represents the configuration given to prettier (the combination of `.prettierrc` and the flags given to the command, among other things)
- and `print`, that is how we call the printer function recursively

Notice that I said that `path` is **some** node in the AST, not the root. That’s because the function is called recursively. For example, if I have the body of a function, I may want to pretty-print each individual statement separately and then do something with this result. This will become clearer as we continue.

This is the boilerplate of our function:

```js
function printToml(path, options, print) {
  const node = path.getValue();

  if (Array.isArray(node)) {
    return concat(path.map(print));
  }

  switch (node.type) {
    default:
      return "";
  }
}
```

The first line just extracts the AST node from the `path`. This is because `path` has some extra information and logic related to the AST node.

Then we have a strange block that checks if the node is an array. This will only be necessary in the initial call, because the parser that we are using represents the code as a list of nodes, not as a tree of nodes. Don’t worry about this, but keep it in mind, because later this will impose some serious limitations to our plugin.

Finally, we have the switch. Here’s where we’ll spend most of our time. The logic we have is very simple: we check the type of the AST node and act accordingly. Let’s start to fill it in.

## A simple assignement

If you take a look at our test, you’ll see that it contains two key/value pairs. The node that represents the first pair is something like this:

```js
{
	type: 'Assign',
	value: {
		type: 'String',
		value: 'TOML Example',
		line: 1,
		column: 9
	},
	line: 1,
	column: 1,
	key: 'title'
}
```

(How do we know this? There are a lot of ways of obtaining it: a good old `console.log`, using the parser in the node REPL, or running the plugin using [ndb](https://github.com/GoogleChromeLabs/ndb) and inspecting the value.)

There are two interesting things here. First, the `type` property, that is what we use in our switch. The second one is that, while the `key` of our pair is a simple string, _our value is another AST node_, whose type is `String`.

So the first thing we’ll do is to add a clause for `Assign` nodes:

```js
case 'Assign':
  return concat([node.key, ' = ', path.call(print, 'value'), hardline])
```

There’s a lot to unpack here, but the main idea is easy to grasp: we are telling prettier that an assignment is printed by concatenating four things:

- The key. Remember that this is just a plain string
- A literal equal sign, padded with spaces
- The result of pretty-printing the value of the assignment, whatever that is
- And a `hardline`

What are `concat` and `hardline`? They are called **builders**, and they are functions and values —exposed by Prettier— that we use to build the result we want. We already have imported `concat`, but we need to add `hardline` to the list of builders we are using:

```js
const {
  doc: {
    builders: { concat, hardline },
  },
} = require("prettier");
```

The `concat` builder is easy to understand: it tells prettier to just concatenate the list of parts it’s given. And `hardline` just means “put a line break”, no matter what. You can see the full list of builders [here](https://github.com/prettier/prettier/blob/master/commands.md).

What about the `path.call(print, 'value')` part? This is a prettier idiom and it just means “call the printer function recursively, using the node that is in the `'value'` key”. Why can’t we just do `print(node.value)`? Well, remember that the printer function expects a _path_, that is, a wrapped node, not a node. So you have to do it like this.

If we add just this and run our test, it will fail. The diff tells us that the keys and the equal sign were printed, but not the value. This makes sense, since the values are nodes of type `String` and we don’t have a clause for that yet. Fortunately, that clause is very simple. Take a look at the AST sub-node again and see if you can guess it.

Yes, it’s that easy:

```js
case 'String':
  return concat(['"', node.value, '"'])
```

You may have guessed just `return node.value`, but that would’ve been wrong, because in that case we would be printing just the content of the string, not the full string. For example, `foo = "bar"` would’ve been printed as `foo = bar`.

If we run our test again, it should pass now.

## Adding support for other values

TOML supports other data types besides strings, and we should support them too. If you look at the example in the root directory, you’ll see that it has numbers, booleans, dates, and lists.

Numbers and booleans are easy:

```js
case 'Integer':
  return node.value.toString()
case 'Boolean':
  return node.value.toString()
```

We have to convert them to strings, because that’s what prettier expects, but that’s it.

Dates are a little trickier and here we’ll run into the first limitation of the parser we are using. Here’s the AST representation of a date assignment:

```js
{
	type: 'Assign',
	value: {
		type: 'Date',
		value: 1979-05-27T15:32:00.000Z,
		line: 5,
		column: 7
	},
	line: 5,
	column: 1,
	key: 'dob'
}
```

Look at the value of the date. That is a `Date` object, a unique representation of a date. But if you take a look at the [TOML spec](https://github.com/toml-lang/toml), you’ll see that you can specify dates in many different formats. That is lost to us during parsing, so we’ll always print dates with the same representation.

```js
case 'Date':
  return node.value.toISOString()
```

That’s not nice at all! But to do it properly, we should know the original representation of the date. We could get it using the position of the node and the original text (that we receive in `options.originalText`), but it would be even better to have a parser that keeps the original value in the AST. Since our parser doesn’t do this, we’ll have to settle for this.

## Tables

In TOML, we can separate different sections with what the spec calls “Tables”, but our parser assigns the type `ObjectPath`. An AST node looks like this:

```
{
	type: 'ObjectPath',
	value: [ 'owner' ],
	line: 3,
	column: 1
}
```

As you see, the value of the node is not a string but an array. This is because we can have nested sections like `[servers.alpha]`. We print this with the following clause:

```js
case 'ObjectPath':
  return concat(['[', node.value.join('.'), ']', hardline])
```

Nothing new here. We join each part of the `value` with a period and surround everything with square brackets.

## Arrays

So far, everything we’ve done has been very straightforward. Arrays are a little more complex, and we’ll have to make some decisions. There are several ways in which an array can be printed, for example:

```
arr1 = [1, 2, 3]
arr2 = [ 1, 2, 3 ]
arr3 = [1,2,3]
arr4 = [
	1,
	2,
	3
]
```

Here’s what prettier usually does in situations like this: if the array fits in one line, print it in one line. Otherwise, print everything on its own line. So we’ll do the `arr1` approach when the array fits, and we’ll print something like `arr4 `when it doesn't.

That seems hard, doesn’t it? But prettier can help us. This is the clause that does what we want:

```js
case 'Array':
  return group(
    concat([
      '[',
      indent(
        concat([
          softline,
          join(concat([',', line]), path.map(print, 'value'))
        ])
      ),
      softline,
      ']'
    ])
  )
```

I know this is quite a jump from what we’ve been doing so far. And the bad news is that a lot of plugin code looks somewhat like this. The good news is that you get used to it quickly.

Let’s start from the innermost part of that expression and work our way out.

First we have a `path.map(print, 'value')` expression. This is similar to the `path.call` idiom we discussed before, but here we are saying “in the `value` key of this node I have an array of sub-nodes; call the printer function on each one and give me an array with the results”. In other words, it’s like doing `node.value.map(print)`, but remember that we can’t do that.

So we have an array with the result of pretty-printing each element in our list. The next step is adding our commas. We use the `join` builder for that. Its signature is `join(separator, list)`, and it just joins the list of parts with the given separator. For example, `concat(["1", ",", "2", ",", "3"])` is equivalent to `join(",", ["1", "2", "3"])`. And we could do that here, right? Just `join(",", path.map(print, 'value'))`. But we want to have a space after the comma when the list fits in one line, and a line break when we split it. That is done with the `line` builder, and that why we join by `concat([",", line])`. The documentation is clear:

> Specify a line break. If an expression fits on one line, the line break will be replaced with a space. Line breaks always indent the next line with the current level of indentation.

So we print each value separated by a comma and a space if the list fits in one line, and we replace the spaces with line breaks if it doesn’t fit. We should be ready, right? Just add the opening and closing square brackets and be done with it. Well, no. Because we want to indent each element of the list when we split it.

We do that by surrounding what we have done so far with `indent(concat([softline, ...]))`. What is going on here? First we put a `softline` at the beginning of the list. `softline` is very similar to `line`, but the difference is that, if everything fits in one line, `softline` is replaced with an empty string. We also use the `indent` builder, that just increases the indentation. When everything fits in one line, we won’t have line breaks, so `indent` won’t do anything.

Almost there! After that, we surround everything with `concat('[', ..., softline, ']')`. We are just adding the brackets. We also add a `softline` before the closing bracket, and since it’s outside the `indent` builder, the `]` will have the same indentation we started with. Otherwise our lists would look like this:

```
arr = [TOML spec
	1,
	2
	]
```

And **finally** we surround everything with a call to `group`. This is a builder that tries to fit everything inside it in one line. If it doesn’t, it will start replacing lines and softlines with line breaks. It’s actually a little more complex, but that explanation will do for now. Check the documentation to see the nuances of it.

Again, this seems hard, but you’ll get it quickly when you start playing with prettier. All of this also shows how powerful prettier is, if you think about it. We’ve used just a few building blocks to pretty-print any list. In fact, this will work even with nested list, no matter how deep they are!

## Aside: How to experiment

How can you check how builders interact, besides reading the documentation and running your full plugin with some examples? It turns out you can use the node REPL to interact with prettier. First start the REPL and import some stuff:

```
> const prettier = require('prettier')
> const print = prettier.doc.printer.printDocToString
> const { concat, group, join, line, softline } = prettier.doc.builders
```

And then you can experiment with the builders:

```
> print(concat(['foo', 'bar', 'baz']), {})
{ formatted: 'foobarbaz' }
> print(join('|', ['foo', 'bar', 'baz']), {})
{ formatted: 'foo|bar|baz' }
```

To test things like `group`, you’ll need to specify a `printWidth`:

```
> print(group(join(line, ['foo', 'bar', 'baz', 'qux'])), { printWidth: 20 })
{ formatted: 'foo bar baz qux' }
> print(group(join(line, ['foo', 'bar', 'baz', 'qux'])), { printWidth: 10 })
{ formatted: 'foo\nbar\nbaz\nqux' }
> print(group(join(softline, ['foo', 'bar', 'baz', 'qux'])), { printWidth: 20 })
{ formatted: 'foobarbazqux' }
> print(group(join(softline, ['foo', 'bar', 'baz', 'qux'])), { printWidth: 10 })
{ formatted: 'foo\nbar\nbaz\nqux' }
```

You can learn this way. I know it’s not a great user experience, and it would be nice to have something better (maybe a web playground where you can run expressions like this and see the result with different inputs?), but I’m not aware of anything better.

## Pending things

If we run our example again, we’ll see that we have an equivalent TOML printed as we specified it:

```
> prettier-plugin-toml@0.0.1 example /home/fvictorio/repos/prettier-plugin-toml
> prettier --plugin . example.toml

title = "TOML Example"
[owner]
name = "Tom Preston-Werner"
dob = 1979-05-27T15:32:00.000Z
[database]
server = "192.168.1.1"
ports = [8001, 8001, 8002]
connection_max = 5000
enabled = true
[servers]
[servers.alpha]
ip = "10.0.0.1"
dc = "eqdc10"
[servers.beta]
ip = "10.0.0.2"
dc = "eqdc10"
[clients]
data = [["gamma", "delta"], [1, 2]]
hosts = ["alpha", "omega"]
```

But it would be hard to argue that this is _prettier_. There are too very important things we are not doing, and that we can’t do easily with the parser we are using:

- We are not preserving **blank lines**. Prettier’s philosophy is to keep them (although if there are two or more blank lines together, they are replaced by a single blank line). This can be done, but to do that we need an easy way to get the start and end indices of the node. As you see in the nodes examples, we only have the starting line and column.
- We are not **indenting the tables**. This would be relatively easy if the representation of the AST would be a proper tree, but remember that we have instead a list of nodes for each line. If under the table objects we’d have a, say, “children” key, we could do something like `path.map(print, 'children')`, join that by hardlines and indent them, for example.

## What’s next?

Hopefully you learned enough to start your own plugin or contribute to one. Take a look at the [list of plugins](https://prettier.io/docs/en/plugins.html#official-plugins): if the language you would like to see prettified isn’t there, you can create your own! And if it is, you can jump on and contribute.

A nice thing about prettier plugins is that it’s very easy to do TDD with them. If you want to contribute to a plugin, just add a fixture with an example that it’s not working and try to make all tests pass. If you are creating a new plugin you can start small: add tests with some simple examples using a subset of the syntax and make them prettier!
