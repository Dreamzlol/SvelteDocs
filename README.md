# Aggregated Content

Getting Started

# Introduction

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/01-getting-
started/01-introduction.md)

## On this page On this page

Welcome to the Svelte reference documentation! This is intended as a resource
for people who already have some familiarity with Svelte and want to learn
more about using it.

If that's not you (yet), you may prefer to visit the [interactive
tutorial](https://learn.svelte.dev) or the [examples](/examples) before
consulting this reference. You can try Svelte online using the [REPL](/repl).
Alternatively, if you'd like a more fully-featured environment, you can try
Svelte on [StackBlitz](https://sveltekit.new).

## Start a new projectpermalink

We recommend using [SvelteKit](https://kit.svelte.dev/), the official
application framework from the Svelte team:

    
    
    npm create svelte@latest myapp
    cd myapp
    npm install
    npm run dev

SvelteKit will handle calling [the Svelte
compiler](https://www.npmjs.com/package/svelte) to convert your `.svelte`
files into `.js` files that create the DOM and `.css` files that style it. It
also provides all the other pieces you need to build a web application such as
a development server, routing, deployment, and SSR support.
[SvelteKit](https://kit.svelte.dev/) uses [Vite](https://vitejs.dev/) to build
your code.

### Alternatives to SvelteKitpermalink

If you don't want to use SvelteKit for some reason, you can also use Svelte
with Vite (but without SvelteKit) by running `npm create vite@latest` and
selecting the `svelte` option. With this, `npm run build` will generate HTML,
JS and CSS files inside the `dist` directory. In most cases, you will probably
need to [choose a routing library](/faq#is-there-a-router) as well.

Alternatively, there are [plugins for all the major web
bundlers](https://sveltesociety.dev/packages?category=build-plugins) to handle
Svelte compilation — which will output `.js` and `.css` that you can insert
into your HTML — but most others won't handle SSR.

## Editor toolingpermalink

The Svelte team maintains a [VS Code
extension](https://marketplace.visualstudio.com/items?itemName=svelte.svelte-
vscode) and there are integrations with various other
[editors](https://sveltesociety.dev/resources#editor-support) and tools as
well.

## Getting helppermalink

Don't be shy about asking for help in the [Discord
chatroom](https://svelte.dev/chat)! You can also find answers on [Stack
Overflow](https://stackoverflow.com/questions/tagged/svelte).

previous

next [Svelte components](/docs/svelte-components)



Template Syntax

# Svelte components

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/02-template-
syntax/01-svelte-components.md)

## On this page On this page

Components are the building blocks of Svelte applications. They are written
into `.svelte` files, using a superset of HTML.

All three sections — script, styles and markup — are optional.

    
    
    <script>
    	// logic goes here
    </script>
    
    <!-- markup (zero or more items) goes here -->
    
    <style>
    	/* styles go here */
    </style>

## <script>permalink

A `<script>` block contains JavaScript that runs when a component instance is
created. Variables declared (or imported) at the top level are 'visible' from
the component's markup. There are four additional rules:

### 1\. export creates a component proppermalink

Svelte uses the `export` keyword to mark a variable declaration as a
_property_ or _prop_ , which means it becomes accessible to consumers of the
component (see the section on [attributes and props](/docs/basic-
markup#attributes-and-props) for more information).

    
    
    <script>
    	export let foo;
    
    	// Values that are passed in as props
    	// are immediately available
    	console.log({ foo });
    </script>

You can specify a default initial value for a prop. It will be used if the
component's consumer doesn't specify the prop on the component (or if its
initial value is `undefined`) when instantiating the component. Note that if
the values of props are subsequently updated, then any prop whose value is not
specified will be set to `undefined` (rather than its initial value).

In development mode (see the [compiler options](/docs/svelte-
compiler#compile)), a warning will be printed if no default initial value is
provided and the consumer does not specify a value. To squelch this warning,
ensure that a default initial value is specified, even if it is `undefined`.

    
    
    <script>
    	export let bar = 'optional default initial value';
    	export let baz = undefined;
    </script>

If you export a `const`, `class` or `function`, it is readonly from outside
the component. Functions are valid prop values, however, as shown below.

App.svelte

    
    
    <script>
    	// these are readonly
    	export const thisIs = 'readonly';
    
    	/** @param {string} name */
    	export function greet(name) {
    		alert(`hello ${name}!`);
    	}
    
    	// this is a prop
    	export let format = (n) => n.toFixed(2);
    </script>

App.svelte

    
    
    <script lang="ts">
    	// these are readonly
    	export const thisIs = 'readonly';
    	
    	export function greet(name: string) {
    		alert(`hello ${name}!`);
    	}
    	
    	// this is a prop
    	export let format = (n) => n.toFixed(2);
    </script>

Readonly props can be accessed as properties on the element, tied to the
component using [`bind:this` syntax](/docs/component-directives#bind-this).

You can use reserved words as prop names.

App.svelte

    
    
    <script>
    	/** @type {string} */
    	let className;
    
    	// creates a `class` property, even
    	// though it is a reserved word
    	export { className as class };
    </script>

App.svelte

    
    
    <script lang="ts">
    	let className: string;
    	
    	// creates a `class` property, even
    	// though it is a reserved word
    	export { className as class };
    </script>

### 2\. Assignments are 'reactive'permalink

To change component state and trigger a re-render, just assign to a locally
declared variable.

Update expressions (`count += 1`) and property assignments (`obj.x = y`) have
the same effect.

    
    
    <script>
    	let count = 0;
    
    	function handleClick() {
    		// calling this function will trigger an
    		// update if the markup references `count`
    		count = count + 1;
    	}
    </script>

Because Svelte's reactivity is based on assignments, using array methods like
`.push()` and `.splice()` won't automatically trigger updates. A subsequent
assignment is required to trigger the update. This and more details can also
be found in the [tutorial](https://learn.svelte.dev/tutorial/updating-arrays-
and-objects).

    
    
    <script>
    	let arr = [0, 1];
    
    	function handleClick() {
    		// this method call does not trigger an update
    		arr.push(2);
    		// this assignment will trigger an update
    		// if the markup references `arr`
    		arr = arr;
    	}
    </script>

Svelte's `<script>` blocks are run only when the component is created, so
assignments within a `<script>` block are not automatically run again when a
prop updates. If you'd like to track changes to a prop, see the next example
in the following section.

    
    
    <script>
    	export let person;
    	// this will only set `name` on component creation
    	// it will not update when `person` does
    	let { name } = person;
    </script>

### 3\. $: marks a statement as reactivepermalink

Any top-level statement (i.e. not inside a block or a function) can be made
reactive by prefixing it with the `$:` [JS label
syntax](https://developer.mozilla.org/en-
US/docs/Web/JavaScript/Reference/Statements/label). Reactive statements run
after other script code and before the component markup is rendered, whenever
the values that they depend on have changed.

    
    
    <script>
    	export let title;
    	export let person;
    
    	// this will update `document.title` whenever
    	// the `title` prop changes
    	$: document.title = title;
    
    	$: {
    		console.log(`multiple statements can be combined`);
    		console.log(`the current title is ${title}`);
    	}
    
    	// this will update `name` when 'person' changes
    	$: ({ name } = person);
    
    	// don't do this. it will run before the previous line
    	let name2 = name;
    </script>

Only values which directly appear within the `$:` block will become
dependencies of the reactive statement. For example, in the code below `total`
will only update when `x` changes, but not `y`.

App.svelte

    
    
    <script>
    	let x = 0;
    	let y = 0;
    
    	/** @param {number} value */
    	function yPlusAValue(value) {
    		return value + y;
    	}
    
    	$: total = yPlusAValue(x);
    </script>
    
    Total: {total}
    <button on:click={() => x++}> Increment X </button>
    
    <button on:click={() => y++}> Increment Y </button>

App.svelte

    
    
    <script lang="ts">
    	let x = 0;
    	let y = 0;
    	
    	function yPlusAValue(value: number) {
    		return value + y;
    	}
    	
    	$: total = yPlusAValue(x);
    </script>
    
    Total: {total}
    <button on:click={() => x++}> Increment X </button>
    
    <button on:click={() => y++}> Increment Y </button>

It is important to note that the reactive blocks are ordered via simple static
analysis at compile time, and all the compiler looks at are the variables that
are assigned to and used within the block itself, not in any functions called
by them. This means that `yDependent` will not be updated when `x` is updated
in the following example:

App.svelte

    
    
    <script>
    	let x = 0;
    	let y = 0;
    
    	/** @param {number} value */
    	function setY(value) {
    		y = value;
    	}
    
    	$: yDependent = y;
    	$: setY(x);
    </script>

App.svelte

    
    
    <script lang="ts">
    	let x = 0;
    	let y = 0;
    	
    	function setY(value: number) {
    		y = value;
    	}
    	
    	$: yDependent = y;
    	$: setY(x);
    </script>

Moving the line `$: yDependent = y` below `$: setY(x)` will cause `yDependent`
to be updated when `x` is updated.

If a statement consists entirely of an assignment to an undeclared variable,
Svelte will inject a `let` declaration on your behalf.

App.svelte

    
    
    <script>
    	/** @type {number} */
    	export let num;
    
    	// we don't need to declare `squared` and `cubed`
    	// — Svelte does it for us
    	$: squared = num * num;
    	$: cubed = squared * num;
    </script>

App.svelte

    
    
    <script lang="ts">
    	export let num: number;
    	
    	// we don't need to declare `squared` and `cubed`
    	// — Svelte does it for us
    	$: squared = num * num;
    	$: cubed = squared * num;
    </script>

### 4\. Prefix stores with $ to access their valuespermalink

A _store_ is an object that allows reactive access to a value via a simple
_store contract_. The [`svelte/store` module](/docs/svelte-store) contains
minimal store implementations which fulfil this contract.

Any time you have a reference to a store, you can access its value inside a
component by prefixing it with the `$` character. This causes Svelte to
declare the prefixed variable, subscribe to the store at component
initialization and unsubscribe when appropriate.

Assignments to `$`-prefixed variables require that the variable be a writable
store, and will result in a call to the store's `.set` method.

Note that the store must be declared at the top level of the component — not
inside an `if` block or a function, for example.

Local variables (that do not represent store values) must _not_ have a `$`
prefix.

    
    
    <script>
    	import { writable } from 'svelte/store';
    
    	const count = writable(0);
    	console.log($count); // logs 0
    
    	count.set(1);
    	console.log($count); // logs 1
    
    	$count = 2;
    	console.log($count); // logs 2
    </script>

#### Store contractpermalink

    
    
    ts
    
    store = { subscribe: (subscription: (value: any) => void) => (() => void), set?: (value: any) => void }

You can create your own stores without relying on
[`svelte/store`](/docs/svelte-store), by implementing the _store contract_ :

  1. A store must contain a `.subscribe` method, which must accept as its argument a subscription function. This subscription function must be immediately and synchronously called with the store's current value upon calling `.subscribe`. All of a store's active subscription functions must later be synchronously called whenever the store's value changes.
  2. The `.subscribe` method must return an unsubscribe function. Calling an unsubscribe function must stop its subscription, and its corresponding subscription function must not be called again by the store.
  3. A store may _optionally_ contain a `.set` method, which must accept as its argument a new value for the store, and which synchronously calls all of the store's active subscription functions. Such a store is called a _writable store_.

For interoperability with RxJS Observables, the `.subscribe` method is also
allowed to return an object with an `.unsubscribe` method, rather than return
the unsubscription function directly. Note however that unless `.subscribe`
synchronously calls the subscription (which is not required by the Observable
spec), Svelte will see the value of the store as `undefined` until it does.

## <script context="module">permalink

A `<script>` tag with a `context="module"` attribute runs once when the module
first evaluates, rather than for each component instance. Values declared in
this block are accessible from a regular `<script>` (and the component markup)
but not vice versa.

You can `export` bindings from this block, and they will become exports of the
compiled module.

You cannot `export default`, since the default export is the component itself.

> Variables defined in `module` scripts are not reactive — reassigning them
> will not trigger a rerender even though the variable itself will update. For
> values shared between multiple components, consider using a
> [store](/docs/svelte-store).
    
    
    <script context="module">
    	let totalComponents = 0;
    
    	// the export keyword allows this function to imported with e.g.
    	// `import Example, { alertTotal } from './Example.svelte'`
    	export function alertTotal() {
    		alert(totalComponents);
    	}
    </script>
    
    <script>
    	totalComponents += 1;
    	console.log(`total number of times this component has been created: ${totalComponents}`);
    </script>

## <style>permalink

CSS inside a `<style>` block will be scoped to that component.

This works by adding a class to affected elements, which is based on a hash of
the component styles (e.g. `svelte-123xyz`).

    
    
    <style>
    	p {
    		/* this will only affect <p> elements in this component */
    		color: burlywood;
    	}
    </style>

To apply styles to a selector globally, use the `:global(...)` modifier.

    
    
    <style>
    	:global(body) {
    		/* this will apply to <body> */
    		margin: 0;
    	}
    
    	div :global(strong) {
    		/* this will apply to all <strong> elements, in any
    			 component, that are inside <div> elements belonging
    			 to this component */
    		color: goldenrod;
    	}
    
    	p:global(.red) {
    		/* this will apply to all <p> elements belonging to this
    			 component with a class of red, even if class="red" does
    			 not initially appear in the markup, and is instead
    			 added at runtime. This is useful when the class
    			 of the element is dynamically applied, for instance
    			 when updating the element's classList property directly. */
    	}
    </style>

If you want to make @keyframes that are accessible globally, you need to
prepend your keyframe names with `-global-`.

The `-global-` part will be removed when compiled, and the keyframe then be
referenced using just `my-animation-name` elsewhere in your code.

    
    
    <style>
    	@keyframes -global-my-animation-name {
    		/* code goes here */
    	}
    </style>

There should only be 1 top-level `<style>` tag per component.

However, it is possible to have `<style>` tag nested inside other elements or
logic blocks.

In that case, the `<style>` tag will be inserted as-is into the DOM, no
scoping or processing will be done on the `<style>` tag.

    
    
    <div>
    	<style>
    		/* this style tag will be inserted as-is */
    		div {
    			/* this will apply to all `<div>` elements in the DOM */
    			color: red;
    		}
    	</style>
    </div>

previous [Introduction](/docs/introduction)

next [Basic markup](/docs/basic-markup)



Template Syntax

# Basic markup

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/02-template-
syntax/02-basic-markup.md)

## On this page On this page

## Tagspermalink

A lowercase tag, like `<div>`, denotes a regular HTML element. A capitalised
tag, such as `<Widget>` or `<Namespace.Widget>`, indicates a _component_.

    
    
    <script>
    	import Widget from './Widget.svelte';
    </script>
    
    <div>
    	<Widget />
    </div>

## Attributes and propspermalink

By default, attributes work exactly like their HTML counterparts.

    
    
    <div class="foo">
    	<button disabled>can't touch this</button>
    </div>

As in HTML, values may be unquoted.

    
    
    <input type=checkbox />

Attribute values can contain JavaScript expressions.

    
    
    <a href="page/{p}">page {p}</a>

Or they can _be_ JavaScript expressions.

    
    
    <button disabled={!clickable}>...</button>

Boolean attributes are included on the element if their value is
[truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) and
excluded if it's [falsy](https://developer.mozilla.org/en-
US/docs/Glossary/Falsy).

All other attributes are included unless their value is
[nullish](https://developer.mozilla.org/en-US/docs/Glossary/Nullish) (`null`
or `undefined`).

    
    
    <input required={false} placeholder="This input field is not required" />
    <div title={null}>This div has no title attribute</div>

An expression might include characters that would cause syntax highlighting to
fail in regular HTML, so quoting the value is permitted. The quotes do not
affect how the value is parsed:

    
    
    <button disabled="{number !== 42}">...</button>

When the attribute name and value match (`name={name}`), they can be replaced
with `{name}`.

    
    
    <button {disabled}>...</button>
    <!-- equivalent to
    <button disabled={disabled}>...</button>
    -->

By convention, values passed to components are referred to as _properties_ or
_props_ rather than _attributes_ , which are a feature of the DOM.

As with elements, `name={name}` can be replaced with the `{name}` shorthand.

    
    
    <Widget foo={bar} answer={42} text="hello" />

_Spread attributes_ allow many attributes or properties to be passed to an
element or component at once.

An element or component can have multiple spread attributes, interspersed with
regular ones.

    
    
    <Widget {...things} />

`$$props` references all props that are passed to a component, including ones
that are not declared with `export`. Using `$$props` will not perform as well
as references to a specific prop because changes to any prop will cause Svelte
to recheck all usages of `$$props`. But it can be useful in some cases – for
example, when you don't know at compile time what props might be passed to a
component.

    
    
    <Widget {...$$props} />

`$$restProps` contains only the props which are _not_ declared with `export`.
It can be used to pass down other unknown attributes to an element in a
component. It shares the same performance characteristics compared to specific
property access as `$$props`.

    
    
    <input {...$$restProps} />

> The `value` attribute of an `input` element or its children `option`
> elements must not be set with spread attributes when using `bind:group` or
> `bind:checked`. Svelte needs to be able to see the element's `value`
> directly in the markup in these cases so that it can link it to the bound
> variable.

> Sometimes, the attribute order matters as Svelte sets attributes
> sequentially in JavaScript. For example, `<input type="range" min="0"
> max="1" value={0.5} step="0.1"/>`, Svelte will attempt to set the value to
> `1` (rounding up from 0.5 as the step by default is 1), and then set the
> step to `0.1`. To fix this, change it to `<input type="range" min="0"
> max="1" step="0.1" value={0.5}/>`.

> Another example is `<img src="..." loading="lazy" />`. Svelte will set the
> img `src` before making the img element `loading="lazy"`, which is probably
> too late. Change this to `<img loading="lazy" src="...">` to make the image
> lazily loaded.

## Text expressionspermalink

A JavaScript expression can be included as text by surrounding it with curly
braces.

    
    
    {expression}

Curly braces can be included in a Svelte template by using their [HTML
entity](https://developer.mozilla.org/docs/Glossary/Entity) strings:
`&lbrace;`, `&lcub;`, or `&#123;` for `{` and `&rbrace;`, `&rcub;`, or
`&#125;` for `}`.

> If you're using a regular expression (`RegExp`) [literal
> notation](https://developer.mozilla.org/en-
> US/docs/Web/JavaScript/Reference/Global_Objects/RegExp#literal_notation_and_constructor),
> you'll need to wrap it in parentheses.
    
    
    <h1>Hello {name}!</h1>
    <p>{a} + {b} = {a + b}.</p>
    
    <div>{(/^[A-Za-z ]+$/).test(value) ? x : y}</div>

## Commentspermalink

You can use HTML comments inside components.

    
    
    <!-- this is a comment! --><h1>Hello world</h1>

Comments beginning with `svelte-ignore` disable warnings for the next block of
markup. Usually, these are accessibility warnings; make sure that you're
disabling them for a good reason.

    
    
    <!-- svelte-ignore a11y-autofocus -->
    <input bind:value={name} autofocus />

previous [Svelte components](/docs/svelte-components)

next [Logic blocks](/docs/logic-blocks)



Template Syntax

# Logic blocks

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/02-template-
syntax/03-logic-blocks.md)

## On this page On this page

## {#if ...}permalink

    
    
    {#if expression}...{/if}
    
    
    {#if expression}...{:else if expression}...{/if}
    
    
    {#if expression}...{:else}...{/if}

Content that is conditionally rendered can be wrapped in an if block.

    
    
    {#if answer === 42}
    	<p>what was the question?</p>
    {/if}

Additional conditions can be added with `{:else if expression}`, optionally
ending in an `{:else}` clause.

    
    
    {#if porridge.temperature > 100}
    	<p>too hot!</p>
    {:else if 80 > porridge.temperature}
    	<p>too cold!</p>
    {:else}
    	<p>just right!</p>
    {/if}

(Blocks don't have to wrap elements, they can also wrap text within elements!)

## {#each ...}permalink

    
    
    {#each expression as name}...{/each}
    
    
    {#each expression as name, index}...{/each}
    
    
    {#each expression as name (key)}...{/each}
    
    
    {#each expression as name, index (key)}...{/each}
    
    
    {#each expression as name}...{:else}...{/each}

Iterating over lists of values can be done with an each block.

    
    
    <h1>Shopping list</h1>
    <ul>
    	{#each items as item}
    		<li>{item.name} x {item.qty}</li>
    	{/each}
    </ul>

You can use each blocks to iterate over any array or array-like value — that
is, any object with a `length` property.

An each block can also specify an _index_ , equivalent to the second argument
in an `array.map(...)` callback:

    
    
    {#each items as item, i}
    	<li>{i + 1}: {item.name} x {item.qty}</li>
    {/each}

If a _key_ expression is provided — which must uniquely identify each list
item — Svelte will use it to diff the list when data changes, rather than
adding or removing items at the end. The key can be any object, but strings
and numbers are recommended since they allow identity to persist when the
objects themselves change.

    
    
    {#each items as item (item.id)}
    	<li>{item.name} x {item.qty}</li>
    {/each}
    
    <!-- or with additional index value -->
    {#each items as item, i (item.id)}
    	<li>{i + 1}: {item.name} x {item.qty}</li>
    {/each}

You can freely use destructuring and rest patterns in each blocks.

    
    
    {#each items as { id, name, qty }, i (id)}
    	<li>{i + 1}: {name} x {qty}</li>
    {/each}
    
    {#each objects as { id, ...rest }}
    	<li><span>{id}</span><MyComponent {...rest} /></li>
    {/each}
    
    {#each items as [id, ...rest]}
    	<li><span>{id}</span><MyComponent values={rest} /></li>
    {/each}

An each block can also have an `{:else}` clause, which is rendered if the list
is empty.

    
    
    {#each todos as todo}
    	<p>{todo.text}</p>
    {:else}
    	<p>No tasks today!</p>
    {/each}

Since Svelte 4 it is possible to iterate over iterables like `Map` or `Set`.
Iterables need to be finite and static (they shouldn't change while being
iterated over). Under the hood, they are transformed to an array using
`Array.from` before being passed off to rendering. If you're writing
performance-sensitive code, try to avoid iterables and use regular arrays as
they are more performant.

## {#await ...}permalink

    
    
    {#await expression}...{:then name}...{:catch name}...{/await}
    
    
    {#await expression}...{:then name}...{/await}
    
    
    {#await expression then name}...{/await}
    
    
    {#await expression catch name}...{/await}

Await blocks allow you to branch on the three possible states of a Promise —
pending, fulfilled or rejected. In SSR mode, only the pending branch will be
rendered on the server. If the provided expression is not a Promise only the
fulfilled branch will be rendered, including in SSR mode.

    
    
    {#await promise}
    	<!-- promise is pending -->
    	<p>waiting for the promise to resolve...</p>
    {:then value}
    	<!-- promise was fulfilled or not a Promise -->
    	<p>The value is {value}</p>
    {:catch error}
    	<!-- promise was rejected -->
    	<p>Something went wrong: {error.message}</p>
    {/await}

The `catch` block can be omitted if you don't need to render anything when the
promise rejects (or no error is possible).

    
    
    {#await promise}
    	<!-- promise is pending -->
    	<p>waiting for the promise to resolve...</p>
    {:then value}
    	<!-- promise was fulfilled -->
    	<p>The value is {value}</p>
    {/await}

If you don't care about the pending state, you can also omit the initial
block.

    
    
    {#await promise then value}
    	<p>The value is {value}</p>
    {/await}

Similarly, if you only want to show the error state, you can omit the `then`
block.

    
    
    {#await promise catch error}
    	<p>The error is {error}</p>
    {/await}

## {#key ...}permalink

    
    
    {#key expression}...{/key}

Key blocks destroy and recreate their contents when the value of an expression
changes.

This is useful if you want an element to play its transition whenever a value
changes.

    
    
    {#key value}
    	<div transition:fade>{value}</div>
    {/key}

When used around components, this will cause them to be reinstantiated and
reinitialised.

    
    
    {#key value}
    	<Component />
    {/key}

previous [Basic markup](/docs/basic-markup)

next [Special tags](/docs/special-tags)



Template Syntax

# Special tags

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/02-template-
syntax/04-special-tags.md)

## On this page On this page

## {@html ...}permalink

    
    
    {@html expression}

In a text expression, characters like `<` and `>` are escaped; however, with
HTML expressions, they're not.

The expression should be valid standalone HTML — `{@html "<div>"}content{@html
"</div>"}` will _not_ work, because `</div>` is not valid HTML. It also will
_not_ compile Svelte code.

> Svelte does not sanitize expressions before injecting HTML. If the data
> comes from an untrusted source, you must sanitize it, or you are exposing
> your users to an XSS vulnerability.
    
    
    <div class="blog-post">
    	<h1>{post.title}</h1>
    	{@html post.content}
    </div>

## {@debug ...}permalink

    
    
    {@debug}
    
    
    {@debug var1, var2, ..., varN}

The `{@debug ...}` tag offers an alternative to `console.log(...)`. It logs
the values of specific variables whenever they change, and pauses code
execution if you have devtools open.

    
    
    <script>
    	let user = {
    		firstname: 'Ada',
    		lastname: 'Lovelace'
    	};
    </script>
    
    {@debug user}
    
    <h1>Hello {user.firstname}!</h1>

`{@debug ...}` accepts a comma-separated list of variable names (not arbitrary
expressions).

    
    
    <!-- Compiles -->
    {@debug user}
    {@debug user1, user2, user3}
    
    <!-- WON'T compile -->
    {@debug user.firstname}
    {@debug myArray[0]}
    {@debug !isReady}
    {@debug typeof user === 'object'}

The `{@debug}` tag without any arguments will insert a `debugger` statement
that gets triggered when _any_ state changes, as opposed to the specified
variables.

## {@const ...}permalink

    
    
    {@const assignment}

The `{@const ...}` tag defines a local constant.

    
    
    <script>
    	export let boxes;
    </script>
    
    {#each boxes as box}
    	{@const area = box.width * box.height}
    	{box.width} * {box.height} = {area}
    {/each}

`{@const}` is only allowed as direct child of `{#if}`, `{:else if}`,
`{:else}`, `{#each}`, `{:then}`, `{:catch}`, `<Component />` or
`<svelte:fragment />`.

previous [Logic blocks](/docs/logic-blocks)

next [Element directives](/docs/element-directives)



Template Syntax

# Element directives

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/02-template-
syntax/05-element-directives.md)

## On this page On this page

As well as attributes, elements can have _directives_ , which control the
element's behaviour in some way.

## on:_eventname_ permalink

    
    
    on:eventname={handler}
    
    
    on:eventname|modifiers={handler}

Use the `on:` directive to listen to DOM events.

App.svelte

    
    
    <script>
    	let count = 0;
    
    	/** @param {MouseEvent} event */
    	function handleClick(event) {
    		count += 1;
    	}
    </script>
    
    <button on:click={handleClick}>
    	count: {count}
    </button>

App.svelte

    
    
    <script lang="ts">
    	let count = 0;
    	
    	function handleClick(event: MouseEvent) {
    		count += 1;
    	}
    </script>
    
    <button on:click={handleClick}>
    	count: {count}
    </button>

Handlers can be declared inline with no performance penalty. As with
attributes, directive values may be quoted for the sake of syntax
highlighters.

    
    
    <button on:click={() => (count += 1)}>
    	count: {count}
    </button>

Add _modifiers_ to DOM events with the `|` character.

    
    
    <form on:submit|preventDefault={handleSubmit}>
    	<!-- the `submit` event's default is prevented,
    		 so the page won't reload -->
    </form>

The following modifiers are available:

  * `preventDefault` — calls `event.preventDefault()` before running the handler
  * `stopPropagation` — calls `event.stopPropagation()`, preventing the event reaching the next element
  * `stopImmediatePropagation` \- calls `event.stopImmediatePropagation()`, preventing other listeners of the same event from being fired.
  * `passive` — improves scrolling performance on touch/wheel events (Svelte will add it automatically where it's safe to do so)
  * `nonpassive` — explicitly set `passive: false`
  * `capture` — fires the handler during the _capture_ phase instead of the _bubbling_ phase
  * `once` — remove the handler after the first time it runs
  * `self` — only trigger handler if `event.target` is the element itself
  * `trusted` — only trigger handler if `event.isTrusted` is `true`. I.e. if the event is triggered by a user action.

Modifiers can be chained together, e.g. `on:click|once|capture={...}`.

If the `on:` directive is used without a value, the component will _forward_
the event, meaning that a consumer of the component can listen for it.

    
    
    <button on:click> The component itself will emit the click event </button>

It's possible to have multiple event listeners for the same event:

App.svelte

    
    
    <script>
    	let counter = 0;
    	function increment() {
    		counter = counter + 1;
    	}
    
    	/** @param {MouseEvent} event */
    	function track(event) {
    		trackEvent(event);
    	}
    </script>
    
    <button on:click={increment} on:click={track}>Click me!</button>

App.svelte

    
    
    <script lang="ts">
    	let counter = 0;
    	function increment() {
    		counter = counter + 1;
    	}
    	
    	function track(event: MouseEvent) {
    		trackEvent(event);
    	}
    </script>
    
    <button on:click={increment} on:click={track}>Click me!</button>

## bind:_property_ permalink

    
    
    bind:property={variable}

Data ordinarily flows down, from parent to child. The `bind:` directive allows
data to flow the other way, from child to parent. Most bindings are specific
to particular elements.

The simplest bindings reflect the value of a property, such as `input.value`.

    
    
    <input bind:value={name} />
    <textarea bind:value={text} />
    
    <input type="checkbox" bind:checked={yes} />

If the name matches the value, you can use a shorthand.

    
    
    <input bind:value />
    <!-- equivalent to
    <input bind:value={value} />
    -->

Numeric input values are coerced; even though `input.value` is a string as far
as the DOM is concerned, Svelte will treat it as a number. If the input is
empty or invalid (in the case of `type="number"`), the value is `null`.

    
    
    <input type="number" bind:value={num} />
    <input type="range" bind:value={num} />

On `<input>` elements with `type="file"`, you can use `bind:files` to get the
[`FileList` of selected files](https://developer.mozilla.org/en-
US/docs/Web/API/FileList). It is readonly.

    
    
    <label for="avatar">Upload a picture:</label>
    <input accept="image/png, image/jpeg" bind:files id="avatar" name="avatar" type="file" />

If you're using `bind:` directives together with `on:` directives, the order
that they're defined in affects the value of the bound variable when the event
handler is called.

    
    
    <script>
    	let value = 'Hello World';
    </script>
    
    <input
    	on:input={() => console.log('Old value:', value)}
    	bind:value
    	on:input={() => console.log('New value:', value)}
    />

Here we were binding to the value of a text input, which uses the `input`
event. Bindings on other elements may use different events such as `change`.

## Binding <select> valuepermalink

A `<select>` value binding corresponds to the `value` property on the selected
`<option>`, which can be any value (not just strings, as is normally the case
in the DOM).

    
    
    <select bind:value={selected}>
    	<option value={a}>a</option>
    	<option value={b}>b</option>
    	<option value={c}>c</option>
    </select>

A `<select multiple>` element behaves similarly to a checkbox group. The bound
variable is an array with an entry corresponding to the `value` property of
each selected `<option>`.

    
    
    <select multiple bind:value={fillings}>
    	<option value="Rice">Rice</option>
    	<option value="Beans">Beans</option>
    	<option value="Cheese">Cheese</option>
    	<option value="Guac (extra)">Guac (extra)</option>
    </select>

When the value of an `<option>` matches its text content, the attribute can be
omitted.

    
    
    <select multiple bind:value={fillings}>
    	<option>Rice</option>
    	<option>Beans</option>
    	<option>Cheese</option>
    	<option>Guac (extra)</option>
    </select>

Elements with the `contenteditable` attribute support the following bindings:

  * [`innerHTML`](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML)
  * [`innerText`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/innerText)
  * [`textContent`](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent)

There are slight differences between each of these, read more about them
[here](https://developer.mozilla.org/en-
US/docs/Web/API/Node/textContent#Differences_from_innerText).

    
    
    <div contenteditable="true" bind:innerHTML={html} />

`<details>` elements support binding to the `open` property.

    
    
    <details bind:open={isOpen}>
    	<summary>Details</summary>
    	<p>Something small enough to escape casual notice.</p>
    </details>

## Media element bindingspermalink

Media elements (`<audio>` and `<video>`) have their own set of bindings —
seven _readonly_ ones...

  * `duration` (readonly) — the total duration of the video, in seconds
  * `buffered` (readonly) — an array of `{start, end}` objects
  * `played` (readonly) — ditto
  * `seekable` (readonly) — ditto
  * `seeking` (readonly) — boolean
  * `ended` (readonly) — boolean
  * `readyState` (readonly) — number between (and including) 0 and 4

...and five _two-way_ bindings:

  * `currentTime` — the current playback time in the video, in seconds
  * `playbackRate` — how fast or slow to play the video, where 1 is 'normal'
  * `paused` — this one should be self-explanatory
  * `volume` — a value between 0 and 1
  * `muted` — a boolean value indicating whether the player is muted

Videos additionally have readonly `videoWidth` and `videoHeight` bindings.

    
    
    <video
    	src={clip}
    	bind:duration
    	bind:buffered
    	bind:played
    	bind:seekable
    	bind:seeking
    	bind:ended
    	bind:readyState
    	bind:currentTime
    	bind:playbackRate
    	bind:paused
    	bind:volume
    	bind:muted
    	bind:videoWidth
    	bind:videoHeight
    />

## Image element bindingspermalink

Image elements (`<img>`) have two readonly bindings:

  * `naturalWidth` (readonly) — the original width of the image, available after the image has loaded
  * `naturalHeight` (readonly) — the original height of the image, available after the image has loaded

    
    
    <img
    	bind:naturalWidth
    	bind:naturalHeight
    ></img>

## Block-level element bindingspermalink

Block-level elements have 4 read-only bindings, measured using a technique
similar to [this one](http://www.backalleycoder.com/2013/03/18/cross-browser-
event-based-element-resize-detection/):

  * `clientWidth`
  * `clientHeight`
  * `offsetWidth`
  * `offsetHeight`

    
    
    <div bind:offsetWidth={width} bind:offsetHeight={height}>
    	<Chart {width} {height} />
    </div>

## bind:grouppermalink

    
    
    bind:group={variable}

Inputs that work together can use `bind:group`.

App.svelte

    
    
    <script>
    	let tortilla = 'Plain';
    
    	/** @type {Array<string>} */
    	let fillings = [];
    </script>
    
    <!-- grouped radio inputs are mutually exclusive -->
    <input type="radio" bind:group={tortilla} value="Plain" />
    <input type="radio" bind:group={tortilla} value="Whole wheat" />
    <input type="radio" bind:group={tortilla} value="Spinach" />
    
    <!-- grouped checkbox inputs populate an array -->
    <input type="checkbox" bind:group={fillings} value="Rice" />
    <input type="checkbox" bind:group={fillings} value="Beans" />
    <input type="checkbox" bind:group={fillings} value="Cheese" />
    <input type="checkbox" bind:group={fillings} value="Guac (extra)" />

App.svelte

    
    
    <script lang="ts">
    	let tortilla = 'Plain';
    	
    	let fillings: Array<string> = [];
    </script>
    
    <!-- grouped radio inputs are mutually exclusive -->
    <input type="radio" bind:group={tortilla} value="Plain" />
    <input type="radio" bind:group={tortilla} value="Whole wheat" />
    <input type="radio" bind:group={tortilla} value="Spinach" />
    
    <!-- grouped checkbox inputs populate an array -->
    <input type="checkbox" bind:group={fillings} value="Rice" />
    <input type="checkbox" bind:group={fillings} value="Beans" />
    <input type="checkbox" bind:group={fillings} value="Cheese" />
    <input type="checkbox" bind:group={fillings} value="Guac (extra)" />

> `bind:group` only works if the inputs are in the same Svelte component.

## bind:thispermalink

    
    
    bind:this={dom_node}

To get a reference to a DOM node, use `bind:this`.

App.svelte

    
    
    <script>
    	import { onMount } from 'svelte';
    
    	/** @type {HTMLCanvasElement} */
    	let canvasElement;
    
    	onMount(() => {
    		const ctx = canvasElement.getContext('2d');
    		drawStuff(ctx);
    	});
    </script>
    
    <canvas bind:this={canvasElement} />

App.svelte

    
    
    <script lang="ts">
    	import { onMount } from 'svelte';
    	
    	let canvasElement: HTMLCanvasElement;
    	
    	onMount(() => {
    		const ctx = canvasElement.getContext('2d');
    		drawStuff(ctx);
    	});
    </script>
    
    <canvas bind:this={canvasElement} />

## class:_name_ permalink

    
    
    class:name={value}
    
    
    class:name

A `class:` directive provides a shorter way of toggling a class on an element.

    
    
    <!-- These are equivalent -->
    <div class={isActive ? 'active' : ''}>...</div>
    <div class:active={isActive}>...</div>
    
    <!-- Shorthand, for when name and value match -->
    <div class:active>...</div>
    
    <!-- Multiple class toggles can be included -->
    <div class:active class:inactive={!active} class:isAdmin>...</div>

## style:_property_ permalink

    
    
    style:property={value}
    
    
    style:property="value"
    
    
    style:property

The `style:` directive provides a shorthand for setting multiple styles on an
element.

    
    
    <!-- These are equivalent -->
    <div style:color="red">...</div>
    <div style="color: red;">...</div>
    
    <!-- Variables can be used -->
    <div style:color={myColor}>...</div>
    
    <!-- Shorthand, for when property and variable name match -->
    <div style:color>...</div>
    
    <!-- Multiple styles can be included -->
    <div style:color style:width="12rem" style:background-color={darkMode ? 'black' : 'white'}>...</div>
    
    <!-- Styles can be marked as important -->
    <div style:color|important="red">...</div>

When `style:` directives are combined with `style` attributes, the directives
will take precedence:

    
    
    <div style="color: blue;" style:color="red">This will be red</div>

## use:_action_ permalink

    
    
    use:action
    
    
    use:action={parameters}
    
    
    ts
    
    action = (node: HTMLElement, parameters: any) => {
    
    	update?: (parameters: any) => void,
    
    	destroy?: () => void
    
    }

Actions are functions that are called when an element is created. They can
return an object with a `destroy` method that is called after the element is
unmounted:

App.svelte

    
    
    <script>
    	/** @type {import('svelte/action').Action}  */
    	function foo(node) {
    		// the node has been mounted in the DOM
    
    		return {
    			destroy() {
    				// the node has been removed from the DOM
    			}
    		};
    	}
    </script>
    
    <div use:foo />

App.svelte

    
    
    <script lang="ts">
    	import type { Action } from 'svelte/action';
    	
    	const foo: Action = (node) => {
    		// the node has been mounted in the DOM
    	
    		return {
    			destroy() {
    				// the node has been removed from the DOM
    			},
    		};
    	};
    </script>
    
    <div use:foo />

An action can have a parameter. If the returned value has an `update` method,
it will be called whenever that parameter changes, immediately after Svelte
has applied updates to the markup.

> Don't worry about the fact that we're redeclaring the `foo` function for
> every component instance — Svelte will hoist any functions that don't depend
> on local state out of the component definition.

App.svelte

    
    
    <script>
    	export let bar;
    
    	/** @type {import('svelte/action').Action}  */
    	function foo(node, bar) {
    		// the node has been mounted in the DOM
    
    		return {
    			update(bar) {
    				// the value of `bar` has changed
    			},
    
    			destroy() {
    				// the node has been removed from the DOM
    			}
    		};
    	}
    </script>
    
    <div use:foo={bar} />

App.svelte

    
    
    <script lang="ts">
    	import type { Action } from 'svelte/action';
    	
    	export let bar;
    	
    	const foo: Action = (node, bar) => {
    		// the node has been mounted in the DOM
    	
    		return {
    			update(bar) {
    				// the value of `bar` has changed
    			},
    	
    			destroy() {
    				// the node has been removed from the DOM
    			},
    		};
    	};
    </script>
    
    <div use:foo={bar} />

Read more in the [`svelte/action`](/docs/svelte-action) page.

## transition:_fn_ permalink

    
    
    transition:fn
    
    
    transition:fn={params}
    
    
    transition:fn|global
    
    
    transition:fn|global={params}
    
    
    transition:fn|local
    
    
    transition:fn|local={params}
    
    
    ts
    
    transition = (node: HTMLElement, params: any, options: { direction: 'in' | 'out' | 'both' }) => {
    
    	delay?: number,
    
    	duration?: number,
    
    	easing?: (t: number) => number,
    
    	css?: (t: number, u: number) => string,
    
    	tick?: (t: number, u: number) => void
    
    }

A transition is triggered by an element entering or leaving the DOM as a
result of a state change.

When a block is transitioning out, all elements inside the block, including
those that do not have their own transitions, are kept in the DOM until every
transition in the block has been completed.

The `transition:` directive indicates a _bidirectional_ transition, which
means it can be smoothly reversed while the transition is in progress.

    
    
    {#if visible}
    	<div transition:fade>fades in and out</div>
    {/if}

Transitions are local by default (in Svelte 3, they were global by default).
Local transitions only play when the block they belong to is created or
destroyed, _not_ when parent blocks are created or destroyed.

    
    
    {#if x}
    	{#if y}
    		<!-- Svelte 3: <p transition:fade|local> -->
    		<p transition:fade>fades in and out only when y changes</p>
    
    		<!-- Svelte 3: <p transition:fade> -->
    		<p transition:fade|global>fades in and out when x or y change</p>
    	{/if}
    {/if}

> By default intro transitions will not play on first render. You can modify
> this behaviour by setting `intro: true` when you [create a
> component](/docs/client-side-component-api) and marking the transition as
> `global`.

## Transition parameterspermalink

Like actions, transitions can have parameters.

(The double `{{curlies}}` aren't a special syntax; this is an object literal
inside an expression tag.)

    
    
    {#if visible}
    	<div transition:fade={{ duration: 2000 }}>fades in and out over two seconds</div>
    {/if}

## Custom transition functionspermalink

Transitions can use custom functions. If the returned object has a `css`
function, Svelte will create a CSS animation that plays on the element.

The `t` argument passed to `css` is a value between `0` and `1` after the
`easing` function has been applied. _In_ transitions run from `0` to `1`,
_out_ transitions run from `1` to `0` — in other words, `1` is the element's
natural state, as though no transition had been applied. The `u` argument is
equal to `1 - t`.

The function is called repeatedly _before_ the transition begins, with
different `t` and `u` arguments.

App.svelte

    
    
    <script>
    	import { elasticOut } from 'svelte/easing';
    
    	/** @type {boolean} */
    	export let visible;
    
    	/**
    	 * @param {HTMLElement} node
    	 * @param {{ delay?: number, duration?: number, easing?: (t: number) => number }} params
    	 */
    	function whoosh(node, params) {
    		const existingTransform = getComputedStyle(node).transform.replace('none', '');
    
    		return {
    			delay: params.delay || 0,
    			duration: params.duration || 400,
    			easing: params.easing || elasticOut,
    			css: (t, u) => `transform: ${existingTransform} scale(${t})`
    		};
    	}
    </script>
    
    {#if visible}
    	<div in:whoosh>whooshes in</div>
    {/if}

App.svelte

    
    
    <script lang="ts">
    	import { elasticOut } from 'svelte/easing';
    	
    	export let visible: boolean;
    	
    	function whoosh(
    		node: HTMLElement,
    		params: { delay?: number; duration?: number; easing?: (t: number) => number },
    	) {
    		const existingTransform = getComputedStyle(node).transform.replace('none', '');
    	
    		return {
    			delay: params.delay || 0,
    			duration: params.duration || 400,
    			easing: params.easing || elasticOut,
    			css: (t, u) => `transform: ${existingTransform} scale(${t})`,
    		};
    	}
    </script>
    
    {#if visible}
    	<div in:whoosh>whooshes in</div>
    {/if}

A custom transition function can also return a `tick` function, which is
called _during_ the transition with the same `t` and `u` arguments.

> If it's possible to use `css` instead of `tick`, do so — CSS animations can
> run off the main thread, preventing jank on slower devices.

App.svelte

    
    
    <script>
    	export let visible = false;
    
    	/**
    	 * @param {HTMLElement} node
    	 * @param {{ speed?: number }} params
    	 */
    	function typewriter(node, { speed = 1 }) {
    		const valid = node.childNodes.length === 1 && node.childNodes[0].nodeType === Node.TEXT_NODE;
    
    		if (!valid) {
    			throw new Error(`This transition only works on elements with a single text node child`);
    		}
    
    		const text = node.textContent;
    		const duration = text.length / (speed * 0.01);
    
    		return {
    			duration,
    			tick: (t) => {
    				const i = ~~(text.length * t);
    				node.textContent = text.slice(0, i);
    			}
    		};
    	}
    </script>
    
    {#if visible}
    	<p in:typewriter={{ speed: 1 }}>The quick brown fox jumps over the lazy dog</p>
    {/if}

App.svelte

    
    
    <script lang="ts">
    	export let visible = false;
    	
    	function typewriter(node: HTMLElement, { speed = 1 }: { speed?: number }) {
    		const valid = node.childNodes.length === 1 && node.childNodes[0].nodeType === Node.TEXT_NODE;
    	
    		if (!valid) {
    			throw new Error(`This transition only works on elements with a single text node child`);
    		}
    	
    		const text = node.textContent;
    		const duration = text.length / (speed * 0.01);
    	
    		return {
    			duration,
    			tick: (t) => {
    				const i = ~~(text.length * t);
    				node.textContent = text.slice(0, i);
    			},
    		};
    	}
    </script>
    
    {#if visible}
    	<p in:typewriter={{ speed: 1 }}>The quick brown fox jumps over the lazy dog</p>
    {/if}

If a transition returns a function instead of a transition object, the
function will be called in the next microtask. This allows multiple
transitions to coordinate, making [crossfade
effects](https://learn.svelte.dev/tutorial/deferred-transitions) possible.

Transition functions also receive a third argument, `options`, which contains
information about the transition.

Available values in the `options` object are:

  * `direction` \- one of `in`, `out`, or `both` depending on the type of transition

## Transition eventspermalink

An element with transitions will dispatch the following events in addition to
any standard DOM events:

  * `introstart`
  * `introend`
  * `outrostart`
  * `outroend`

    
    
    {#if visible}
    	<p
    		transition:fly={{ y: 200, duration: 2000 }}
    		on:introstart={() => (status = 'intro started')}
    		on:outrostart={() => (status = 'outro started')}
    		on:introend={() => (status = 'intro ended')}
    		on:outroend={() => (status = 'outro ended')}
    	>
    		Flies in and out
    	</p>
    {/if}

## in:_fn_ /out:_fn_ permalink

    
    
    in:fn
    
    
    in:fn={params}
    
    
    in:fn|global
    
    
    in:fn|global={params}
    
    
    in:fn|local
    
    
    in:fn|local={params}
    
    
    out:fn
    
    
    out:fn={params}
    
    
    out:fn|global
    
    
    out:fn|global={params}
    
    
    out:fn|local
    
    
    out:fn|local={params}

Similar to `transition:`, but only applies to elements entering (`in:`) or
leaving (`out:`) the DOM.

Unlike with `transition:`, transitions applied with `in:` and `out:` are not
bidirectional — an in transition will continue to 'play' alongside the out
transition, rather than reversing, if the block is outroed while the
transition is in progress. If an out transition is aborted, transitions will
restart from scratch.

    
    
    {#if visible}
    	<div in:fly out:fade>flies in, fades out</div>
    {/if}

## animate:_fn_ permalink

    
    
    animate:name
    
    
    animate:name={params}
    
    
    ts
    
    animation = (node: HTMLElement, { from: DOMRect, to: DOMRect } , params: any) => {
    
    	delay?: number,
    
    	duration?: number,
    
    	easing?: (t: number) => number,
    
    	css?: (t: number, u: number) => string,
    
    	tick?: (t: number, u: number) => void
    
    }
    
    
    ts
    
    DOMRect {
    
    	bottom: number,
    
    	height: number,
    
    	​​left: number,
    
    	right: number,
    
    	​top: number,
    
    	width: number,
    
    	x: number,
    
    	y: number
    
    }

An animation is triggered when the contents of a [keyed each
block](/docs/logic-blocks#each) are re-ordered. Animations do not run when an
element is added or removed, only when the index of an existing data item
within the each block changes. Animate directives must be on an element that
is an _immediate_ child of a keyed each block.

Animations can be used with Svelte's [built-in animation
functions](/docs/svelte-animate) or [custom animation
functions](/docs/element-directives#custom-animation-functions).

    
    
    <!-- When `list` is reordered the animation will run-->
    {#each list as item, index (item)}
    	<li animate:flip>{item}</li>
    {/each}

## Animation Parameterspermalink

As with actions and transitions, animations can have parameters.

(The double `{{curlies}}` aren't a special syntax; this is an object literal
inside an expression tag.)

    
    
    {#each list as item, index (item)}
    	<li animate:flip={{ delay: 500 }}>{item}</li>
    {/each}

## Custom animation functionspermalink

Animations can use custom functions that provide the `node`, an `animation`
object and any `parameters` as arguments. The `animation` parameter is an
object containing `from` and `to` properties each containing a
[DOMRect](https://developer.mozilla.org/en-US/docs/Web/API/DOMRect#Properties)
describing the geometry of the element in its `start` and `end` positions. The
`from` property is the DOMRect of the element in its starting position, and
the `to` property is the DOMRect of the element in its final position after
the list has been reordered and the DOM updated.

If the returned object has a `css` method, Svelte will create a CSS animation
that plays on the element.

The `t` argument passed to `css` is a value that goes from `0` and `1` after
the `easing` function has been applied. The `u` argument is equal to `1 - t`.

The function is called repeatedly _before_ the animation begins, with
different `t` and `u` arguments.

    
    
    <script>
    	import { cubicOut } from 'svelte/easing';
    
    	/**
    	 * @param {HTMLElement} node
    	 * @param {{ from: DOMRect; to: DOMRect }} states
    	 * @param {any} params
    	 */
    	function whizz(node, { from, to }, params) {
    		const dx = from.left - to.left;
    		const dy = from.top - to.top;
    
    		const d = Math.sqrt(dx * dx + dy * dy);
    
    		return {
    			delay: 0,
    			duration: Math.sqrt(d) * 120,
    			easing: cubicOut,
    			css: (t, u) => `transform: translate(${u * dx}px, ${u * dy}px) rotate(${t * 360}deg);`
    		};
    	}
    </script>
    
    {#each list as item, index (item)}
    	<div animate:whizz>{item}</div>
    {/each}

A custom animation function can also return a `tick` function, which is called
_during_ the animation with the same `t` and `u` arguments.

> If it's possible to use `css` instead of `tick`, do so — CSS animations can
> run off the main thread, preventing jank on slower devices.
    
    
    <script>
    	import { cubicOut } from 'svelte/easing';
    
    	/**
    	 * @param {HTMLElement} node
    	 * @param {{ from: DOMRect; to: DOMRect }} states
    	 * @param {any} params
    	 */
    	function whizz(node, { from, to }, params) {
    		const dx = from.left - to.left;
    		const dy = from.top - to.top;
    
    		const d = Math.sqrt(dx * dx + dy * dy);
    
    		return {
    			delay: 0,
    			duration: Math.sqrt(d) * 120,
    			easing: cubicOut,
    			tick: (t, u) => Object.assign(node.style, { color: t > 0.5 ? 'Pink' : 'Blue' })
    		};
    	}
    </script>
    
    {#each list as item, index (item)}
    	<div animate:whizz>{item}</div>
    {/each}

previous [Special tags](/docs/special-tags)

next [Component directives](/docs/component-directives)



Template Syntax

# Component directives

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/02-template-
syntax/06-component-directives.md)

## On this page On this page

## on:_eventname_ permalink

    
    
    on:eventname={handler}

Components can emit events using
[`createEventDispatcher`](/docs/svelte#createeventdispatcher) or by forwarding
DOM events.

    
    
    <script>
    	import { createEventDispatcher } from 'svelte';
    
    	const dispatch = createEventDispatcher();
    </script>
    
    <!-- programmatic dispatching -->
    <button on:click={() => dispatch('hello')}> one </button>
    
    <!-- declarative event forwarding -->
    <button on:click> two </button>

Listening for component events looks the same as listening for DOM events:

    
    
    <SomeComponent on:whatever={handler} />

As with DOM events, if the `on:` directive is used without a value, the event
will be forwarded, meaning that a consumer can listen for it.

    
    
    <SomeComponent on:whatever />

## \--style-propspermalink

    
    
    --style-props="anycssvalue"

You can also pass styles as props to components for the purposes of theming,
using CSS custom properties.

Svelte's implementation is essentially syntactic sugar for adding a wrapper
element. This example:

    
    
    <Slider bind:value min={0} --rail-color="black" --track-color="rgb(0, 0, 255)" />

Desugars to this:

    
    
    <div style="display: contents; --rail-color: black; --track-color: rgb(0, 0, 255)">
    	<Slider bind:value min={0} max={100} />
    </div>

**Note** : Since this is an extra `<div>`, beware that your CSS structure
might accidentally target this. Be mindful of this added wrapper element when
using this feature.

For SVG namespace, the example above desugars into using `<g>` instead:

    
    
    <g style="--rail-color: black; --track-color: rgb(0, 0, 255)">
    	<Slider bind:value min={0} max={100} />
    </g>

**Note** : Since this is an extra `<g>`, beware that your CSS structure might
accidentally target this. Be mindful of this added wrapper element when using
this feature.

Svelte's CSS Variables support allows for easily themeable components:

    
    
    <style>
    	.potato-slider-rail {
    		background-color: var(--rail-color, var(--theme-color, 'purple'));
    	}
    </style>

So you can set a high-level theme color:

    
    
    /* global.css */
    html {
    	--theme-color: black;
    }

Or override it at the consumer level:

    
    
    <Slider --rail-color="goldenrod" />

## bind:_property_ permalink

    
    
    bind:property={variable}

You can bind to component props using the same syntax as for elements.

    
    
    <Keypad bind:value={pin} />

While Svelte props are reactive without binding, that reactivity only flows
downward into the component by default. Using `bind:property` allows changes
to the property from within the component to flow back up out of the
component.

## bind:thispermalink

    
    
    bind:this={component_instance}

Components also support `bind:this`, allowing you to interact with component
instances programmatically.

    
    
    <ShoppingCart bind:this={cart} />
    
    <button on:click={() => cart.empty()}> Empty shopping cart </button>

> Note that we can't do `{cart.empty}` since `cart` is `undefined` when the
> button is first rendered and throws an error.

previous [Element directives](/docs/element-directives)

next [Special elements](/docs/special-elements)



Template Syntax

# Special elements

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/02-template-
syntax/07-special-elements.md)

## On this page On this page

## <slot>permalink

    
    
    <slot><!-- optional fallback --></slot>
    
    
    <slot name="x"><!-- optional fallback --></slot>
    
    
    <slot prop={value} />

Components can have child content, in the same way that elements can.

The content is exposed in the child component using the `<slot>` element,
which can contain fallback content that is rendered if no children are
provided.

    
    
    <!-- Widget.svelte -->
    <div>
    	<slot>
    		this fallback content will be rendered when no content is provided, like in the first example
    	</slot>
    </div>
    
    <!-- App.svelte -->
    <Widget />
    <!-- this component will render the default content -->
    
    <Widget>
    	<p>this is some child content that will overwrite the default slot content</p>
    </Widget>

Note: If you want to render regular `<slot>` element, You can use
`<svelte:element this="slot" />`.

### <slot name="_name_ ">permalink

Named slots allow consumers to target specific areas. They can also have
fallback content.

    
    
    <!-- Widget.svelte -->
    <div>
    	<slot name="header">No header was provided</slot>
    	<p>Some content between header and footer</p>
    	<slot name="footer" />
    </div>
    
    <!-- App.svelte -->
    <Widget>
    	<h1 slot="header">Hello</h1>
    	<p slot="footer">Copyright (c) 2019 Svelte Industries</p>
    </Widget>

Components can be placed in a named slot using the syntax `<Component
slot="name" />`. In order to place content in a slot without using a wrapper
element, you can use the special element `<svelte:fragment>`.

    
    
    <!-- Widget.svelte -->
    <div>
    	<slot name="header">No header was provided</slot>
    	<p>Some content between header and footer</p>
    	<slot name="footer" />
    </div>
    
    <!-- App.svelte -->
    <Widget>
    	<HeaderComponent slot="header" />
    	<svelte:fragment slot="footer">
    		<p>All rights reserved.</p>
    		<p>Copyright (c) 2019 Svelte Industries</p>
    	</svelte:fragment>
    </Widget>

### $$slotspermalink

`$$slots` is an object whose keys are the names of the slots passed into the
component by the parent. If the parent does not pass in a slot with a
particular name, that name will not be present in `$$slots`. This allows
components to render a slot (and other elements, like wrappers for styling)
only if the parent provides it.

Note that explicitly passing in an empty named slot will add that slot's name
to `$$slots`. For example, if a parent passes `<div slot="title" />` to a
child component, `$$slots.title` will be truthy within the child.

    
    
    <!-- Card.svelte -->
    <div>
    	<slot name="title" />
    	{#if $$slots.description}
    		<!-- This <hr> and slot will render only if a slot named "description" is provided. -->
    		<hr />
    		<slot name="description" />
    	{/if}
    </div>
    
    <!-- App.svelte -->
    <Card>
    	<h1 slot="title">Blog Post Title</h1>
    	<!-- No slot named "description" was provided so the optional slot will not be rendered. -->
    </Card>

### <slot key={_value_}>permalink

Slots can be rendered zero or more times and can pass values _back_ to the
parent using props. The parent exposes the values to the slot template using
the `let:` directive.

The usual shorthand rules apply — `let:item` is equivalent to
`let:item={item}`, and `<slot {item}>` is equivalent to `<slot item={item}>`.

    
    
    <!-- FancyList.svelte -->
    <ul>
    	{#each items as item}
    		<li class="fancy">
    			<slot prop={item} />
    		</li>
    	{/each}
    </ul>
    
    <!-- App.svelte -->
    <FancyList {items} let:prop={thing}>
    	<div>{thing.text}</div>
    </FancyList>

Named slots can also expose values. The `let:` directive goes on the element
with the `slot` attribute.

    
    
    <!-- FancyList.svelte -->
    <ul>
    	{#each items as item}
    		<li class="fancy">
    			<slot name="item" {item} />
    		</li>
    	{/each}
    </ul>
    
    <slot name="footer" />
    
    <!-- App.svelte -->
    <FancyList {items}>
    	<div slot="item" let:item>{item.text}</div>
    	<p slot="footer">Copyright (c) 2019 Svelte Industries</p>
    </FancyList>

## <svelte:self>permalink

The `<svelte:self>` element allows a component to include itself, recursively.

It cannot appear at the top level of your markup; it must be inside an if or
each block or passed to a component's slot to prevent an infinite loop.

App.svelte

    
    
    <script>
    	/** @type {number} */
    	export let count;
    </script>
    
    {#if count > 0}
    	<p>counting down... {count}</p>
    	<svelte:self count={count - 1} />
    {:else}
    	<p>lift-off!</p>
    {/if}

App.svelte

    
    
    <script lang="ts">
    	export let count: number;
    </script>
    
    {#if count > 0}
    	<p>counting down... {count}</p>
    	<svelte:self count={count - 1} />
    {:else}
    	<p>lift-off!</p>
    {/if}

## <svelte:component>permalink

    
    
    <svelte:component this={expression} />

The `<svelte:component>` element renders a component dynamically, using the
component constructor specified as the `this` property. When the property
changes, the component is destroyed and recreated.

If `this` is falsy, no component is rendered.

    
    
    <svelte:component this={currentSelection.component} foo={bar} />

## <svelte:element>permalink

    
    
    <svelte:element this={expression} />

The `<svelte:element>` element lets you render an element of a dynamically
specified type. This is useful for example when displaying rich text content
from a CMS. Any properties and event listeners present will be applied to the
element.

The only supported binding is `bind:this`, since the element type-specific
bindings that Svelte does at build time (e.g. `bind:value` for input elements)
do not work with a dynamic tag type.

If `this` has a nullish value, the element and its children will not be
rendered.

If `this` is the name of a [void element](https://developer.mozilla.org/en-
US/docs/Glossary/Void_element) (e.g., `br`) and `<svelte:element>` has child
elements, a runtime error will be thrown in development mode.

    
    
    <script>
    	let tag = 'div';
    
    	export let handler;
    </script>
    
    <svelte:element this={tag} on:click={handler}>Foo</svelte:element>

## <svelte:window>permalink

    
    
    <svelte:window on:event={handler} />
    
    
    <svelte:window bind:prop={value} />

The `<svelte:window>` element allows you to add event listeners to the
`window` object without worrying about removing them when the component is
destroyed, or checking for the existence of `window` when server-side
rendering.

Unlike `<svelte:self>`, this element may only appear at the top level of your
component and must never be inside a block or element.

App.svelte

    
    
    <script>
    	/** @param {KeyboardEvent} event */
    	function handleKeydown(event) {
    		alert(`pressed the ${event.key} key`);
    	}
    </script>
    
    <svelte:window on:keydown={handleKeydown} />

App.svelte

    
    
    <script lang="ts">
    	function handleKeydown(event: KeyboardEvent) {
    		alert(`pressed the ${event.key} key`);
    	}
    </script>
    
    <svelte:window on:keydown={handleKeydown} />

You can also bind to the following properties:

  * `innerWidth`
  * `innerHeight`
  * `outerWidth`
  * `outerHeight`
  * `scrollX`
  * `scrollY`
  * `online` — an alias for `window.navigator.onLine`
  * `devicePixelRatio`

All except `scrollX` and `scrollY` are readonly.

    
    
    <svelte:window bind:scrollY={y} />

> Note that the page will not be scrolled to the initial value to avoid
> accessibility issues. Only subsequent changes to the bound variable of
> `scrollX` and `scrollY` will cause scrolling. However, if the scrolling
> behaviour is desired, call `scrollTo()` in `onMount()`.

## <svelte:document>permalink

    
    
    <svelte:document on:event={handler} />
    
    
    <svelte:document bind:prop={value} />

Similarly to `<svelte:window>`, this element allows you to add listeners to
events on `document`, such as `visibilitychange`, which don't fire on
`window`. It also lets you use [actions](/docs/element-directives#use-action)
on `document`.

As with `<svelte:window>`, this element may only appear the top level of your
component and must never be inside a block or element.

    
    
    <svelte:document on:visibilitychange={handleVisibilityChange} use:someAction />

You can also bind to the following properties:

  * `fullscreenElement`
  * `visibilityState`

All are readonly.

## <svelte:body>permalink

    
    
    <svelte:body on:event={handler} />

Similarly to `<svelte:window>`, this element allows you to add listeners to
events on `document.body`, such as `mouseenter` and `mouseleave`, which don't
fire on `window`. It also lets you use [actions](/docs/element-directives#use-
action) on the `<body>` element.

As with `<svelte:window>` and `<svelte:document>`, this element may only
appear the top level of your component and must never be inside a block or
element.

    
    
    <svelte:body on:mouseenter={handleMouseenter} on:mouseleave={handleMouseleave} use:someAction />

## <svelte:head>permalink

    
    
    <svelte:head>...</svelte:head>

This element makes it possible to insert elements into `document.head`. During
server-side rendering, `head` content is exposed separately to the main `html`
content.

As with `<svelte:window>`, `<svelte:document>` and `<svelte:body>`, this
element may only appear at the top level of your component and must never be
inside a block or element.

    
    
    <svelte:head>
    	<title>Hello world!</title>
    	<meta name="description" content="This is where the description goes for SEO" />
    </svelte:head>

## <svelte:options>permalink

    
    
    <svelte:options option={value} />

The `<svelte:options>` element provides a place to specify per-component
compiler options, which are detailed in the [compiler section](/docs/svelte-
compiler#compile). The possible options are:

  * `immutable={true}` — you never use mutable data, so the compiler can do simple referential equality checks to determine if values have changed
  * `immutable={false}` — the default. Svelte will be more conservative about whether or not mutable objects have changed
  * `accessors={true}` — adds getters and setters for the component's props
  * `accessors={false}` — the default
  * `namespace="..."` — the namespace where this component will be used, most commonly "svg"; use the "foreign" namespace to opt out of case-insensitive attribute names and HTML-specific warnings
  * `customElement="..."` — the name to use when compiling this component as a custom element

    
    
    <svelte:options customElement="my-custom-element" />

## <svelte:fragment>permalink

The `<svelte:fragment>` element allows you to place content in a [named
slot](/docs/special-elements#slot-slot-name-name) without wrapping it in a
container DOM element. This keeps the flow layout of your document intact.

    
    
    <!-- Widget.svelte -->
    <div>
    	<slot name="header">No header was provided</slot>
    	<p>Some content between header and footer</p>
    	<slot name="footer" />
    </div>
    
    <!-- App.svelte -->
    <Widget>
    	<h1 slot="header">Hello</h1>
    	<svelte:fragment slot="footer">
    		<p>All rights reserved.</p>
    		<p>Copyright (c) 2019 Svelte Industries</p>
    	</svelte:fragment>
    </Widget>

previous [Component directives](/docs/component-directives)

next [svelte](/docs/svelte)



Runtime

# svelte

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/03-runtime/01-svelte.md)

## On this page On this page

The `svelte` package exposes [lifecycle
functions](https://learn.svelte.dev/tutorial/onmount) and the [context
API](https://learn.svelte.dev/tutorial/context-api).

## onMountpermalink

    
    
    ts
    
    function onMount<T>(
    
    	fn: () =>
    
    		| NotFunction<T>
    
    		| Promise<NotFunction<T>>
    
    		| (() => any)
    
    ): void;

The `onMount` function schedules a callback to run as soon as the component
has been mounted to the DOM. It must be called during the component's
initialisation (but doesn't need to live _inside_ the component; it can be
called from an external module).

`onMount` does not run inside a [server-side component](/docs/server-side-
component-api).

    
    
    <script>
    	import { onMount } from 'svelte';
    
    	onMount(() => {
    		console.log('the component has mounted');
    	});
    </script>

If a function is returned from `onMount`, it will be called when the component
is unmounted.

    
    
    <script>
    	import { onMount } from 'svelte';
    
    	onMount(() => {
    		const interval = setInterval(() => {
    			console.log('beep');
    		}, 1000);
    
    		return () => clearInterval(interval);
    	});
    </script>

> This behaviour will only work when the function passed to `onMount`
> _synchronously_ returns a value. `async` functions always return a
> `Promise`, and as such cannot _synchronously_ return a function.

## beforeUpdatepermalink

    
    
    ts
    
    function beforeUpdate(fn: () => any): void;

Schedules a callback to run immediately before the component is updated after
any state change.

> The first time the callback runs will be before the initial `onMount`
    
    
    <script>
    	import { beforeUpdate } from 'svelte';
    
    	beforeUpdate(() => {
    		console.log('the component is about to update');
    	});
    </script>

## afterUpdatepermalink

    
    
    ts
    
    function afterUpdate(fn: () => any): void;

Schedules a callback to run immediately after the component has been updated.

> The first time the callback runs will be after the initial `onMount`
    
    
    <script>
    	import { afterUpdate } from 'svelte';
    
    	afterUpdate(() => {
    		console.log('the component just updated');
    	});
    </script>

## onDestroypermalink

    
    
    ts
    
    function onDestroy(fn: () => any): void;

Schedules a callback to run immediately before the component is unmounted.

Out of `onMount`, `beforeUpdate`, `afterUpdate` and `onDestroy`, this is the
only one that runs inside a server-side component.

    
    
    <script>
    	import { onDestroy } from 'svelte';
    
    	onDestroy(() => {
    		console.log('the component is being destroyed');
    	});
    </script>

## tickpermalink

    
    
    ts
    
    function tick(): Promise<void>;

Returns a promise that resolves once any pending state changes have been
applied, or in the next microtask if there are none.

    
    
    <script>
    	import { beforeUpdate, tick } from 'svelte';
    
    	beforeUpdate(async () => {
    		console.log('the component is about to update');
    		await tick();
    		console.log('the component just updated');
    	});
    </script>

## setContextpermalink

    
    
    ts
    
    function setContext<T>(key: any, context: T): T;

Associates an arbitrary `context` object with the current component and the
specified `key` and returns that object. The context is then available to
children of the component (including slotted content) with `getContext`.

Like lifecycle functions, this must be called during component initialisation.

    
    
    <script>
    	import { setContext } from 'svelte';
    
    	setContext('answer', 42);
    </script>

> Context is not inherently reactive. If you need reactive values in context
> then you can pass a store into context, which _will_ be reactive.

## getContextpermalink

    
    
    ts
    
    function getContext<T>(key: any): T;

Retrieves the context that belongs to the closest parent component with the
specified `key`. Must be called during component initialisation.

    
    
    <script>
    	import { getContext } from 'svelte';
    
    	const answer = getContext('answer');
    </script>

## hasContextpermalink

    
    
    ts
    
    function hasContext(key: any): boolean;

Checks whether a given `key` has been set in the context of a parent
component. Must be called during component initialisation.

    
    
    <script>
    	import { hasContext } from 'svelte';
    
    	if (hasContext('answer')) {
    		// do something
    	}
    </script>

## getAllContextspermalink

    
    
    ts
    
    function getAllContexts<
    
    	T extends Map<any, any> = Map<any, any>
    
    >(): T;

Retrieves the whole context map that belongs to the closest parent component.
Must be called during component initialisation. Useful, for example, if you
programmatically create a component and want to pass the existing context to
it.

    
    
    <script>
    	import { getAllContexts } from 'svelte';
    
    	const contexts = getAllContexts();
    </script>

## createEventDispatcherpermalink

    
    
    ts
    
    function createEventDispatcher<
    
    	EventMap extends Record<string, any> = any
    
    >(): EventDispatcher<EventMap>;

Creates an event dispatcher that can be used to dispatch [component
events](/docs/component-directives#on-eventname). Event dispatchers are
functions that can take two arguments: `name` and `detail`.

Component events created with `createEventDispatcher` create a
[CustomEvent](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent).
These events do not [bubble](https://developer.mozilla.org/en-
US/docs/Learn/JavaScript/Building_blocks/Events#Event_bubbling_and_capture).
The `detail` argument corresponds to the
[CustomEvent.detail](https://developer.mozilla.org/en-
US/docs/Web/API/CustomEvent/detail) property and can contain any type of data.

    
    
    <script>
    	import { createEventDispatcher } from 'svelte';
    
    	const dispatch = createEventDispatcher();
    </script>
    
    <button on:click={() => dispatch('notify', 'detail value')}>Fire Event</button>

Events dispatched from child components can be listened to in their parent.
Any data provided when the event was dispatched is available on the `detail`
property of the event object.

    
    
    <script>
    	function callbackFunction(event) {
    		console.log(`Notify fired! Detail: ${event.detail}`);
    	}
    </script>
    
    <Child on:notify={callbackFunction} />

Events can be cancelable by passing a third parameter to the dispatch
function. The function returns `false` if the event is cancelled with
`event.preventDefault()`, otherwise it returns `true`.

    
    
    <script>
    	import { createEventDispatcher } from 'svelte';
    
    	const dispatch = createEventDispatcher();
    
    	function notify() {
    		const shouldContinue = dispatch('notify', 'detail value', { cancelable: true });
    		if (shouldContinue) {
    			// no one called preventDefault
    		} else {
    			// a listener called preventDefault
    		}
    	}
    </script>

You can type the event dispatcher to define which events it can receive. This
will make your code more type safe both within the component (wrong calls are
flagged) and when using the component (types of the events are now narrowed).
See [here](typescript#script-lang-ts-events) how to do it.

## Typespermalink

### ComponentConstructorOptionspermalink

    
    
    ts
    
    interface ComponentConstructorOptions<
    
    	Props extends Record<string, any> = Record<string, any>
    
    > {…}
    
    
    ts
    
    target: Element | Document | ShadowRoot;
    
    
    ts
    
    anchor?: Element;
    
    
    ts
    
    props?: Props;
    
    
    ts
    
    context?: Map<any, any>;
    
    
    ts
    
    hydrate?: boolean;
    
    
    ts
    
    intro?: boolean;
    
    
    ts
    
    $$inline?: boolean;

### ComponentEventspermalink

Convenience type to get the events the given component expects. Example:

    
    
    <script lang="ts">
       import type { ComponentEvents } from 'svelte';
       import Component from './Component.svelte';
    
       function handleCloseEvent(event: ComponentEvents<Component>['close']) {
    	  console.log(event.detail);
       }
    </script>
    
    <Component on:close={handleCloseEvent} />
    
    
    ts
    
    type ComponentEvents<Component extends SvelteComponent_1> =
    
    	Component extends SvelteComponent<any, infer Events>
    
    		? Events
    
    		: never;

### ComponentPropspermalink

Convenience type to get the props the given component expects. Example:

    
    
    <script lang="ts">
    	import type { ComponentProps } from 'svelte';
    	import Component from './Component.svelte';
    
    	const props: ComponentProps<Component> = { foo: 'bar' }; // Errors if these aren't the correct props
    </script>
    
    
    ts
    
    type ComponentProps<Component extends SvelteComponent_1> =
    
    	Component extends SvelteComponent<infer Props>
    
    		? Props
    
    		: never;

### ComponentTypepermalink

Convenience type to get the type of a Svelte component. Useful for example in
combination with dynamic components using `<svelte:component>`.

Example:

    
    
    <script lang="ts">
    	import type { ComponentType, SvelteComponent } from 'svelte';
    	import Component1 from './Component1.svelte';
    	import Component2 from './Component2.svelte';
    
    	const component: ComponentType = someLogic() ? Component1 : Component2;
    	const componentOfCertainSubType: ComponentType<SvelteComponent<{ needsThisProp: string }>> = someLogic() ? Component1 : Component2;
    </script>
    
    <svelte:component this={component} />
    <svelte:component this={componentOfCertainSubType} needsThisProp="hello" />
    
    
    ts
    
    type ComponentType<
    
    	Component extends SvelteComponent = SvelteComponent
    
    > = (new (
    
    	options: ComponentConstructorOptions<
    
    		Component extends SvelteComponent<infer Props>
    
    			? Props
    
    			: Record<string, any>
    
    	>
    
    ) => Component) & {
    
    	/** The custom element version of the component. Only present if compiled with the `customElement` compiler option */
    
    	element?: typeof HTMLElement;
    
    };

### SvelteComponentpermalink

Base class for Svelte components with some minor dev-enhancements. Used when
dev=true.

Can be used to create strongly typed Svelte components.

#### Example:permalink

You have component library on npm called `component-library`, from which you
export a component called `MyComponent`. For Svelte+TypeScript users, you want
to provide typings. Therefore you create a `index.d.ts`:

    
    
    ts
    
    import { SvelteComponent } from "svelte";
    
    export class MyComponent extends SvelteComponent<{foo: string}> {}

Typing this makes it possible for IDEs like VS Code with the Svelte extension
to provide intellisense and to use the component like this in a Svelte file
with TypeScript:

    
    
    <script lang="ts">
    	import { MyComponent } from "component-library";
    </script>
    <MyComponent foo={'bar'} />
    
    
    ts
    
    class SvelteComponent<
    
    	Props extends Record<string, any> = any,
    
    	Events extends Record<string, any> = any,
    
    	Slots extends Record<string, any> = any
    
    > extends SvelteComponent_1<Props, Events> {…}
    
    
    ts
    
    [prop: string]: any;
    
    
    ts
    
    constructor(options: ComponentConstructorOptions<Props>);
    
    
    ts
    
    $capture_state(): void;
    
    
    ts
    
    $inject_state(): void;

### SvelteComponentTypedpermalink

> Use `SvelteComponent` instead. See PR for more information:
> <https://github.com/sveltejs/svelte/pull/8512>
    
    
    ts
    
    class SvelteComponentTyped<
    
    	Props extends Record<string, any> = any,
    
    	Events extends Record<string, any> = any,
    
    	Slots extends Record<string, any> = any
    
    > extends SvelteComponent<Props, Events, Slots> {}

previous [Special elements](/docs/special-elements)

next [svelte/store](/docs/svelte-store)



Runtime

# svelte/store

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/03-runtime/02-svelte-
store.md)

## On this page On this page

The `svelte/store` module exports functions for creating
[readable](/docs/svelte-store#readable), [writable](/docs/svelte-
store#writable) and [derived](/docs/svelte-store#derived) stores.

Keep in mind that you don't _have_ to use these functions to enjoy the
[reactive `$store` syntax](/docs/svelte-components#script-4-prefix-stores-
with-$-to-access-their-values) in your components. Any object that correctly
implements `.subscribe`, unsubscribe, and (optionally) `.set` is a valid
store, and will work both with the special syntax, and with Svelte's built-in
[`derived` stores](/docs/svelte-store#derived).

This makes it possible to wrap almost any other reactive state handling
library for use in Svelte. Read more about the [store contract](/docs/svelte-
components#script-4-prefix-stores-with-$-to-access-their-values) to see what a
correct implementation looks like.

## writablepermalink

    
    
    ts
    
    function writable<T>(
    
    	value?: T | undefined,
    
    	start?: StartStopNotifier<T> | undefined
    
    ): Writable<T>;

Function that creates a store which has values that can be set from 'outside'
components. It gets created as an object with additional `set` and `update`
methods.

`set` is a method that takes one argument which is the value to be set. The
store value gets set to the value of the argument if the store value is not
already equal to it.

`update` is a method that takes one argument which is a callback. The callback
takes the existing store value as its argument and returns the new value to be
set to the store.

store.js

    
    
    ts
    
    import { writable } from 'svelte/store';
    
    
    
    
    const count = writable(0);
    
    
    
    
    count.subscribe((value) => {
    
    	console.log(value);
    
    }); // logs '0'
    
    
    
    
    count.set(1); // logs '1'
    
    
    
    
    count.update((n) => n + 1); // logs '2'

store.ts

    
    
    ts
    
    import { writable } from 'svelte/store';
    
    
    
    
    const count = writable(0);
    
    
    
    
    count.subscribe((value) => {
    
    	console.log(value);
    
    }); // logs '0'
    
    
    
    
    count.set(1); // logs '1'
    
    
    
    
    count.update((n) => n + 1); // logs '2'

If a function is passed as the second argument, it will be called when the
number of subscribers goes from zero to one (but not from one to two, etc).
That function will be passed a `set` function which changes the value of the
store, and an `update` function which works like the `update` method on the
store, taking a callback to calculate the store's new value from its old
value. It must return a `stop` function that is called when the subscriber
count goes from one to zero.

store.js

    
    
    ts
    
    import { writable } from 'svelte/store';
    
    
    
    
    const count = writable(0, () => {
    
    	console.log('got a subscriber');
    
    	return () => console.log('no more subscribers');
    
    });
    
    
    
    
    count.set(1); // does nothing
    
    
    
    
    const unsubscribe = count.subscribe((value) => {
    
    	console.log(value);
    
    }); // logs 'got a subscriber', then '1'
    
    
    
    
    unsubscribe(); // logs 'no more subscribers'

store.ts

    
    
    ts
    
    import { writable } from 'svelte/store';
    
    
    
    
    const count = writable(0, () => {
    
    	console.log('got a subscriber');
    
    	return () => console.log('no more subscribers');
    
    });
    
    
    
    
    count.set(1); // does nothing
    
    
    
    
    const unsubscribe = count.subscribe((value) => {
    
    	console.log(value);
    
    }); // logs 'got a subscriber', then '1'
    
    
    
    
    unsubscribe(); // logs 'no more subscribers'

Note that the value of a `writable` is lost when it is destroyed, for example
when the page is refreshed. However, you can write your own logic to sync the
value to for example the `localStorage`.

## readablepermalink

    
    
    ts
    
    function readable<T>(
    
    	value?: T | undefined,
    
    	start?: StartStopNotifier<T> | undefined
    
    ): Readable<T>;

Creates a store whose value cannot be set from 'outside', the first argument
is the store's initial value, and the second argument to `readable` is the
same as the second argument to `writable`.

    
    
    ts
    
    import { readable } from 'svelte/store';
    
    
    
    
    const time = readable(new Date(), (set) => {
    
    	set(new Date());
    
    
    
    
    	const interval = setInterval(() => {
    
    		set(new Date());
    
    	}, 1000);
    
    
    
    
    	return () => clearInterval(interval);
    
    });
    
    
    
    
    const ticktock = readable('tick', (set, update) => {
    
    	const interval = setInterval(() => {
    
    		update((sound) => (sound === 'tick' ? 'tock' : 'tick'));
    
    	}, 1000);
    
    
    
    
    	return () => clearInterval(interval);
    
    });

## derivedpermalink

    
    
    ts
    
    function derived<S extends Stores, T>(
    
    	stores: S,
    
    	fn: (
    
    		values: StoresValues<S>,
    
    		set: (value: T) => void,
    
    		update: (fn: Updater<T>) => void
    
    	) => Unsubscriber | void,
    
    	initial_value?: T | undefined
    
    ): Readable<T>;
    
    
    ts
    
    function derived<S extends Stores, T>(
    
    	stores: S,
    
    	fn: (values: StoresValues<S>) => T,
    
    	initial_value?: T | undefined
    
    ): Readable<T>;

Derives a store from one or more other stores. The callback runs initially
when the first subscriber subscribes and then whenever the store dependencies
change.

In the simplest version, `derived` takes a single store, and the callback
returns a derived value.

    
    
    ts
    
    import { derived } from 'svelte/store';
    
    
    
    
    const doubled = derived(a, ($a) => $a * 2);

The callback can set a value asynchronously by accepting a second argument,
`set`, and an optional third argument, `update`, calling either or both of
them when appropriate.

In this case, you can also pass a third argument to `derived` — the initial
value of the derived store before `set` or `update` is first called. If no
initial value is specified, the store's initial value will be `undefined`.

    
    
    ts
    
    import { derived } from 'svelte/store';
    
    
    
    
    const delayed = derived(
    
    	a,
    
    	($a, set) => {
    
    		setTimeout(() => set($a), 1000);
    
    	},
    
    	2000
    
    );
    
    
    
    
    const delayedIncrement = derived(a, ($a, set, update) => {
    
    	set($a);
    
    	setTimeout(() => update((x) => x + 1), 1000);
    
    	// every time $a produces a value, this produces two
    
    	// values, $a immediately and then $a + 1 a second later
    
    });

If you return a function from the callback, it will be called when a) the
callback runs again, or b) the last subscriber unsubscribes.

    
    
    ts
    
    import { derived } from 'svelte/store';
    
    
    
    
    const tick = derived(
    
    	frequency,
    
    	($frequency, set) => {
    
    		const interval = setInterval(() => {
    
    			set(Date.now());
    
    		}, 1000 / $frequency);
    
    
    
    
    		return () => {
    
    			clearInterval(interval);
    
    		};
    
    	},
    
    	2000
    
    );

In both cases, an array of arguments can be passed as the first argument
instead of a single store.

    
    
    ts
    
    import { derived } from 'svelte/store';
    
    
    
    
    const summed = derived([a, b], ([$a, $b]) => $a + $b);
    
    
    
    
    const delayed = derived([a, b], ([$a, $b], set) => {
    
    	setTimeout(() => set($a + $b), 1000);
    
    });

## readonlypermalink

    
    
    ts
    
    function readonly<T>(store: Readable<T>): Readable<T>;

This simple helper function makes a store readonly. You can still subscribe to
the changes from the original one using this new readable store.

    
    
    ts
    
    import { readonly, writable } from 'svelte/store';
    
    
    
    
    const writableStore = writable(1);
    
    const readableStore = readonly(writableStore);
    
    
    
    
    Property 'set' does not exist on type 'Readable<number>'.2339Property 'set' does not exist on type 'Readable<number>'.
    
    readableStore.subscribe(console.log);
    
    
    
    
    writableStore.set(2); // console: 2
    
    readableStore.set(2); // ERROR

## getpermalink

    
    
    ts
    
    function get<T>(store: Readable<T>): T;

Generally, you should read the value of a store by subscribing to it and using
the value as it changes over time. Occasionally, you may need to retrieve the
value of a store to which you're not subscribed. `get` allows you to do so.

> This works by creating a subscription, reading the value, then
> unsubscribing. It's therefore not recommended in hot code paths.
    
    
    ts
    
    import { get } from 'svelte/store';
    
    
    
    
    const value = get(store);

## Typespermalink

### Readablepermalink

Readable interface for subscribing.

    
    
    ts
    
    interface Readable<T> {…}
    
    
    ts
    
    subscribe(this: void, run: Subscriber<T>, invalidate?: Invalidator<T>): Unsubscriber;

  * `run` subscription callback
  * `invalidate` cleanup callback

Subscribe on value changes.

### StartStopNotifierpermalink

Start and stop notification callbacks. This function is called when the first
subscriber subscribes.

    
    
    ts
    
    type StartStopNotifier<T> = (
    
    	set: (value: T) => void,
    
    	update: (fn: Updater<T>) => void
    
    ) => void | (() => void);

### Subscriberpermalink

Callback to inform of a value updates.

    
    
    ts
    
    type Subscriber<T> = (value: T) => void;

### Unsubscriberpermalink

Unsubscribes from value updates.

    
    
    ts
    
    type Unsubscriber = () => void;

### Updaterpermalink

Callback to update a value.

    
    
    ts
    
    type Updater<T> = (value: T) => T;

### Writablepermalink

Writable interface for both updating and subscribing.

    
    
    ts
    
    interface Writable<T> extends Readable<T> {…}
    
    
    ts
    
    set(this: void, value: T): void;

  * `value` to set

Set value and inform subscribers.

    
    
    ts
    
    update(this: void, updater: Updater<T>): void;

  * `updater` callback

Update value using callback and inform subscribers.

previous [svelte](/docs/svelte)

next [svelte/motion](/docs/svelte-motion)



Runtime

# svelte/motion

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/03-runtime/03-svelte-
motion.md)

## On this page On this page

The `svelte/motion` module exports two functions, `tweened` and `spring`, for
creating writable stores whose values change over time after `set` and
`update`, rather than immediately.

## tweenedpermalink

    
    
    ts
    
    function tweened<T>(
    
    	value?: T | undefined,
    
    	defaults?: TweenedOptions<T> | undefined
    
    ): Tweened<T>;

Tweened stores update their values over a fixed duration. The following
options are available:

  * `delay` (`number`, default 0) — milliseconds before starting
  * `duration` (`number` | `function`, default 400) — milliseconds the tween lasts
  * `easing` (`function`, default `t => t`) — an [easing function](/docs/svelte-easing)
  * `interpolate` (`function`) — see below

`store.set` and `store.update` can accept a second `options` argument that
will override the options passed in upon instantiation.

Both functions return a Promise that resolves when the tween completes. If the
tween is interrupted, the promise will never resolve.

Out of the box, Svelte will interpolate between two numbers, two arrays or two
objects (as long as the arrays and objects are the same 'shape', and their
'leaf' properties are also numbers).

    
    
    <script>
    	import { tweened } from 'svelte/motion';
    	import { cubicOut } from 'svelte/easing';
    
    	const size = tweened(1, {
    		duration: 300,
    		easing: cubicOut
    	});
    
    	function handleClick() {
    		// this is equivalent to size.update(n => n + 1)
    		$size += 1;
    	}
    </script>
    
    <button on:click={handleClick} style="transform: scale({$size}); transform-origin: 0 0">
    	embiggen
    </button>

If the initial value is `undefined` or `null`, the first value change will
take effect immediately. This is useful when you have tweened values that are
based on props, and don't want any motion when the component first renders.

    
    
    ts
    
    import { tweened } from 'svelte/motion';
    
    import { cubicOut } from 'svelte/easing';
    
    
    
    
    const size = tweened(undefined, {
    
    	duration: 300,
    
    	easing: cubicOut
    
    });
    
    
    
    
    $: $size = big ? 100 : 10;

The `interpolate` option allows you to tween between _any_ arbitrary values.
It must be an `(a, b) => t => value` function, where `a` is the starting
value, `b` is the target value, `t` is a number between 0 and 1, and `value`
is the result. For example, we can use the
[d3-interpolate](https://github.com/d3/d3-interpolate) package to smoothly
interpolate between two colours.

    
    
    <script>
    	import { interpolateLab } from 'd3-interpolate';
    	import { tweened } from 'svelte/motion';
    
    	const colors = ['rgb(255, 62, 0)', 'rgb(64, 179, 255)', 'rgb(103, 103, 120)'];
    
    	const color = tweened(colors[0], {
    		duration: 800,
    		interpolate: interpolateLab
    	});
    </script>
    
    {#each colors as c}
    	<button style="background-color: {c}; color: white; border: none;" on:click={(e) => color.set(c)}>
    		{c}
    	</button>
    {/each}
    
    <h1 style="color: {$color}">{$color}</h1>

## springpermalink

    
    
    ts
    
    function spring<T = any>(
    
    	value?: T | undefined,
    
    	opts?: SpringOpts | undefined
    
    ): Spring<T>;

A `spring` store gradually changes to its target value based on its
`stiffness` and `damping` parameters. Whereas `tweened` stores change their
values over a fixed duration, `spring` stores change over a duration that is
determined by their existing velocity, allowing for more natural-seeming
motion in many situations. The following options are available:

  * `stiffness` (`number`, default `0.15`) — a value between 0 and 1 where higher means a 'tighter' spring
  * `damping` (`number`, default `0.8`) — a value between 0 and 1 where lower means a 'springier' spring
  * `precision` (`number`, default `0.01`) — determines the threshold at which the spring is considered to have 'settled', where lower means more precise

All of the options above can be changed while the spring is in motion, and
will take immediate effect.

    
    
    ts
    
    import { spring } from 'svelte/motion';
    
    
    
    
    const size = spring(100);
    
    size.stiffness = 0.3;
    
    size.damping = 0.4;
    
    size.precision = 0.005;

As with [`tweened`](/docs/svelte-motion#tweened) stores, `set` and `update`
return a Promise that resolves if the spring settles.

Both `set` and `update` can take a second argument — an object with `hard` or
`soft` properties. `{ hard: true }` sets the target value immediately; `{
soft: n }` preserves existing momentum for `n` seconds before settling. `{
soft: true }` is equivalent to `{ soft: 0.5 }`.

    
    
    ts
    
    import { spring } from 'svelte/motion';
    
    
    
    
    const coords = spring({ x: 50, y: 50 });
    
    // updates the value immediately
    
    coords.set({ x: 100, y: 200 }, { hard: true });
    
    // preserves existing momentum for 1s
    
    coords.update(
    
    	(target_coords, coords) => {
    
    		return { x: target_coords.x, y: coords.y };
    
    	},
    
    	{ soft: 1 }
    
    );

[See a full example on the spring
tutorial.](https://learn.svelte.dev/tutorial/springs)

    
    
    <script>
    	import { spring } from 'svelte/motion';
    
    	const coords = spring(
    		{ x: 50, y: 50 },
    		{
    			stiffness: 0.1,
    			damping: 0.25
    		}
    	);
    </script>

If the initial value is `undefined` or `null`, the first value change will
take effect immediately, just as with `tweened` values (see above).

    
    
    ts
    
    import { spring } from 'svelte/motion';
    
    
    
    
    const size = spring();
    
    $: $size = big ? 100 : 10;

## Typespermalink

### Springpermalink

    
    
    ts
    
    interface Spring<T> extends Readable<T> {…}
    
    
    ts
    
    set: (new_value: T, opts?: SpringUpdateOpts) => Promise<void>;
    
    
    ts
    
    update: (fn: Updater<T>, opts?: SpringUpdateOpts) => Promise<void>;
    
    
    ts
    
    precision: number;
    
    
    ts
    
    damping: number;
    
    
    ts
    
    stiffness: number;

### Tweenedpermalink

    
    
    ts
    
    interface Tweened<T> extends Readable<T> {…}
    
    
    ts
    
    set(value: T, opts?: TweenedOptions<T>): Promise<void>;
    
    
    ts
    
    update(updater: Updater<T>, opts?: TweenedOptions<T>): Promise<void>;

previous [svelte/store](/docs/svelte-store)

next [svelte/transition](/docs/svelte-transition)



Runtime

# svelte/transition

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/03-runtime/04-svelte-
transition.md)

## On this page On this page

The `svelte/transition` module exports seven functions: `fade`, `blur`, `fly`,
`slide`, `scale`, `draw` and `crossfade`. They are for use with Svelte
[`transitions`](/docs/element-directives#transition-fn).

## fadepermalink

    
    
    ts
    
    function fade(
    
    	node: Element,
    
    	{ delay, duration, easing }?: FadeParams | undefined
    
    ): TransitionConfig;
    
    
    transition:fade={params}
    
    
    in:fade={params}
    
    
    out:fade={params}

Animates the opacity of an element from 0 to the current opacity for `in`
transitions and from the current opacity to 0 for `out` transitions.

`fade` accepts the following parameters:

  * `delay` (`number`, default 0) — milliseconds before starting
  * `duration` (`number`, default 400) — milliseconds the transition lasts
  * `easing` (`function`, default `linear`) — an [easing function](/docs/svelte-easing)

You can see the `fade` transition in action in the [transition
tutorial](https://learn.svelte.dev/tutorial/transition).

    
    
    <script>
    	import { fade } from 'svelte/transition';
    </script>
    
    {#if condition}
    	<div transition:fade={{ delay: 250, duration: 300 }}>fades in and out</div>
    {/if}

## blurpermalink

    
    
    ts
    
    function blur(
    
    	node: Element,
    
    	{
    
    		delay,
    
    		duration,
    
    		easing,
    
    		amount,
    
    		opacity
    
    	}?: BlurParams | undefined
    
    ): TransitionConfig;
    
    
    transition:blur={params}
    
    
    in:blur={params}
    
    
    out:blur={params}

Animates a `blur` filter alongside an element's opacity.

`blur` accepts the following parameters:

  * `delay` (`number`, default 0) — milliseconds before starting
  * `duration` (`number`, default 400) — milliseconds the transition lasts
  * `easing` (`function`, default `cubicInOut`) — an [easing function](/docs/svelte-easing)
  * `opacity` (`number`, default 0) - the opacity value to animate out to and in from
  * `amount` (`number | string`, default 5) - the size of the blur. Supports css units (for example: `"4rem"`). The default unit is `px`

    
    
    <script>
    	import { blur } from 'svelte/transition';
    </script>
    
    {#if condition}
    	<div transition:blur={{ amount: 10 }}>fades in and out</div>
    {/if}

## flypermalink

    
    
    ts
    
    function fly(
    
    	node: Element,
    
    	{
    
    		delay,
    
    		duration,
    
    		easing,
    
    		x,
    
    		y,
    
    		opacity
    
    	}?: FlyParams | undefined
    
    ): TransitionConfig;
    
    
    transition:fly={params}
    
    
    in:fly={params}
    
    
    out:fly={params}

Animates the x and y positions and the opacity of an element. `in` transitions
animate from the provided values, passed as parameters to the element's
default values. `out` transitions animate from the element's default values to
the provided values.

`fly` accepts the following parameters:

  * `delay` (`number`, default 0) — milliseconds before starting
  * `duration` (`number`, default 400) — milliseconds the transition lasts
  * `easing` (`function`, default `cubicOut`) — an [easing function](/docs/svelte-easing)
  * `x` (`number | string`, default 0) - the x offset to animate out to and in from
  * `y` (`number | string`, default 0) - the y offset to animate out to and in from
  * `opacity` (`number`, default 0) - the opacity value to animate out to and in from

x and y use `px` by default but support css units, for example `x: '100vw'` or
`y: '50%'`. You can see the `fly` transition in action in the [transition
tutorial](https://learn.svelte.dev/tutorial/adding-parameters-to-transitions).

    
    
    <script>
    	import { fly } from 'svelte/transition';
    	import { quintOut } from 'svelte/easing';
    </script>
    
    {#if condition}
    	<div
    		transition:fly={{ delay: 250, duration: 300, x: 100, y: 500, opacity: 0.5, easing: quintOut }}
    	>
    		flies in and out
    	</div>
    {/if}

## slidepermalink

    
    
    ts
    
    function slide(
    
    	node: Element,
    
    	{
    
    		delay,
    
    		duration,
    
    		easing,
    
    		axis
    
    	}?: SlideParams | undefined
    
    ): TransitionConfig;
    
    
    transition:slide={params}
    
    
    in:slide={params}
    
    
    out:slide={params}

Slides an element in and out.

`slide` accepts the following parameters:

  * `delay` (`number`, default 0) — milliseconds before starting
  * `duration` (`number`, default 400) — milliseconds the transition lasts
  * `easing` (`function`, default `cubicOut`) — an [easing function](/docs/svelte-easing)

  * `axis` (`x` | `y`, default `y`) — the axis of motion along which the transition occurs

    
    
    <script>
    	import { slide } from 'svelte/transition';
    	import { quintOut } from 'svelte/easing';
    </script>
    
    {#if condition}
    	<div transition:slide={{ delay: 250, duration: 300, easing: quintOut, axis: 'x' }}>
    		slides in and out horizontally
    	</div>
    {/if}

## scalepermalink

    
    
    ts
    
    function scale(
    
    	node: Element,
    
    	{
    
    		delay,
    
    		duration,
    
    		easing,
    
    		start,
    
    		opacity
    
    	}?: ScaleParams | undefined
    
    ): TransitionConfig;
    
    
    transition:scale={params}
    
    
    in:scale={params}
    
    
    out:scale={params}

Animates the opacity and scale of an element. `in` transitions animate from an
element's current (default) values to the provided values, passed as
parameters. `out` transitions animate from the provided values to an element's
default values.

`scale` accepts the following parameters:

  * `delay` (`number`, default 0) — milliseconds before starting
  * `duration` (`number`, default 400) — milliseconds the transition lasts
  * `easing` (`function`, default `cubicOut`) — an [easing function](/docs/svelte-easing)
  * `start` (`number`, default 0) - the scale value to animate out to and in from
  * `opacity` (`number`, default 0) - the opacity value to animate out to and in from

    
    
    <script>
    	import { scale } from 'svelte/transition';
    	import { quintOut } from 'svelte/easing';
    </script>
    
    {#if condition}
    	<div transition:scale={{ duration: 500, delay: 500, opacity: 0.5, start: 0.5, easing: quintOut }}>
    		scales in and out
    	</div>
    {/if}

## drawpermalink

    
    
    ts
    
    function draw(
    
    	node: SVGElement & {
    
    		getTotalLength(): number;
    
    	},
    
    	{
    
    		delay,
    
    		speed,
    
    		duration,
    
    		easing
    
    	}?: DrawParams | undefined
    
    ): TransitionConfig;
    
    
    transition:draw={params}
    
    
    in:draw={params}
    
    
    out:draw={params}

Animates the stroke of an SVG element, like a snake in a tube. `in`
transitions begin with the path invisible and draw the path to the screen over
time. `out` transitions start in a visible state and gradually erase the path.
`draw` only works with elements that have a `getTotalLength` method, like
`<path>` and `<polyline>`.

`draw` accepts the following parameters:

  * `delay` (`number`, default 0) — milliseconds before starting
  * `speed` (`number`, default undefined) - the speed of the animation, see below.
  * `duration` (`number` | `function`, default 800) — milliseconds the transition lasts
  * `easing` (`function`, default `cubicInOut`) — an [easing function](/docs/svelte-easing)

The `speed` parameter is a means of setting the duration of the transition
relative to the path's length. It is a modifier that is applied to the length
of the path: `duration = length / speed`. A path that is 1000 pixels with a
speed of 1 will have a duration of `1000ms`, setting the speed to `0.5` will
double that duration and setting it to `2` will halve it.

    
    
    <script>
    	import { draw } from 'svelte/transition';
    	import { quintOut } from 'svelte/easing';
    </script>
    
    <svg viewBox="0 0 5 5" xmlns="http://www.w3.org/2000/svg">
    	{#if condition}
    		<path
    			transition:draw={{ duration: 5000, delay: 500, easing: quintOut }}
    			d="M2 1 h1 v1 h1 v1 h-1 v1 h-1 v-1 h-1 v-1 h1 z"
    			fill="none"
    			stroke="cornflowerblue"
    			stroke-width="0.1px"
    			stroke-linejoin="round"
    		/>
    	{/if}
    </svg>

## crossfadepermalink

    
    
    ts
    
    function crossfade({
    
    	fallback,
    
    	...defaults
    
    }: CrossfadeParams & {
    
    	fallback?:
    
    		| ((
    
    				node: Element,
    
    				params: CrossfadeParams,
    
    				intro: boolean
    
    		  ) => TransitionConfig)
    
    		| undefined;
    
    }): [
    
    	(
    
    		node: any,
    
    		params: CrossfadeParams & {
    
    			key: any;
    
    		}
    
    	) => () => TransitionConfig,
    
    	(
    
    		node: any,
    
    		params: CrossfadeParams & {
    
    			key: any;
    
    		}
    
    	) => () => TransitionConfig
    
    ];

The `crossfade` function creates a pair of [transitions](/docs/element-
directives#transition-fn) called `send` and `receive`. When an element is
'sent', it looks for a corresponding element being 'received', and generates a
transition that transforms the element to its counterpart's position and fades
it out. When an element is 'received', the reverse happens. If there is no
counterpart, the `fallback` transition is used.

`crossfade` accepts the following parameters:

  * `delay` (`number`, default 0) — milliseconds before starting
  * `duration` (`number` | `function`, default 800) — milliseconds the transition lasts
  * `easing` (`function`, default `cubicOut`) — an [easing function](/docs/svelte-easing)
  * `fallback` (`function`) — A fallback [transition](/docs/element-directives#transition-fn) to use for send when there is no matching element being received, and for receive when there is no element being sent.

    
    
    <script>
    	import { crossfade } from 'svelte/transition';
    	import { quintOut } from 'svelte/easing';
    
    	const [send, receive] = crossfade({
    		duration: 1500,
    		easing: quintOut
    	});
    </script>
    
    {#if condition}
    	<h1 in:send={{ key }} out:receive={{ key }}>BIG ELEM</h1>
    {:else}
    	<small in:send={{ key }} out:receive={{ key }}>small elem</small>
    {/if}

## Typespermalink

### BlurParamspermalink

    
    
    ts
    
    interface BlurParams {…}
    
    
    ts
    
    delay?: number;
    
    
    ts
    
    duration?: number;
    
    
    ts
    
    easing?: EasingFunction;
    
    
    ts
    
    amount?: number | string;
    
    
    ts
    
    opacity?: number;

### CrossfadeParamspermalink

    
    
    ts
    
    interface CrossfadeParams {…}
    
    
    ts
    
    delay?: number;
    
    
    ts
    
    duration?: number | ((len: number) => number);
    
    
    ts
    
    easing?: EasingFunction;

### DrawParamspermalink

    
    
    ts
    
    interface DrawParams {…}
    
    
    ts
    
    delay?: number;
    
    
    ts
    
    speed?: number;
    
    
    ts
    
    duration?: number | ((len: number) => number);
    
    
    ts
    
    easing?: EasingFunction;

### EasingFunctionpermalink

    
    
    ts
    
    type EasingFunction = (t: number) => number;

### FadeParamspermalink

    
    
    ts
    
    interface FadeParams {…}
    
    
    ts
    
    delay?: number;
    
    
    ts
    
    duration?: number;
    
    
    ts
    
    easing?: EasingFunction;

### FlyParamspermalink

    
    
    ts
    
    interface FlyParams {…}
    
    
    ts
    
    delay?: number;
    
    
    ts
    
    duration?: number;
    
    
    ts
    
    easing?: EasingFunction;
    
    
    ts
    
    x?: number | string;
    
    
    ts
    
    y?: number | string;
    
    
    ts
    
    opacity?: number;

### ScaleParamspermalink

    
    
    ts
    
    interface ScaleParams {…}
    
    
    ts
    
    delay?: number;
    
    
    ts
    
    duration?: number;
    
    
    ts
    
    easing?: EasingFunction;
    
    
    ts
    
    start?: number;
    
    
    ts
    
    opacity?: number;

### SlideParamspermalink

    
    
    ts
    
    interface SlideParams {…}
    
    
    ts
    
    delay?: number;
    
    
    ts
    
    duration?: number;
    
    
    ts
    
    easing?: EasingFunction;
    
    
    ts
    
    axis?: 'x' | 'y';

### TransitionConfigpermalink

    
    
    ts
    
    interface TransitionConfig {…}
    
    
    ts
    
    delay?: number;
    
    
    ts
    
    duration?: number;
    
    
    ts
    
    easing?: EasingFunction;
    
    
    ts
    
    css?: (t: number, u: number) => string;
    
    
    ts
    
    tick?: (t: number, u: number) => void;

previous [svelte/motion](/docs/svelte-motion)

next [svelte/animate](/docs/svelte-animate)



Runtime

# svelte/animate

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/03-runtime/05-svelte-
animate.md)

## On this page On this page

The `svelte/animate` module exports one function for use with Svelte
[animations](/docs/element-directives#animate-fn).

## flippermalink

    
    
    ts
    
    function flip(
    
    	node: Element,
    
    	{
    
    		from,
    
    		to
    
    	}: {
    
    		from: DOMRect;
    
    		to: DOMRect;
    
    	},
    
    	params?: FlipParams
    
    ): AnimationConfig;
    
    
    animate:flip={params}

The `flip` function calculates the start and end position of an element and
animates between them, translating the `x` and `y` values. `flip` stands for
[First, Last, Invert, Play](https://aerotwist.com/blog/flip-your-animations/).

`flip` accepts the following parameters:

  * `delay` (`number`, default 0) — milliseconds before starting
  * `duration` (`number` | `function`, default `d => Math.sqrt(d) * 120`) — see below
  * `easing` (`function`, default `cubicOut`) — an [easing function](/docs/svelte-easing)

`duration` can be provided as either:

  * a `number`, in milliseconds.
  * a function, `distance: number => duration: number`, receiving the distance the element will travel in pixels and returning the duration in milliseconds. This allows you to assign a duration that is relative to the distance travelled by each element.

You can see a full example on the [animations
tutorial](https://learn.svelte.dev/tutorial/animate).

    
    
    <script>
    	import { flip } from 'svelte/animate';
    	import { quintOut } from 'svelte/easing';
    
    	let list = [1, 2, 3];
    </script>
    
    {#each list as n (n)}
    	<div animate:flip={{ delay: 250, duration: 250, easing: quintOut }}>
    		{n}
    	</div>
    {/each}

## Typespermalink

### AnimationConfigpermalink

    
    
    ts
    
    interface AnimationConfig {…}
    
    
    ts
    
    delay?: number;
    
    
    ts
    
    duration?: number;
    
    
    ts
    
    easing?: (t: number) => number;
    
    
    ts
    
    css?: (t: number, u: number) => string;
    
    
    ts
    
    tick?: (t: number, u: number) => void;

### FlipParamspermalink

    
    
    ts
    
    interface FlipParams {…}
    
    
    ts
    
    delay?: number;
    
    
    ts
    
    duration?: number | ((len: number) => number);
    
    
    ts
    
    easing?: (t: number) => number;

previous [svelte/transition](/docs/svelte-transition)

next [svelte/easing](/docs/svelte-easing)



Runtime

# svelte/easing

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/03-runtime/06-svelte-
easing.md)

## On this page On this page

Easing functions specify the rate of change over time and are useful when
working with Svelte's built-in transitions and animations as well as the
tweened and spring utilities. `svelte/easing` contains 31 named exports, a
`linear` ease and 3 variants of 10 different easing functions: `in`, `out` and
`inOut`.

You can explore the various eases using the [ease
visualiser](/examples/easing) in the [examples section](/examples).

ease | in | out | inOut  
---|---|---|---  
**back** | `backIn` | `backOut` | `backInOut`  
**bounce** | `bounceIn` | `bounceOut` | `bounceInOut`  
**circ** | `circIn` | `circOut` | `circInOut`  
**cubic** | `cubicIn` | `cubicOut` | `cubicInOut`  
**elastic** | `elasticIn` | `elasticOut` | `elasticInOut`  
**expo** | `expoIn` | `expoOut` | `expoInOut`  
**quad** | `quadIn` | `quadOut` | `quadInOut`  
**quart** | `quartIn` | `quartOut` | `quartInOut`  
**quint** | `quintIn` | `quintOut` | `quintInOut`  
**sine** | `sineIn` | `sineOut` | `sineInOut`  
  
previous [svelte/animate](/docs/svelte-animate)

next [svelte/action](/docs/svelte-action)



Runtime

# svelte/action

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/03-runtime/07-svelte-
action.md)

## On this page On this page

Actions are functions that are called when an element is created. They can
return an object with a `destroy` method that is called after the element is
unmounted:

App.svelte

    
    
    <script>
    	/** @type {import('svelte/action').Action}  */
    	function foo(node) {
    		// the node has been mounted in the DOM
    
    		return {
    			destroy() {
    				// the node has been removed from the DOM
    			}
    		};
    	}
    </script>
    
    <div use:foo />

App.svelte

    
    
    <script lang="ts">
    	import type { Action } from 'svelte/action';
    	
    	const foo: Action = (node) => {
    		// the node has been mounted in the DOM
    	
    		return {
    			destroy() {
    				// the node has been removed from the DOM
    			},
    		};
    	};
    </script>
    
    <div use:foo />

An action can have a parameter. If the returned value has an `update` method,
it will be called immediately after Svelte has applied updates to the markup
whenever that parameter changes.

> Don't worry that we're redeclaring the `foo` function for every component
> instance — Svelte will hoist any functions that don't depend on local state
> out of the component definition.

App.svelte

    
    
    <script>
    	/** @type {string} */
    	export let bar;
    
    	/** @type {import('svelte/action').Action<HTMLElement, string>}  */
    	function foo(node, bar) {
    		// the node has been mounted in the DOM
    
    		return {
    			update(bar) {
    				// the value of `bar` has changed
    			},
    
    			destroy() {
    				// the node has been removed from the DOM
    			}
    		};
    	}
    </script>
    
    <div use:foo={bar} />

App.svelte

    
    
    <script lang="ts">
    	import type { Action } from 'svelte/action';
    	
    	export let bar: string;
    	
    	const foo: Action<HTMLElement, string> = (node, bar) => {
    		// the node has been mounted in the DOM
    	
    		return {
    			update(bar) {
    				// the value of `bar` has changed
    			},
    	
    			destroy() {
    				// the node has been removed from the DOM
    			},
    		};
    	};
    </script>
    
    <div use:foo={bar} />

## Attributespermalink

Sometimes actions emit custom events and apply custom attributes to the
element they are applied to. To support this, actions typed with
`[Action](/docs/svelte-action#types-action)` or `[ActionReturn](/docs/svelte-
action#types-actionreturn)` type can have a last parameter, `Attributes`:

App.svelte

    
    
    <script>
    	/**
    	 * @type {import('svelte/action').Action<HTMLDivElement, { prop: any }, { 'on:emit': (e: CustomEvent<string>) => void }>}
    	 */
    	function foo(node, { prop }) {
    		// the node has been mounted in the DOM
    
    		//...LOGIC
    		node.dispatchEvent(new CustomEvent('emit', { detail: 'hello' }));
    
    		return {
    			destroy() {
    				// the node has been removed from the DOM
    			}
    		};
    	}
    </script>
    
    <div on:emit={handleEmit} use:foo={{ prop: 'someValue' }} />

App.svelte

    
    
    <script lang="ts">
    	import type { Action } from 'svelte/action';
    	
    	const foo: Action<
    		HTMLDivElement,
    		{ prop: any },
    		{ 'on:emit': (e: CustomEvent<string>) => void }
    	> = (node, { prop }) => {
    		// the node has been mounted in the DOM
    	
    		//...LOGIC
    		node.dispatchEvent(new CustomEvent('emit', { detail: 'hello' }));
    	
    		return {
    			destroy() {
    				// the node has been removed from the DOM
    			},
    		};
    	};
    </script>
    
    <div on:emit={handleEmit} use:foo={{ prop: 'someValue' }} />

## Typespermalink

### Actionpermalink

Actions are functions that are called when an element is created. You can use
this interface to type such actions. The following example defines an action
that only works on `<div>` elements and optionally accepts a parameter which
it has a default value for:

    
    
    ts
    
    export const myAction: Action<HTMLDivElement, { someProperty: boolean } | undefined> = (node, param = { someProperty: true }) => {
    
      // ...
    
    }

`[Action](/docs/svelte-action#types-action)<HTMLDivElement>` and
`[Action](/docs/svelte-action#types-action)<HTMLDivElement, undefined>` both
signal that the action accepts no parameters.

You can return an object with methods `update` and `destroy` from the function
and type which additional attributes and events it has. See interface
`[ActionReturn](/docs/svelte-action#types-actionreturn)` for more details.

Docs: <https://svelte.dev/docs/svelte-action>

    
    
    ts
    
    interface Action<
    
    	Element = HTMLElement,
    
    	Parameter = undefined,
    
    	Attributes extends Record<string, any> = Record<
    
    		never,
    
    		any
    
    	>
    
    > {…}
    
    
    ts
    
    <Node extends Element>(
    
    	...args: undefined extends Parameter
    
    		? [node: Node, parameter?: Parameter]
    
    		: [node: Node, parameter: Parameter]
    
    ): void | ActionReturn<Parameter, Attributes>;

### ActionReturnpermalink

Actions can return an object containing the two properties defined in this
interface. Both are optional.

  * update: An action can have a parameter. This method will be called whenever that parameter changes, immediately after Svelte has applied updates to the markup. `[ActionReturn](/docs/svelte-action#types-actionreturn)` and `[ActionReturn](/docs/svelte-action#types-actionreturn)<undefined>` both mean that the action accepts no parameters.
  * destroy: Method that is called after the element is unmounted

Additionally, you can specify which additional attributes and events the
action enables on the applied element. This applies to TypeScript typings only
and has no effect at runtime.

Example usage:

    
    
    ts
    
    interface Attributes {
    
    	newprop?: string;
    
    	'on:event': (e: CustomEvent<boolean>) => void;
    
    }
    
    
    
    
    export function myAction(node: HTMLElement, parameter: Parameter): ActionReturn<Parameter, Attributes> {
    
    	// ...
    
    	return {
    
    		update: (updatedParameter) => {...},
    
    		destroy: () => {...}
    
    	};
    
    }

Docs: <https://svelte.dev/docs/svelte-action>

    
    
    ts
    
    interface ActionReturn<
    
    	Parameter = undefined,
    
    	Attributes extends Record<string, any> = Record<
    
    		never,
    
    		any
    
    	>
    
    > {…}
    
    
    ts
    
    update?: (parameter: Parameter) => void;
    
    
    ts
    
    destroy?: () => void;

previous [svelte/easing](/docs/svelte-easing)

next [svelte/compiler](/docs/svelte-compiler)



Compiler and API

# svelte/compiler

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/04-compiler-
and-api/01-svelte-compiler.md)

## On this page On this page

Typically, you won't interact with the Svelte compiler directly, but will
instead integrate it into your build system using a bundler plugin. The
bundler plugin that the Svelte team most recommends and invests in is [vite-
plugin-svelte](https://github.com/sveltejs/vite-plugin-svelte). The
[SvelteKit](https://kit.svelte.dev/) framework provides a setup leveraging
`vite-plugin-svelte` to build applications as well as a [tool for packaging
Svelte component libraries](https://kit.svelte.dev/docs/packaging). Svelte
Society maintains a list of [other bundler
plugins](https://sveltesociety.dev/packages?category=bundler-plugins) for
additional tools like Rollup and Webpack.

Nonetheless, it's useful to understand how to use the compiler, since bundler
plugins generally expose compiler options to you.

## compilepermalink

    
    
    ts
    
    function compile(
    
    	source: string,
    
    	options?: CompileOptions
    
    ): CompileResult;

This is where the magic happens. `svelte.compile` takes your component source
code, and turns it into a JavaScript module that exports a class.

    
    
    ts
    
    import { compile } from 'svelte/compiler';
    
    
    
    
    const result = compile(source, {
    
    	// options
    
    });

Refer to CompileOptions for all the available options.

The returned `result` object contains the code for your component, along with
useful bits of metadata.

    
    
    ts
    
    const { js, css, ast, warnings, vars, stats } = compile(source);

Refer to CompileResult for a full description of the compile result.

## parsepermalink

    
    
    ts
    
    function parse(
    
    	template: string,
    
    	options?: ParserOptions
    
    ): Ast;

The `parse` function parses a component, returning only its abstract syntax
tree. Unlike compiling with the `generate: false` option, this will not
perform any validation or other analysis of the component beyond parsing it.
Note that the returned AST is not considered public API, so breaking changes
could occur at any point in time.

    
    
    ts
    
    import { parse } from 'svelte/compiler';
    
    
    
    
    const ast = parse(source, { filename: 'App.svelte' });

## preprocesspermalink

    
    
    ts
    
    function preprocess(
    
    	source: string,
    
    	preprocessor: PreprocessorGroup | PreprocessorGroup[],
    
    	options?:
    
    		| {
    
    				filename?: string | undefined;
    
    		  }
    
    		| undefined
    
    ): Promise<Processed>;

A number of [official and community-maintained preprocessing
plugins](https://sveltesociety.dev/packages?category=preprocessors) are
available to allow you to use Svelte with tools like TypeScript, PostCSS,
SCSS, and Less.

You can write your own preprocessor using the `svelte.preprocess` API.

The `preprocess` function provides convenient hooks for arbitrarily
transforming component source code. For example, it can be used to convert a
`<style lang="sass">` block into vanilla CSS.

The first argument is the component source code. The second is an array of
_preprocessors_ (or a single preprocessor, if you only have one), where a
preprocessor is an object with a `name` which is required, and `markup`,
`script` and `style` functions, each of which is optional.

The `markup` function receives the entire component source text, along with
the component's `filename` if it was specified in the third argument.

The `script` and `style` functions receive the contents of `<script>` and
`<style>` elements respectively (`content`) as well as the entire component
source text (`markup`). In addition to `filename`, they get an object of the
element's attributes.

Each `markup`, `script` or `style` function must return an object (or a
Promise that resolves to an object) with a `code` property, representing the
transformed source code. Optionally they can return an array of `dependencies`
which represents files to watch for changes, and a `map` object which is a
sourcemap mapping back the transformation to the original code. `script` and
`style` preprocessors can optionally return a record of attributes which
represent the updated attributes on the script/style tag.

> Preprocessor functions should return a `map` object whenever possible or
> else debugging becomes harder as stack traces can't link to the original
> code correctly.
    
    
    ts
    
    import { preprocess } from 'svelte/compiler';
    
    import MagicString from 'magic-string';
    
    
    
    
    const { code } = await preprocess(
    
    	source,
    
    	{
    
    		markup: ({ content, filename }) => {
    
    			const pos = content.indexOf('foo');
    
    			if (pos < 0) {
    
    				return { code: content };
    
    			}
    
    			const s = new MagicString(content, { filename });
    
    			s.overwrite(pos, pos + 3, 'bar', { storeName: true });
    
    			return {
    
    				code: s.toString(),
    
    				map: s.generateMap()
    
    			};
    
    		}
    
    	},
    
    	{
    
    		filename: 'App.svelte'
    
    	}
    
    );

If a `dependencies` array is returned, it will be included in the result
object. This is used by packages like [vite-plugin-
svelte](https://github.com/sveltejs/vite-plugin-svelte) and [rollup-plugin-
svelte](https://github.com/sveltejs/rollup-plugin-svelte) to watch additional
files for changes, in the case where your `<style>` tag has an `@import` (for
example).

preprocess-sass.js

    
    
    ts
    
    import { preprocess } from 'svelte/compiler';
    
    import MagicString from 'magic-string';
    
    Type 'string | undefined' is not assignable to type 'string'.
      Type 'undefined' is not assignable to type 'string'.2322Type 'string | undefined' is not assignable to type 'string'.
      Type 'undefined' is not assignable to type 'string'.
    
    import sass from 'sass';
    
    import { dirname } from 'path';
    
    Argument of type 'string | undefined' is not assignable to parameter of type 'string'.
      Type 'undefined' is not assignable to type 'string'.2345Argument of type 'string | undefined' is not assignable to parameter of type 'string'.
      Type 'undefined' is not assignable to type 'string'.
    
    
    
    
    const { code } = await preprocess(
    
    	source,
    
    	{
    
    		name: 'my-fancy-preprocessor',
    
    		markup: ({ content, filename }) => {
    
    			// Return code as is when no foo string present
    
    			const pos = content.indexOf('foo');
    
    			if (pos < 0) {
    
    				return;
    
    			}
    
    
    
    
    			// Replace foo with bar using MagicString which provides
    
    			// a source map along with the changed code
    
    			const s = new MagicString(content, { filename });
    
    			s.overwrite(pos, pos + 3, 'bar', { storeName: true });
    
    
    
    
    			return {
    
    				code: s.toString(),
    
    				map: s.generateMap({ hires: true, file: filename })
    
    			};
    
    		},
    
    		style: async ({ content, attributes, filename }) => {
    
    			// only process <style lang="sass">
    
    			if (attributes.lang !== 'sass') return;
    
    
    
    
    			const { css, stats } = await new Promise((resolve, reject) =>
    
    				sass.render(
    
    					{
    
    						file: filename,
    
    						data: content,
    
    						includePaths: [dirname(filename)]
    
    					},
    
    					(err, result) => {
    
    						if (err) reject(err);
    
    						else resolve(result);
    
    					}
    
    				)
    
    			);
    
    
    
    
    			// remove lang attribute from style tag
    
    			delete attributes.lang;
    
    
    
    
    			return {
    
    				code: css.toString(),
    
    				dependencies: stats.includedFiles,
    
    				attributes
    
    			};
    
    		}
    
    	},
    
    	{
    
    		filename: 'App.svelte'
    
    	}
    
    );

Multiple preprocessors can be used together. The output of the first becomes
the input to the second. Within one preprocessor, `markup` runs first, then
`script` and `style`.

> In Svelte 3, all `markup` functions ran first, then all `script` and then
> all `style` preprocessors. This order was changed in Svelte 4.

multiple-preprocessor.js

    
    
    ts
    
    import { preprocess } from 'svelte/compiler';
    
    
    
    
    const { code } = await preprocess(source, [
    
    	{
    
    		name: 'first preprocessor',
    
    		markup: () => {
    
    			console.log('this runs first');
    
    		},
    
    		script: () => {
    
    			console.log('this runs second');
    
    		},
    
    		style: () => {
    
    			console.log('this runs third');
    
    		}
    
    	},
    
    	{
    
    		name: 'second preprocessor',
    
    		markup: () => {
    
    			console.log('this runs fourth');
    
    		},
    
    		script: () => {
    
    			console.log('this runs fifth');
    
    		},
    
    		style: () => {
    
    			console.log('this runs sixth');
    
    		}
    
    	}
    
    ], {
    
    	filename: 'App.svelte'
    
    });

multiple-preprocessor.ts

    
    
    ts
    
    import { preprocess } from 'svelte/compiler';
    
    
    
    
    const { code } = await preprocess(
    
    	source,
    
    	[
    
    		{
    
    			name: 'first preprocessor',
    
    			markup: () => {
    
    				console.log('this runs first');
    
    			},
    
    			script: () => {
    
    				console.log('this runs second');
    
    			},
    
    			style: () => {
    
    				console.log('this runs third');
    
    			},
    
    		},
    
    		{
    
    			name: 'second preprocessor',
    
    			markup: () => {
    
    				console.log('this runs fourth');
    
    			},
    
    			script: () => {
    
    				console.log('this runs fifth');
    
    			},
    
    			style: () => {
    
    				console.log('this runs sixth');
    
    			},
    
    		},
    
    	],
    
    	{
    
    		filename: 'App.svelte',
    
    	},
    
    );

## walkpermalink

The `walk` function provides a way to walk the abstract syntax trees generated
by the parser, using the compiler's own built-in instance of [estree-
walker](https://github.com/Rich-Harris/estree-walker).

The walker takes an abstract syntax tree to walk and an object with two
optional methods: `enter` and `leave`. For each node, `enter` is called (if
present). Then, unless `this.skip()` is called during `enter`, each of the
children are traversed, and then `leave` is called on the node.

compiler-walk.js

    
    
    ts
    
    import { walk } from 'svelte/compiler';
    
    
    
    
    walk(ast, {
    
    	enter(node, parent, prop, index) {
    
    		do_something(node);
    
    		if (should_skip_children(node)) {
    
    			this.skip();
    
    		}
    
    	},
    
    	leave(node, parent, prop, index) {
    
    		do_something_else(node);
    
    	}
    
    });

compiler-walk.ts

    
    
    ts
    
    import { walk } from 'svelte/compiler';
    
    
    
    
    walk(ast, {
    
    	enter(node, parent, prop, index) {
    
    		do_something(node);
    
    		if (should_skip_children(node)) {
    
    			this.skip();
    
    		}
    
    	},
    
    	leave(node, parent, prop, index) {
    
    		do_something_else(node);
    
    	},
    
    });

## VERSIONpermalink

    
    
    ts
    
    const VERSION: string;

The current version, as set in package.json.

    
    
    ts
    
    import { VERSION } from 'svelte/compiler';
    
    console.log(`running svelte version ${VERSION}`);

## Typespermalink

### CompileOptionspermalink

    
    
    ts
    
    interface CompileOptions {…}
    
    
    ts
    
    name?: string;

  * default `'Component'`

Sets the name of the resulting JavaScript class (though the compiler will
rename it if it would otherwise conflict with other variables in scope). It
will normally be inferred from `filename`

    
    
    ts
    
    filename?: string;

  * default `null`

Used for debugging hints and sourcemaps. Your bundler plugin will set it
automatically.

    
    
    ts
    
    generate?: 'dom' | 'ssr' | false;

  * default `'dom'`

If `"dom"`, Svelte emits a JavaScript class for mounting to the DOM. If
`"ssr"`, Svelte emits an object with a `render` method suitable for server-
side rendering. If `false`, no JavaScript or CSS is returned; just metadata.

    
    
    ts
    
    errorMode?: 'throw' | 'warn';

  * default `'throw'`

If `"throw"`, Svelte throws when a compilation error occurred. If `"warn"`,
Svelte will treat errors as warnings and add them to the warning report.

    
    
    ts
    
    varsReport?: 'full' | 'strict' | false;

  * default `'strict'`

If `"strict"`, Svelte returns a variables report with only variables that are
not globals nor internals. If `"full"`, Svelte returns a variables report with
all detected variables. If `false`, no variables report is returned.

    
    
    ts
    
    sourcemap?: object | string;

  * default `null`

An initial sourcemap that will be merged into the final output sourcemap. This
is usually the preprocessor sourcemap.

    
    
    ts
    
    enableSourcemap?: EnableSourcemap;

  * default `true`

If `true`, Svelte generate sourcemaps for components. Use an object with `js`
or `css` for more granular control of sourcemap generation.

    
    
    ts
    
    outputFilename?: string;

  * default `null`

Used for your JavaScript sourcemap.

    
    
    ts
    
    cssOutputFilename?: string;

  * default `null`

Used for your CSS sourcemap.

    
    
    ts
    
    sveltePath?: string;

  * default `'svelte'`

The location of the `svelte` package. Any imports from `svelte` or
`svelte/[module]` will be modified accordingly.

    
    
    ts
    
    dev?: boolean;

  * default `false`

If `true`, causes extra code to be added to components that will perform
runtime checks and provide debugging information during development.

    
    
    ts
    
    accessors?: boolean;

  * default `false`

If `true`, getters and setters will be created for the component's props. If
`false`, they will only be created for readonly exported values (i.e. those
declared with `const`, `class` and `function`). If compiling with
`customElement: true` this option defaults to `true`.

    
    
    ts
    
    immutable?: boolean;

  * default `false`

If `true`, tells the compiler that you promise not to mutate any objects. This
allows it to be less conservative about checking whether values have changed.

    
    
    ts
    
    hydratable?: boolean;

  * default `false`

If `true` when generating DOM code, enables the `hydrate: true` runtime
option, which allows a component to upgrade existing DOM rather than creating
new DOM from scratch. When generating SSR code, this adds markers to `<head>`
elements so that hydration knows which to replace.

    
    
    ts
    
    legacy?: boolean;

  * default `false`

If `true`, generates code that will work in IE9 and IE10, which don't support
things like `element.dataset`.

    
    
    ts
    
    customElement?: boolean;

  * default `false`

If `true`, tells the compiler to generate a custom element constructor instead
of a regular Svelte component.

    
    
    ts
    
    tag?: string;

  * default `null`

A `string` that tells Svelte what tag name to register the custom element
with. It must be a lowercase alphanumeric string with at least one hyphen,
e.g. `"my-element"`.

    
    
    ts
    
    css?: 'injected' | 'external' | 'none' | boolean;

  * `'injected'` (formerly `true`), styles will be included in the JavaScript class and injected at runtime for the components actually rendered.
  * `'external'` (formerly `false`), the CSS will be returned in the `css` field of the compilation result. Most Svelte bundler plugins will set this to `'external'` and use the CSS that is statically generated for better performance, as it will result in smaller JavaScript bundles and the output can be served as cacheable `.css` files.
  * `'none'`, styles are completely avoided and no CSS output is generated.

    
    
    ts
    
    loopGuardTimeout?: number;

  * default `0`

A `number` that tells Svelte to break the loop if it blocks the thread for
more than `loopGuardTimeout` ms. This is useful to prevent infinite loops.
**Only available when`dev: true`**.

    
    
    ts
    
    namespace?: string;

  * default `'html'`

The namespace of the element; e.g., `"mathml"`, `"svg"`, `"foreign"`.

    
    
    ts
    
    cssHash?: CssHashGetter;

  * default `undefined`

A function that takes a `{ hash, css, name, filename }` argument and returns
the string that is used as a classname for scoped CSS. It defaults to
returning `svelte-${hash(css)}`.

    
    
    ts
    
    preserveComments?: boolean;

  * default `false`

If `true`, your HTML comments will be preserved during server-side rendering.
By default, they are stripped out.

    
    
    ts
    
    preserveWhitespace?: boolean;

  * default `false`

If `true`, whitespace inside and between elements is kept as you typed it,
rather than removed or collapsed to a single space where possible.

    
    
    ts
    
    discloseVersion?: boolean;

  * default `true`

If `true`, exposes the Svelte major version in the browser by adding it to a
`Set` stored in the global `window.__svelte.v`.

### CompileResultpermalink

The returned shape of `compile` from `svelte/compiler`

    
    
    ts
    
    interface CompileResult {…}
    
    
    ts
    
    js: {…}

The resulting JavaScript code from compling the component

    
    
    ts
    
    code: string;

Code as a string

    
    
    ts
    
    map: any;

A source map

    
    
    ts
    
    css: CssResult;

The resulting CSS code from compling the component

    
    
    ts
    
    ast: Ast;

The abstract syntax tree representing the structure of the component

    
    
    ts
    
    warnings: Warning[];

An array of warning objects that were generated during compilation. Each
warning has several properties:

  * code is a string identifying the category of warning
  * message describes the issue in human-readable terms
  * start and end, if the warning relates to a specific location, are objects with line, column and character properties
  * frame, if applicable, is a string highlighting the offending code with line numbers

    
    
    ts
    
    vars: Var[];

An array of the component's declarations used by tooling in the ecosystem
(like our ESLint plugin) to infer more information

    
    
    ts
    
    stats: {
    
    	timings: {
    
    		total: number;
    
    	};
    
    };

An object used by the Svelte developer team for diagnosing the compiler. Avoid
relying on it to stay the same!

### CssHashGetterpermalink

    
    
    ts
    
    type CssHashGetter = (args: {
    
    	name: string;
    
    	filename: string | undefined;
    
    	css: string;
    
    	hash: (input: string) => string;
    
    }) => string;

### EnableSourcemappermalink

    
    
    ts
    
    type EnableSourcemap =
    
    	| boolean
    
    	| { js: boolean; css: boolean };

### MarkupPreprocessorpermalink

A markup preprocessor that takes a string of code and returns a processed
version.

    
    
    ts
    
    type MarkupPreprocessor = (options: {
    
    	/**
    
    	 * The whole Svelte file content
    
    	 */
    
    	content: string;
    
    	/**
    
    	 * The filename of the Svelte file
    
    	 */
    
    	filename?: string;
    
    }) => Processed | void | Promise<Processed | void>;

### Preprocessorpermalink

A script/style preprocessor that takes a string of code and returns a
processed version.

    
    
    ts
    
    type Preprocessor = (options: {
    
    	/**
    
    	 * The script/style tag content
    
    	 */
    
    	content: string;
    
    	/**
    
    	 * The attributes on the script/style tag
    
    	 */
    
    	attributes: Record<string, string | boolean>;
    
    	/**
    
    	 * The whole Svelte file content
    
    	 */
    
    	markup: string;
    
    	/**
    
    	 * The filename of the Svelte file
    
    	 */
    
    	filename?: string;
    
    }) => Processed | void | Promise<Processed | void>;

### PreprocessorGrouppermalink

A preprocessor group is a set of preprocessors that are applied to a Svelte
file.

    
    
    ts
    
    interface PreprocessorGroup {…}
    
    
    ts
    
    name?: string;

Name of the preprocessor. Will be a required option in the next major version

    
    
    ts
    
    markup?: MarkupPreprocessor;
    
    
    ts
    
    style?: Preprocessor;
    
    
    ts
    
    script?: Preprocessor;

### Processedpermalink

The result of a preprocessor run. If the preprocessor does not return a
result, it is assumed that the code is unchanged.

    
    
    ts
    
    interface Processed {…}
    
    
    ts
    
    code: string;

The new code

    
    
    ts
    
    map?: string | object;

A source map mapping back to the original code

    
    
    ts
    
    dependencies?: string[];

A list of additional files to watch for changes

    
    
    ts
    
    attributes?: Record<string, string | boolean>;

Only for script/style preprocessors: The updated attributes to set on the tag.
If undefined, attributes stay unchanged.

    
    
    ts
    
    toString?: () => string;

### SveltePreprocessorpermalink

Utility type to extract the type of a preprocessor from a preprocessor group

    
    
    ts
    
    interface SveltePreprocessor<
    
    	PreprocessorType extends keyof PreprocessorGroup,
    
    	Options = any
    
    > {…}
    
    
    ts
    
    (options?: Options): Required<Pick<PreprocessorGroup, PreprocessorType>>;

previous [svelte/action](/docs/svelte-action)

next [Client-side component API](/docs/client-side-component-api)



Compiler and API

# Client-side component API

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/04-compiler-
and-api/02-client-side-component-api.md)

## On this page On this page

## Creating a componentpermalink

    
    
    ts
    
    const component = new Component(options);

A client-side component — that is, a component compiled with `generate: 'dom'`
(or the `generate` option left unspecified) is a JavaScript class.

    
    
    ts
    
    import App from './App.svelte';
    
    
    
    
    const app = new App({
    
    	target: document.body,
    
    	props: {
    
    		// assuming App.svelte contains something like
    
    		// `export let answer`:
    
    		answer: 42
    
    	}
    
    });

The following initialisation options can be provided:

option | default | description  
---|---|---  
`target` | **none** | An `HTMLElement` or `ShadowRoot` to render to. This option is required  
`anchor` | `null` | A child of `target` to render the component immediately before  
`props` | `{}` | An object of properties to supply to the component  
`context` | `new Map()` | A `Map` of root-level context key-value pairs to supply to the component  
`hydrate` | `false` | See below  
`intro` | `false` | If `true`, will play transitions on initial render, rather than waiting for subsequent state changes  
  
Existing children of `target` are left where they are.

The `hydrate` option instructs Svelte to upgrade existing DOM (usually from
server-side rendering) rather than creating new elements. It will only work if
the component was compiled with the [`hydratable: true` option](/docs/svelte-
compiler#compile). Hydration of `<head>` elements only works properly if the
server-side rendering code was also compiled with `hydratable: true`, which
adds a marker to each element in the `<head>` so that the component knows
which elements it's responsible for removing during hydration.

Whereas children of `target` are normally left alone, `hydrate: true` will
cause any children to be removed. For that reason, the `anchor` option cannot
be used alongside `hydrate: true`.

The existing DOM doesn't need to match the component — Svelte will 'repair'
the DOM as it goes.

index.js

    
    
    ts
    
    import App from './App.svelte';
    
    
    
    
    const app = new App({
    
    	target: document.querySelector('#server-rendered-html'),
    
    	hydrate: true
    
    });

## $setpermalink

    
    
    ts
    
    component.$set(props);

Programmatically sets props on an instance. `component.$set({ x: 1 })` is
equivalent to `x = 1` inside the component's `<script>` block.

Calling this method schedules an update for the next microtask — the DOM is
_not_ updated synchronously.

    
    
    ts
    
    component.$set({ answer: 42 });

## $onpermalink

    
    
    ts
    
    component.$on(ev, callback);

Causes the `callback` function to be called whenever the component dispatches
an `event`.

A function is returned that will remove the event listener when called.

index.js

    
    
    ts
    
    const off = component.$on('selected', (event) => {
    
    	console.log(event.detail.selection);
    
    });
    
    
    
    
    off();

## $destroypermalink

    
    
    ts
    
    component.$destroy();

Removes a component from the DOM and triggers any `onDestroy` handlers.

## Component propspermalink

    
    
    ts
    
    component.prop;
    
    
    ts
    
    component.prop = value;

If a component is compiled with `accessors: true`, each instance will have
getters and setters corresponding to each of the component's props. Setting a
value will cause a _synchronous_ update, rather than the default async update
caused by `component.$set(...)`.

By default, `accessors` is `false`, unless you're compiling as a custom
element.

index.js

    
    
    ts
    
    console.log(component.count);
    
    component.count += 1;

index.ts

    
    
    ts
    
    console.log(component.count);
    
    component.count += 1;

previous [svelte/compiler](/docs/svelte-compiler)

next [Server-side component API](/docs/server-side-component-api)



Compiler and API

# Server-side component API

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/04-compiler-
and-api/03-server-side-component-api.md)

## On this page On this page

    
    
    ts
    
    const result = Component.render(...)

Unlike client-side components, server-side components don't have a lifespan
after you render them — their whole job is to create some HTML and CSS. For
that reason, the API is somewhat different.

A server-side component exposes a `render` method that can be called with
optional props. It returns an object with `head`, `html`, and `css`
properties, where `head` contains the contents of any `<svelte:head>` elements
encountered.

You can import a Svelte component directly into Node using
[`svelte/register`](/docs/svelte-register).

    
    
    ts
    
    require('svelte/register');
    
    
    
    
    const App = require('./App.svelte').default;
    
    
    
    
    const { head, html, css } = App.render({
    
    	answer: 42
    
    });

The `.render()` method accepts the following parameters:

parameter | default | description  
---|---|---  
`props` | `{}` | An object of properties to supply to the component  
`options` | `{}` | An object of options  
  
The `options` object takes in the following options:

option | default | description  
---|---|---  
`context` | `new Map()` | A `Map` of root-level context key-value pairs to supply to the component  
      
    
    ts
    
    const { head, html, css } = App.render(
    
    	// props
    
    	{ answer: 42 },
    
    	// options
    
    	{
    
    		context: new Map([['context-key', 'context-value']])
    
    	}
    
    );

previous [Client-side component API](/docs/client-side-component-api)

next [Custom elements API](/docs/custom-elements-api)



Compiler and API

# Custom elements API

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/04-compiler-
and-api/04-custom-elements-api.md)

## On this page On this page

Svelte components can also be compiled to custom elements (aka web components)
using the `customElement: true` compiler option. You should specify a tag name
for the component using the `<svelte:options>` [element](/docs/special-
elements#svelte-options).

    
    
    <svelte:options customElement="my-element" />
    
    <!-- in Svelte 3, do this instead:
    <svelte:options tag="my-element" />
    -->
    
    <script>
    	export let name = 'world';
    </script>
    
    <h1>Hello {name}!</h1>
    <slot />

You can leave out the tag name for any of your inner components which you
don't want to expose and use them like regular Svelte components. Consumers of
the component can still name it afterwards if needed, using the static
`element` property which contains the custom element constructor and which is
available when the `customElement` compiler option is `true`.

    
    
    ts
    
    import MyElement from './MyElement.svelte';
    
    
    
    
    customElements.define('my-element', MyElement.element);
    
    // In Svelte 3, do this instead:
    
    // customElements.define('my-element', MyElement);

Once a custom element has been defined, it can be used as a regular DOM
element:

    
    
    ts
    
    document.body.innerHTML = `
    
    	<my-element>
    
    		<p>This is some slotted content</p>
    
    	</my-element>
    
    `;

By default, custom elements are compiled with `accessors: true`, which means
that any [props](/docs/basic-markup#attributes-and-props) are exposed as
properties of the DOM element (as well as being readable/writable as
attributes, where possible).

To prevent this, add `accessors={false}` to `<svelte:options>`.

    
    
    ts
    
    const el = document.querySelector('my-element');
    
    
    
    
    // get the current value of the 'name' prop
    
    console.log(el.name);
    
    
    
    
    // set a new value, updating the shadow DOM
    
    el.name = 'everybody';

## Component lifecyclepermalink

Custom elements are created from Svelte components using a wrapper approach.
This means the inner Svelte component has no knowledge that it is a custom
element. The custom element wrapper takes care of handling its lifecycle
appropriately.

When a custom element is created, the Svelte component it wraps is _not_
created right away. It is only created in the next tick after the
`connectedCallback` is invoked. Properties assigned to the custom element
before it is inserted into the DOM are temporarily saved and then set on
component creation, so their values are not lost. The same does not work for
invoking exported functions on the custom element though, they are only
available after the element has mounted. If you need to invoke functions
before component creation, you can work around it by using the `extend`
option.

When a custom element written with Svelte is created or updated, the shadow
DOM will reflect the value in the next tick, not immediately. This way updates
can be batched, and DOM moves which temporarily (but synchronously) detach the
element from the DOM don't lead to unmounting the inner component.

The inner Svelte component is destroyed in the next tick after the
`disconnectedCallback` is invoked.

## Component optionspermalink

When constructing a custom element, you can tailor several aspects by defining
`customElement` as an object within `<svelte:options>` since Svelte 4. This
object may contain the following properties:

  * `tag`: the mandatory `tag` property for the custom element's name
  * `shadow`: an optional property that can be set to `"none"` to forgo shadow root creation. Note that styles are then no longer encapsulated, and you can't use slots
  * `props`: an optional property to modify certain details and behaviors of your component's properties. It offers the following settings:
    * `attribute: string`: To update a custom element's prop, you have two alternatives: either set the property on the custom element's reference as illustrated above or use an HTML attribute. For the latter, the default attribute name is the lowercase property name. Modify this by assigning `attribute: "<desired name>"`.
    * `reflect: boolean`: By default, updated prop values do not reflect back to the DOM. To enable this behavior, set `reflect: true`.
    * `type: 'String' | 'Boolean' | 'Number' | 'Array' | 'Object'`: While converting an attribute value to a prop value and reflecting it back, the prop value is assumed to be a `String` by default. This may not always be accurate. For instance, for a number type, define it using `type: "Number"` You don't need to list all properties, those not listed will use the default settings.
  * `extend`: an optional property which expects a function as its argument. It is passed the custom element class generated by Svelte and expects you to return a custom element class. This comes in handy if you have very specific requirements to the life cycle of the custom element or want to enhance the class to for example use [ElementInternals](https://developer.mozilla.org/en-US/docs/Web/API/ElementInternals#examples) for better HTML form integration.

    
    
    <svelte:options
    	customElement={{
    		tag: 'custom-element',
    		shadow: 'none',
    		props: {
    			name: { reflect: true, type: 'Number', attribute: 'element-index' }
    		},
    		extend: (customElementConstructor) => {
    			// Extend the class so we can let it participate in HTML forms
    			return class extends customElementConstructor {
    				static formAssociated = true;
    
    				constructor() {
    					super();
    					this.attachedInternals = this.attachInternals();
    				}
    
    				// Add the function here, not below in the component so that
    				// it's always available, not just when the inner Svelte component
    				// is mounted
    				randomIndex() {
    					this.elementIndex = Math.random();
    				}
    			};
    		}
    	}}
    />
    
    <script>
    	export let elementIndex;
    	export let attachedInternals;
    	// ...
    	function check() {
    		attachedInternals.checkValidity();
    	}
    </script>
    
    ...

## Caveats and limitationspermalink

Custom elements can be a useful way to package components for consumption in a
non-Svelte app, as they will work with vanilla HTML and JavaScript as well as
[most frameworks](https://custom-elements-everywhere.com/). There are,
however, some important differences to be aware of:

  * Styles are _encapsulated_ , rather than merely _scoped_ (unless you set `shadow: "none"`). This means that any non-component styles (such as you might have in a `global.css` file) will not apply to the custom element, including styles with the `:global(...)` modifier
  * Instead of being extracted out as a separate .css file, styles are inlined into the component as a JavaScript string
  * Custom elements are not generally suitable for server-side rendering, as the shadow DOM is invisible until JavaScript loads
  * In Svelte, slotted content renders _lazily_. In the DOM, it renders _eagerly_. In other words, it will always be created even if the component's `<slot>` element is inside an `{#if ...}` block. Similarly, including a `<slot>` in an `{#each ...}` block will not cause the slotted content to be rendered multiple times
  * The `let:` directive has no effect, because custom elements do not have a way to pass data to the parent component that fills the slot
  * Polyfills are required to support older browsers
  * You can use Svelte's context feature between regular Svelte components within a custom element, but you can't use them across custom elements. In other words, you can't use `setContext` on a parent custom element and read that with `getContext` in a child custom element.

previous [Server-side component API](/docs/server-side-component-api)

next [Frequently asked questions](/docs/faq)



Misc

# Frequently asked questions

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/05-misc/01-faq.md)

## On this page On this page

## I'm new to Svelte. Where should I start?permalink

We think the best way to get started is playing through the interactive
[tutorial](https://learn.svelte.dev/). Each step there is mainly focused on
one specific aspect and is easy to follow. You'll be editing and running real
Svelte components right in your browser.

Five to ten minutes should be enough to get you up and running. An hour and a
half should get you through the entire tutorial.

## Where can I get support?permalink

If your question is about certain syntax, the [API
page](https://svelte.dev/docs) is a good place to start.

Stack Overflow is a popular forum to ask code-level questions or if you’re
stuck with a specific error. Read through the existing questions tagged with
[Svelte](https://stackoverflow.com/questions/tagged/svelte+or+svelte-3) or
[ask your own](https://stackoverflow.com/questions/ask?tags=svelte)!

There are online forums and chats which are a great place for discussion about
best practices, application architecture or just to get to know fellow Svelte
users. [Our Discord](https://svelte.dev/chat) or [the Reddit
channel](https://www.reddit.com/r/sveltejs/) are examples of that. If you have
an answerable code-level question, Stack Overflow is usually a better fit.

## Are there any third-party resources?permalink

Svelte Society maintains a [list of books and
videos](https://sveltesociety.dev/resources).

## How can I get VS Code to syntax-highlight my .svelte files?permalink

There is an [official VS Code extension for
Svelte](https://marketplace.visualstudio.com/items?itemName=svelte.svelte-
vscode).

## Is there a tool to automatically format my .svelte files?permalink

You can use prettier with the [prettier-plugin-
svelte](https://www.npmjs.com/package/prettier-plugin-svelte) plugin.

## How do I document my components?permalink

In editors which use the Svelte Language Server you can document Components,
functions and exports using specially formatted comments.

    
    
    <script>
    	/** What should we call the user? */
    	export let name = 'world';
    </script>
    
    <!--
    @component
    Here's some documentation for this component.
    It will show up on hover.
    
    - You can use markdown here.
    - You can also use code blocks here.
    - Usage:
      ```tsx
      <main name="Arethra">
      ```
    -->
    <main>
    	<h1>
    		Hello, {name}
    	</h1>
    </main>

Note: The `@component` is necessary in the HTML comment which describes your
component.

## Does Svelte scale?permalink

There will be a blog post about this eventually, but in the meantime, check
out [this issue](https://github.com/sveltejs/svelte/issues/2546).

## Is there a UI component library?permalink

There are several UI component libraries as well as standalone components.
Find them under the [design systems section of the components
page](https://sveltesociety.dev/packages?category=design-system) on the Svelte
Society website.

## How do I test Svelte apps?permalink

How your application is structured and where logic is defined will determine
the best way to ensure it is properly tested. It is important to note that not
all logic belongs within a component - this includes concerns such as data
transformation, cross-component state management, and logging, among others.
Remember that the Svelte library has its own test suite, so you do not need to
write tests to validate implementation details provided by Svelte.

A Svelte application will typically have three different types of tests: Unit,
Component, and End-to-End (E2E).

_Unit Tests_ : Focus on testing business logic in isolation. Often this is
validating individual functions and edge cases. By minimizing the surface area
of these tests they can be kept lean and fast, and by extracting as much logic
as possible from your Svelte components more of your application can be
covered using them. When creating a new SvelteKit project, you will be asked
whether you would like to setup [Vitest](https://vitest.dev/) for unit
testing. There are a number of other test runners that could be used as well.

_Component Tests_ : Validating that a Svelte component mounts and interacts as
expected throughout its lifecycle requires a tool that provides a Document
Object Model (DOM). Components can be compiled (since Svelte is a compiler and
not a normal library) and mounted to allow asserting against element
structure, listeners, state, and all the other capabilities provided by a
Svelte component. Tools for component testing range from an in-memory
implementation like jsdom paired with a test runner like
[Vitest](https://vitest.dev/) to solutions that leverage an actual browser to
provide a visual testing capability such as
[Playwright](https://playwright.dev/docs/test-components) or
[Cypress](https://www.cypress.io/).

_End-to-End Tests_ : To ensure your users are able to interact with your
application it is necessary to test it as a whole in a manner as close to
production as possible. This is done by writing end-to-end (E2E) tests which
load and interact with a deployed version of your application in order to
simulate how the user will interact with your application. When creating a new
SvelteKit project, you will be asked whether you would like to setup
[Playwright](https://playwright.dev/) for end-to-end testing. There are many
other E2E test libraries available for use as well.

Some resources for getting started with testing:

  * [Svelte Testing Library](https://testing-library.com/docs/svelte-testing-library/example/)
  * [Svelte Component Testing in Cypress](https://docs.cypress.io/guides/component-testing/svelte/overview)
  * [Example using vitest](https://github.com/vitest-dev/vitest/tree/main/examples/svelte)
  * [Example using uvu test runner with JSDOM](https://github.com/lukeed/uvu/tree/master/examples/svelte)
  * [Test Svelte components using Vitest & Playwright](https://davipon.hashnode.dev/test-svelte-component-using-vitest-playwright)
  * [Component testing with WebdriverIO](https://webdriver.io/docs/component-testing/svelte)

## Is there a router?permalink

The official routing library is [SvelteKit](https://kit.svelte.dev/).
SvelteKit provides a filesystem router, server-side rendering (SSR), and hot
module reloading (HMR) in one easy-to-use package. It shares similarities with
Next.js for React.

However, you can use any router library. A lot of people use
[page.js](https://github.com/visionmedia/page.js). There's also
[navaid](https://github.com/lukeed/navaid), which is very similar. And
[universal-router](https://github.com/kriasoft/universal-router), which is
isomorphic with child routes, but without built-in history support.

If you prefer a declarative HTML approach, there's the isomorphic [svelte-
routing](https://github.com/EmilTholin/svelte-routing) library and a fork of
it called [svelte-navigator](https://github.com/mefechoel/svelte-navigator)
containing some additional functionality.

If you need hash-based routing on the client side, check out [svelte-spa-
router](https://github.com/ItalyPaleAle/svelte-spa-router) or [abstract-state-
router](https://github.com/TehShrike/abstract-state-router/).

[Routify](https://routify.dev) is another filesystem-based router, similar to
SvelteKit's router. Version 3 supports Svelte's native SSR.

You can see a [community-maintained list of routers on
sveltesociety.dev](https://sveltesociety.dev/packages?category=routers).

## Can I tell Svelte not to remove my unused styles?permalink

No. Svelte removes the styles from the component and warns you about them in
order to prevent issues that would otherwise arise.

Svelte's component style scoping works by generating a class unique to the
given component, adding it to the relevant elements in the component that are
under Svelte's control, and then adding it to each of the selectors in that
component's styles. When the compiler can't see what elements a style selector
applies to, there would be two bad options for keeping it:

  * If it keeps the selector and adds the scoping class to it, the selector will likely not match the expected elements in the component, and they definitely won't if they were created by a child component or `{@html ...}`.
  * If it keeps the selector without adding the scoping class to it, the given style will become a global style, affecting your entire page.

If you need to style something that Svelte can't identify at compile time, you
will need to explicitly opt into global styles by using `:global(...)`. But
also keep in mind that you can wrap `:global(...)` around only part of a
selector. `.foo :global(.bar) { ... }` will style any `.bar` elements that
appear within the component's `.foo` elements. As long as there's some parent
element in the current component to start from, partially global selectors
like this will almost always be able to get you what you want.

## Is Svelte v2 still available?permalink

New features aren't being added to it, and bugs will probably only be fixed if
they are extremely nasty or present some sort of security vulnerability.

The documentation is still available [here](https://v2.svelte.dev/guide).

## How do I do hot module reloading?permalink

We recommend using [SvelteKit](https://kit.svelte.dev/), which supports HMR
out of the box and is built on top of [Vite](https://vitejs.dev/) and [svelte-
hmr](https://github.com/sveltejs/svelte-hmr). There are also community plugins
for [rollup](https://github.com/rixo/rollup-plugin-svelte-hot) and
[webpack](https://github.com/sveltejs/svelte-loader).

previous [Custom elements API](/docs/custom-elements-api)

next [Accessibility warnings](/docs/accessibility-warnings)



Misc

# Accessibility warnings

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/05-misc/02-accessibility-
warnings.md)

## On this page On this page

Accessibility (shortened to a11y) isn't always easy to get right, but Svelte
will help by warning you at compile time if you write inaccessible markup.
However, keep in mind that many accessibility issues can only be identified at
runtime using other automated tools and by manually testing your application.

Some warnings may be incorrect in your concrete use case. You can disable such
false positives by placing a `<!-- svelte-ignore a11y-<code> -->` comment
above the line that causes the warning. Example:

    
    
    <!-- svelte-ignore a11y-autofocus -->
    <input autofocus />

Here is a list of accessibility checks Svelte will do for you.

## a11y-accesskeypermalink

Enforce no `accesskey` on element. Access keys are HTML attributes that allow
web developers to assign keyboard shortcuts to elements. Inconsistencies
between keyboard shortcuts and keyboard commands used by screen reader and
keyboard-only users create accessibility complications. To avoid
complications, access keys should not be used.

    
    
    <!-- A11y: Avoid using accesskey -->
    <div accessKey="z" />

## a11y-aria-activedescendant-has-tabindexpermalink

An element with `aria-activedescendant` must be tabbable, so it must either
have an inherent `tabindex` or declare `tabindex` as an attribute.

    
    
    <!-- A11y: Elements with attribute aria-activedescendant should have tabindex value -->
    <div aria-activedescendant="some-id" />

## a11y-aria-attributespermalink

Certain reserved DOM elements do not support ARIA roles, states and
properties. This is often because they are not visible, for example `meta`,
`html`, `script`, `style`. This rule enforces that these DOM elements do not
contain the `aria-*` props.

    
    
    <!-- A11y: <meta> should not have aria-* attributes -->
    <meta aria-hidden="false" />

## a11y-autofocuspermalink

Enforce that `autofocus` is not used on elements. Autofocusing elements can
cause usability issues for sighted and non-sighted users alike.

    
    
    <!-- A11y: Avoid using autofocus -->
    <input autofocus />

## a11y-click-events-have-key-eventspermalink

Enforce that visible, non-interactive elements with an `on:click` event are
accompanied by a keyboard event handler.

Users should first consider whether an interactive element might be more
appropriate such as a `<button type="button">` element for actions or `<a>`
element for navigations. These elements are more semantically meaningful and
will have built-in key handling. E.g. `Space` and `Enter` will trigger a
`<button>` and `Enter` will trigger an `<a>` element.

If a non-interactive element is required then `on:click` should be accompanied
by an `on:keyup` or `on:keydown` handler that enables the user to perform
equivalent actions via the keyboard. In order for the user to be able to
trigger a key press, the element will also need to be focusable by adding a
[`tabindex`](https://developer.mozilla.org/en-
US/docs/Web/HTML/Global_attributes/tabindex). While an `on:keypress` handler
will also silence this warning, it should be noted that the `keypress` event
is deprecated.

    
    
    <!-- A11y: visible, non-interactive elements with an on:click event must be accompanied by a keyboard event handler. -->
    <div on:click={() => {}} />

Coding for the keyboard is important for users with physical disabilities who
cannot use a mouse, AT compatibility, and screenreader users.

## a11y-distracting-elementspermalink

Enforces that no distracting elements are used. Elements that can be visually
distracting can cause accessibility issues with visually impaired users. Such
elements are most likely deprecated, and should be avoided.

The following elements are visually distracting: `<marquee>` and `<blink>`.

    
    
    <!-- A11y: Avoid <marquee> elements -->
    <marquee />

## a11y-hiddenpermalink

Certain DOM elements are useful for screen reader navigation and should not be
hidden.

    
    
    <!-- A11y: <h2> element should not be hidden -->
    <h2 aria-hidden="true">invisible header</h2>

## a11y-img-redundant-altpermalink

Enforce img alt attribute does not contain the word image, picture, or photo.
Screen readers already announce `img` elements as an image. There is no need
to use words such as _image_ , _photo_ , and/or _picture_.

    
    
    <img src="foo" alt="Foo eating a sandwich." />
    
    <!-- aria-hidden, won't be announced by screen reader -->
    <img src="bar" aria-hidden="true" alt="Picture of me taking a photo of an image" />
    
    <!-- A11y: Screen readers already announce <img> elements as an image. -->
    <img src="foo" alt="Photo of foo being weird." />
    
    <!-- A11y: Screen readers already announce <img> elements as an image. -->
    <img src="bar" alt="Image of me at a bar!" />
    
    <!-- A11y: Screen readers already announce <img> elements as an image. -->
    <img src="foo" alt="Picture of baz fixing a bug." />

## a11y-incorrect-aria-attribute-typepermalink

Enforce that only the correct type of value is used for aria attributes. For
example, `aria-hidden` should only receive a boolean.

    
    
    <!-- A11y: The value of 'aria-hidden' must be exactly one of true or false -->
    <div aria-hidden="yes" />

## a11y-invalid-attributepermalink

Enforce that attributes important for accessibility have a valid value. For
example, `href` should not be empty, `'#'`, or `javascript:`.

    
    
    <!-- A11y: '' is not a valid href attribute -->
    <a href="">invalid</a>

## a11y-interactive-supports-focuspermalink

Enforce that elements with an interactive role and interactive handlers (mouse
or key press) must be focusable or tabbable.

    
    
    <!-- A11y: Elements with the 'button' interactive role must have a tabindex value. -->
    <div role="button" on:keypress={() => {}} />

## a11y-label-has-associated-controlpermalink

Enforce that a label tag has a text label and an associated control.

There are two supported ways to associate a label with a control:

  * Wrapping a control in a label tag.
  * Adding `for` to a label and assigning it the ID of an input on the page.

    
    
    <label for="id">B</label>
    
    <label>C <input type="text" /></label>
    
    <!-- A11y: A form label must be associated with a control. -->
    <label>A</label>

## a11y-media-has-captionpermalink

Providing captions for media is essential for deaf users to follow along.
Captions should be a transcription or translation of the dialogue, sound
effects, relevant musical cues, and other relevant audio information. Not only
is this important for accessibility, but can also be useful for all users in
the case that the media is unavailable (similar to `alt` text on an image when
an image is unable to load).

The captions should contain all important and relevant information to
understand the corresponding media. This may mean that the captions are not a
1:1 mapping of the dialogue in the media content. However, captions are not
necessary for video components with the `muted` attribute.

    
    
    <video><track kind="captions" /></video>
    
    <audio muted />
    
    <!-- A11y: Media elements must have a <track kind=\"captions\"> -->
    <video />
    
    <!-- A11y: Media elements must have a <track kind=\"captions\"> -->
    <video><track /></video>

## a11y-misplaced-rolepermalink

Certain reserved DOM elements do not support ARIA roles, states and
properties. This is often because they are not visible, for example `meta`,
`html`, `script`, `style`. This rule enforces that these DOM elements do not
contain the `role` props.

    
    
    <!-- A11y: <meta> should not have role attribute -->
    <meta role="tooltip" />

## a11y-misplaced-scopepermalink

The scope attribute should only be used on `<th>` elements.

    
    
    <!-- A11y: The scope attribute should only be used with <th> elements -->
    <div scope="row" />

## a11y-missing-attributepermalink

Enforce that attributes required for accessibility are present on an element.
This includes the following checks:

  * `<a>` should have an href (unless it's a [fragment-defining tag](https://github.com/sveltejs/svelte/issues/4697))
  * `<area>` should have alt, aria-label, or aria-labelledby
  * `<html>` should have lang
  * `<iframe>` should have title
  * `<img>` should have alt
  * `<object>` should have title, aria-label, or aria-labelledby
  * `<input type="image">` should have alt, aria-label, or aria-labelledby

    
    
    <!-- A11y: <input type=\"image\"> element should have an alt, aria-label or aria-labelledby attribute -->
    <input type="image" />
    
    <!-- A11y: <html> element should have a lang attribute -->
    <html />
    
    <!-- A11y: <a> element should have an href attribute -->
    <a>text</a>

## a11y-missing-contentpermalink

Enforce that heading elements (`h1`, `h2`, etc.) and anchors have content and
that the content is accessible to screen readers

    
    
    <!-- A11y: <a> element should have child content -->
    <a href="/foo" />
    
    <!-- A11y: <h1> element should have child content -->
    <h1 />

## a11y-mouse-events-have-key-eventspermalink

Enforce that `on:mouseover` and `on:mouseout` are accompanied by `on:focus`
and `on:blur`, respectively. This helps to ensure that any functionality
triggered by these mouse events is also accessible to keyboard users.

    
    
    <!-- A11y: on:mouseover must be accompanied by on:focus -->
    <div on:mouseover={handleMouseover} />
    
    <!-- A11y: on:mouseout must be accompanied by on:blur -->
    <div on:mouseout={handleMouseout} />

## a11y-no-redundant-rolespermalink

Some HTML elements have default ARIA roles. Giving these elements an ARIA role
that is already set by the browser [has no
effect](https://www.w3.org/TR/using-aria/#aria-does-nothing) and is redundant.

    
    
    <!-- A11y: Redundant role 'button' -->
    <button role="button" />
    
    <!-- A11y: Redundant role 'img' -->
    <img role="img" src="foo.jpg" />

## a11y-no-interactive-element-to-noninteractive-rolepermalink

[WAI-ARIA](https://www.w3.org/TR/wai-aria-1.1/#usage_intro) roles should not
be used to convert an interactive element to a non-interactive element. Non-
interactive ARIA roles include `article`, `banner`, `complementary`, `img`,
`listitem`, `main`, `region` and `tooltip`.

    
    
    <!-- A11y: <textarea> cannot have role 'listitem' -->
    <textarea role="listitem" />

### a11y-no-noninteractive-element-interactionspermalink

A non-interactive element does not support event handlers (mouse and key
handlers). Non-interactive elements include `<main>`, `<area>`, `<h1>`
(,`<h2>`, etc), `<p>`, `<img>`, `<li>`, `<ul>` and `<ol>`. Non-interactive
[WAI-ARIA roles](https://www.w3.org/TR/wai-aria-1.1/#usage_intro) include
`article`, `banner`, `complementary`, `img`, `listitem`, `main`, `region` and
`tooltip`.

    
    
    <!-- `A11y: Non-interactive element <li> should not be assigned mouse or keyboard event listeners.` -->
    <li on:click={() => {}} />
    
    <!-- `A11y: Non-interactive element <div> should not be assigned mouse or keyboard event listeners.` -->
    <div role="listitem" on:click={() => {}} />

### a11y-no-noninteractive-element-to-interactive-rolepermalink

[WAI-ARIA](https://www.w3.org/TR/wai-aria-1.1/#usage_intro) roles should not
be used to convert a non-interactive element to an interactive element.
Interactive ARIA roles include `button`, `link`, `checkbox`, `menuitem`,
`menuitemcheckbox`, `menuitemradio`, `option`, `radio`, `searchbox`, `switch`
and `textbox`.

    
    
    <!-- A11y: Non-interactive element <h3> cannot have interactive role 'searchbox' -->
    <h3 role="searchbox">Button</h3>

## a11y-no-noninteractive-tabindexpermalink

Tab key navigation should be limited to elements on the page that can be
interacted with.

    
    
    <!-- A11y: noninteractive element cannot have nonnegative tabIndex value -->
    <div tabindex="0" />

## a11y-no-static-element-interactionspermalink

Elements like `<div>` with interactive handlers like `click` must have an ARIA
role.

    
    
    <!-- A11y: <div> with click handler must have an ARIA role -->
    <div on:click={() => ''} />

## a11y-positive-tabindexpermalink

Avoid positive `tabindex` property values. This will move elements out of the
expected tab order, creating a confusing experience for keyboard users.

    
    
    <!-- A11y: avoid tabindex values above zero -->
    <div tabindex="1" />

## a11y-role-has-required-aria-propspermalink

Elements with ARIA roles must have all required attributes for that role.

    
    
    <!-- A11y: A11y: Elements with the ARIA role "checkbox" must have the following attributes defined: "aria-checked" -->
    <span role="checkbox" aria-labelledby="foo" tabindex="0" />

## a11y-role-supports-aria-propspermalink

Elements with explicit or implicit roles defined contain only `aria-*`
properties supported by that role.

    
    
    <!-- A11y: The attribute 'aria-multiline' is not supported by the role 'link'. -->
    <div role="link" aria-multiline />
    
    <!-- A11y: The attribute 'aria-required' is not supported by the role 'listitem'. This role is implicit on the element <li>. -->
    <li aria-required />

## a11y-structurepermalink

Enforce that certain DOM elements have the correct structure.

    
    
    <!-- A11y: <figcaption> must be an immediate child of <figure> -->
    <div>
    	<figcaption>Image caption</figcaption>
    </div>

## a11y-unknown-aria-attributepermalink

Enforce that only known ARIA attributes are used. This is based on the [WAI-
ARIA States and Properties
spec](https://www.w3.org/WAI/PF/aria-1.1/states_and_properties).

    
    
    <!-- A11y: Unknown aria attribute 'aria-labeledby' (did you mean 'labelledby'?) -->
    <input type="image" aria-labeledby="foo" />

## a11y-unknown-rolepermalink

Elements with ARIA roles must use a valid, non-abstract ARIA role. A reference
to role definitions can be found at [WAI-ARIA](https://www.w3.org/TR/wai-
aria/#role_definitions) site.

    
    
    <!-- A11y: Unknown role 'toooltip' (did you mean 'tooltip'?) -->
    <div role="toooltip" />

previous [Frequently asked questions](/docs/faq)

next [TypeScript](/docs/typescript)



Misc

# TypeScript

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/05-misc/03-typescript.md)

## On this page On this page

You can use TypeScript within Svelte components. IDE extensions like the
[Svelte VSCode
extension](https://marketplace.visualstudio.com/items?itemName=svelte.svelte-
vscode) will help you catch errors right in your editor, and [`svelte-
check`](https://www.npmjs.com/package/svelte-check) does the same on the
command line, which you can integrate into your CI.

## Setuppermalink

To use TypeScript within Svelte components, you need to add a preprocessor
that will turn TypeScript into JavaScript.

### Using SvelteKit or Vitepermalink

The easiest way to get started is scaffolding a new SvelteKit project by
typing `npm create svelte@latest`, following the prompts and choosing the
TypeScript option.

svelte.config.js

    
    
    ts
    
    import { vitePreprocess } from '@sveltejs/kit/vite';
    
    
    
    
    const config = {
    
    	preprocess: vitePreprocess()
    
    };
    
    
    
    
    export default config;

If you don't need or want all the features SvelteKit has to offer, you can
scaffold a Svelte-flavoured Vite project instead by typing `npm create
vite@latest` and selecting the `svelte-ts` option.

svelte.config.js

    
    
    ts
    
    import { vitePreprocess } from '@sveltejs/vite-plugin-svelte';
    
    
    
    
    const config = {
    
    	preprocess: vitePreprocess()
    
    };
    
    
    
    
    export default config;

In both cases, a `svelte.config.js` with `vitePreprocess` will be added.
Vite/SvelteKit will read from this config file.

### Other build toolspermalink

If you're using tools like Rollup or Webpack instead, install their respective
Svelte plugins. For Rollup that's [rollup-plugin-
svelte](https://github.com/sveltejs/rollup-plugin-svelte) and for Webpack
that's [svelte-loader](https://github.com/sveltejs/svelte-loader). For both,
you need to install `typescript` and `svelte-preprocess` and add the
preprocessor to the plugin config (see the respective READMEs for more info).
If you're starting a new project, you can also use the
[rollup](https://github.com/sveltejs/template) or
[webpack](https://github.com/sveltejs/template-webpack) template to scaffold
the setup from a script.

> If you're starting a new project, we recommend using SvelteKit or Vite
> instead

## <script lang="ts">permalink

To use TypeScript inside your Svelte components, add `lang="ts"` to your
`script` tags:

    
    
    <script lang="ts">
    	let name: string = 'world';
    
    	function greet(name: string) {
    		alert(`Hello, ${name}!`);
    	}
    </script>

### Propspermalink

Props can be typed directly on the `export let` statement:

    
    
    <script lang="ts">
    	export let name: string;
    </script>

### Slotspermalink

Slot and slot prop types are inferred from the types of the slot props passed
to them:

    
    
    <script lang="ts">
    	export let name: string;
    </script>
    
    <slot {name} />
    
    <!-- Later -->
    <Comp let:name>
    	<!--    ^ Inferred as string -->
    	{name}
    </Comp>

### Eventspermalink

Events can be typed with `createEventDispatcher`:

    
    
    <script lang="ts">
    	import { createEventDispatcher } from 'svelte';
    
    	const dispatch = createEventDispatcher<{
    		event: null; // does not accept a payload
    		click: string; // has a required string payload
    		type: string | null; // has an optional string payload
    	}>();
    
    	function handleClick() {
    		dispatch('event');
    		dispatch('click', 'hello');
    	}
    
    	function handleType() {
    		dispatch('event');
    		dispatch('type', Math.random() > 0.5 ? 'world' : null);
    	}
    </script>
    
    <button on:click={handleClick} on:keydown={handleType}>Click</button>

## Enhancing built-in DOM typespermalink

Svelte provides a best effort of all the HTML DOM types that exist. Sometimes
you may want to use experimental attributes or custom events coming from an
action. In these cases, TypeScript will throw a type error, saying that it
does not know these types. If it's a non-experimental standard
attribute/event, this may very well be a missing typing from our [HTML
typings](https://github.com/sveltejs/svelte/blob/master/packages/svelte/elements.d.ts).
In that case, you are welcome to open an issue and/or a PR fixing it.

In case this is a custom or experimental attribute/event, you can enhance the
typings like this:

additional-svelte-typings.d.ts

    
    
    ts
    
    declare namespace svelteHTML {
    
    	// enhance elements
    
    	interface IntrinsicElements {
    
    		'my-custom-element': { someattribute: string; 'on:event': (e: CustomEvent<any>) => void };
    
    	}
    
    	// enhance attributes
    
    	interface HTMLAttributes<T> {
    
    		// If you want to use on:beforeinstallprompt
    
    		'on:beforeinstallprompt'?: (event: any) => any;
    
    		// If you want to use myCustomAttribute={..} (note: all lowercase)
    
    		mycustomattribute?: any; // You can replace any with something more specific if you like
    
    	}
    
    }

Then make sure that `d.ts` file is referenced in your `tsconfig.json`. If it
reads something like `"include": ["src/**/*"]` and your `d.ts` file is inside
`src`, it should work. You may need to reload for the changes to take effect.

Since Svelte version 4.2 / `svelte-check` version 3.5 / VS Code extension
version 107.10.0 you can also declare the typings by augmenting the
`svelte/elements` module like this:

additional-svelte-typings.d.ts

    
    
    ts
    
    import { HTMLButtonAttributes } from 'svelte/elements';
    
    
    
    
    declare module 'svelte/elements' {
    
    	export interface SvelteHTMLElements {
    
    		'custom-button': HTMLButtonAttributes;
    
    	}
    
    
    
    
    	// allows for more granular control over what element to add the typings to
    
    	export interface HTMLButtonAttributes {
    
    		veryexperimentalattribute?: string;
    
    	}
    
    }
    
    
    
    
    export {}; // ensure this is not an ambient module, else types will be overridden instead of augmented

## Experimental advanced typingspermalink

A few features are missing from taking full advantage of TypeScript in more
advanced use cases like typing that a component implements a certain
interface, explicitly typing slots, or using generics. These things are
possible using experimental advanced type capabilities. See [this
RFC](https://github.com/dummdidumm/rfcs/blob/ts-typedefs-within-svelte-
components/text/ts-typing-props-slots-events.md) for more information on how
to make use of them.

> The API is experimental and may change at any point

## Limitationspermalink

### No TS in markuppermalink

You cannot use TypeScript in your template's markup. For example, the
following does not work:

    
    
    <script lang="ts">
    	let count = 10;
    </script>
    
    <h1>Count as string: {count as string}!</h1> <!-- ❌ Does not work -->
    {#if count > 4}
    	{@const countString: string = count} <!-- ❌ Does not work -->
    	{countString}
    {/if}

### Reactive Declarationspermalink

You cannot type your reactive declarations with TypeScript in the way you type
a variable. For example, the following does not work:

    
    
    <script lang="ts">
    	let count = 0;
    
    	$: doubled: number = count * 2; // ❌ Does not work
    </script>

You cannot add a `: TYPE` because it's invalid syntax in this position.
Instead, you can move the definition to a `let` statement just above:

    
    
    <script lang="ts">
    	let count = 0;
    
    	let doubled: number;
    	$: doubled = count * 2;
    </script>

## Typespermalink

### ComponentConstructorOptionspermalink

    
    
    ts
    
    interface ComponentConstructorOptions<
    
    	Props extends Record<string, any> = Record<string, any>
    
    > {…}
    
    
    ts
    
    target: Element | Document | ShadowRoot;
    
    
    ts
    
    anchor?: Element;
    
    
    ts
    
    props?: Props;
    
    
    ts
    
    context?: Map<any, any>;
    
    
    ts
    
    hydrate?: boolean;
    
    
    ts
    
    intro?: boolean;
    
    
    ts
    
    $$inline?: boolean;

### ComponentEventspermalink

Convenience type to get the events the given component expects. Example:

    
    
    <script lang="ts">
       import type { ComponentEvents } from 'svelte';
       import Component from './Component.svelte';
    
       function handleCloseEvent(event: ComponentEvents<Component>['close']) {
    	  console.log(event.detail);
       }
    </script>
    
    <Component on:close={handleCloseEvent} />
    
    
    ts
    
    type ComponentEvents<Component extends SvelteComponent_1> =
    
    	Component extends SvelteComponent<any, infer Events>
    
    		? Events
    
    		: never;

### ComponentPropspermalink

Convenience type to get the props the given component expects. Example:

    
    
    <script lang="ts">
    	import type { ComponentProps } from 'svelte';
    	import Component from './Component.svelte';
    
    	const props: ComponentProps<Component> = { foo: 'bar' }; // Errors if these aren't the correct props
    </script>
    
    
    ts
    
    type ComponentProps<Component extends SvelteComponent_1> =
    
    	Component extends SvelteComponent<infer Props>
    
    		? Props
    
    		: never;

### ComponentTypepermalink

Convenience type to get the type of a Svelte component. Useful for example in
combination with dynamic components using `<svelte:component>`.

Example:

    
    
    <script lang="ts">
    	import type { ComponentType, SvelteComponent } from 'svelte';
    	import Component1 from './Component1.svelte';
    	import Component2 from './Component2.svelte';
    
    	const component: ComponentType = someLogic() ? Component1 : Component2;
    	const componentOfCertainSubType: ComponentType<SvelteComponent<{ needsThisProp: string }>> = someLogic() ? Component1 : Component2;
    </script>
    
    <svelte:component this={component} />
    <svelte:component this={componentOfCertainSubType} needsThisProp="hello" />
    
    
    ts
    
    type ComponentType<
    
    	Component extends SvelteComponent = SvelteComponent
    
    > = (new (
    
    	options: ComponentConstructorOptions<
    
    		Component extends SvelteComponent<infer Props>
    
    			? Props
    
    			: Record<string, any>
    
    	>
    
    ) => Component) & {
    
    	/** The custom element version of the component. Only present if compiled with the `customElement` compiler option */
    
    	element?: typeof HTMLElement;
    
    };

### SvelteComponentpermalink

Base class for Svelte components with some minor dev-enhancements. Used when
dev=true.

Can be used to create strongly typed Svelte components.

#### Example:permalink

You have component library on npm called `component-library`, from which you
export a component called `MyComponent`. For Svelte+TypeScript users, you want
to provide typings. Therefore you create a `index.d.ts`:

    
    
    ts
    
    import { SvelteComponent } from "svelte";
    
    export class MyComponent extends SvelteComponent<{foo: string}> {}

Typing this makes it possible for IDEs like VS Code with the Svelte extension
to provide intellisense and to use the component like this in a Svelte file
with TypeScript:

    
    
    <script lang="ts">
    	import { MyComponent } from "component-library";
    </script>
    <MyComponent foo={'bar'} />
    
    
    ts
    
    class SvelteComponent<
    
    	Props extends Record<string, any> = any,
    
    	Events extends Record<string, any> = any,
    
    	Slots extends Record<string, any> = any
    
    > extends SvelteComponent_1<Props, Events> {…}
    
    
    ts
    
    [prop: string]: any;
    
    
    ts
    
    constructor(options: ComponentConstructorOptions<Props>);
    
    
    ts
    
    $capture_state(): void;
    
    
    ts
    
    $inject_state(): void;

### SvelteComponentTypedpermalink

> Use `SvelteComponent` instead. See PR for more information:
> <https://github.com/sveltejs/svelte/pull/8512>
    
    
    ts
    
    class SvelteComponentTyped<
    
    	Props extends Record<string, any> = any,
    
    	Events extends Record<string, any> = any,
    
    	Slots extends Record<string, any> = any
    
    > extends SvelteComponent<Props, Events, Slots> {}

previous [Accessibility warnings](/docs/accessibility-warnings)

next [Svelte 4 migration guide](/docs/v4-migration-guide)



Legacy

# svelte/register

[ Edit this page on
GitHub](https://github.com/sveltejs/svelte/edit/svelte-4/documentation/docs/06-legacy/01-svelte-
register.md)

## On this page On this page

> This API is removed in Svelte 4. `require` hooks are deprecated and current
> Node versions understand ESM. Use a bundler like Vite or our full-stack
> framework [SvelteKit](https://kit.svelte.dev) instead to create JavaScript
> modules from Svelte components.

To render Svelte components in Node.js without bundling, use
`require('svelte/register')`. After that, you can use `require` to include any
`.svelte` file.

    
    
    ts
    
    require('svelte/register');
    
    
    
    
    const App = require('./App.svelte').default;
    
    
    
    
    // ...
    
    
    
    
    const { html, css, head } = App.render({ answer: 42 });

> The `.default` is necessary because we're converting from native JavaScript
> modules to the CommonJS modules recognised by Node. Note that if your
> component imports JavaScript modules, they will fail to load in Node and you
> will need to use a bundler instead.

To set compile options, or to use a custom file extension, call the `register`
hook as a function:

    
    
    ts
    
    require('svelte/register')({
    
    	extensions: ['.customextension'], // defaults to ['.html', '.svelte']
    
    	preserveComments: true
    
    });

previous [Svelte 4 migration guide](/docs/v4-migration-guide)

next



