Libraries to look at:

- [domvm](https://github.com/domvm/domvm/)
- [solid-js](https://www.npmjs.com/package/solid-js)
- [ivi](https://www.npmjs.com/package/ivi)
- [petit-dom](https://www.npmjs.com/package/petit-dom)
- [hyperhtml](https://www.npmjs.com/package/hyperhtml)

Others to consider:

- [vue-next](https://github.com/vuejs/vue-next)
- [domdiff](https://github.com/WebReflection/domdiff/blob/master/esm/index.js
- [surplus]()

Preact's options today

```ts
interface Options {
  /** Attach a hook that is invoked whenever a VNode is created. */
  vnode?(vnode: VNode): void;
  /** Attach a hook that is invoked immediately before a vnode is unmounted. */
  unmount?(vnode: VNode): void;
  /** Attach a hook that is invoked after a vnode has rendered. */
  diffed?(vnode: VNode): void;
  event?(e: Event): void;

  requestAnimationFrame?: typeof requestAnimationFrame;
  debounceRendering?(cb: () => void): void;
  useDebugValue?(value: string | number): void;
}
```

```ts
interface InternalOptions {
  /** Attach a hook that is invoked before render, mainly to check the arguments. */
  _root?(vnode: preact.ComponentChild, parent: Element): void;
  /** Attach a hook that is invoked before a vnode is diffed. */
  _diff?(vnode: VNode): void;
  /** Attach a hook that is invoked after a tree was mounted or was updated. */
  _commit?(vnode: VNode, commitQueue: Component[]): void;
  /** Attach a hook that is invoked before a vnode has rendered. */
  _render?(vnode: VNode): void;
  /** Attach a hook that is invoked before a hook's state is queried. */
  _hook?(component: Component): void;
  /** Attach a hook that is invoked after an error is caught in a component but before calling lifecycle hooks */
  _catchError(error: any, vnode: VNode, oldVNode: VNode | undefined): void;
}
```

## Sample extensions to validate component model

- [Class component API](https://github.com/preactjs/preact/blob/ca42c53210db925fe14d8f0f9122eb3bf43556d7/src/component.js#L13)
- [Function component API](https://github.com/preactjs/preact/blob/ca42c53210db925fe14d8f0f9122eb3bf43556d7/src/diff/index.js#L450)
- [Hooks API](https://github.com/preactjs/preact/blob/ca42c53210db925fe14d8f0f9122eb3bf43556d7/hooks/src/index.js)
- [Composition API components](https://github.com/preactjs/preact/pull/1923)
- [Flow components](https://github.com/jviide/flowponent)
- Snabbdom Hero module ([sample](http://snabbdom.github.io/snabbdom/examples/hero/), [code](https://github.com/snabbdom/snabbdom/blob/master/src/modules/hero.ts))
- Snabbdom delayed/removed style attribute extension ([sample](http://snabbdom.github.io/snabbdom/examples/reorder-animation/), [code](https://github.com/snabbdom/snabbdom/blob/master/src/modules/style.ts))
- Consider experimenting if refs can be added as an extension
- Controlled inputs (another, perhaps better? implementation would be to improve `options.event` callback)
- Think about way for different component models to hook into devtools and prefresh

## Other Component models

### Snabbdom 0.7.4

#### VNode shape

- `sel: string`: CSS selector passed to h() during creation to create the DOM node
- `data: object`: The place to add information for modules to access and manipulate the real DOM element when it is created
- `children: Vnode[]`: An Array of virtual nodes that should be added as children of the parent DOM node upon creation.
- `text: string`: This property is created when a virtual node is created with only a single child that possesses text and only requires document.createTextNode() to be used.
- `elm: Element`: A pointer to the real DOM node created by snabbdom.
- `key: string | number`: Used to keep pointers to DOM nodes that existed previously to avoid recreating them if it is unnecessary.

#### Mounting lifecycles

<dl>
  <dt><code>init</code></dt>
  <dd>
    A vnode has been added. Available on vnodes only. Args: <code>(vnode)</code>
  </dd>
  
  <dt><code>create</code></dt>
  <dd>
    A DOM element has been created based on a vnode. Available on module and vnodes. Args: <code>(emptyVnode, vnode)</code>
  </dd>
  
  <dt><code>insert</code></dt>
  <dd>
    An element has been inserted into the DOM. Available on vnodes only. Args: <code>(vnode)</code>
  </dd>
</dl>

#### Updating lifecycles

<dl>
  <dt><code>pre</code></dt>
  <dd>
    The patch process begins. Available at module scope only. Args: <code>()</code>
  </dd>
  
  <dt><code>prepatch</code></dt>
  <dd>
    An element is about to be patched. Available on vnodes only. Args: <code>(oldVnode, vnode)</code>
  </dd>
  
  <dt><code>update</code></dt>
  <dd>
    An element is being updated. Available on module-scope and vnodes. Args: <code>(oldVnode, vnode)</code>
  </dd>
  
  <dt><code>postpatch</code></dt>
  <dd>
    An element has been patched. Available on vnodes only. Args: <code>(oldVnode, vnode)</code>
  </dd>
  
  <dt><code>post</code></dt>
  <dd>
    The patch process is done. Available on module-scope only. Args: <code>()</code>
  </dd>
</dl>

#### Destroy lifecycles

<dl>
  <dt><code>destroy</code></dt>
  <dd>
    An element is directly or indirectly being removed. Available on module-scope and vnodes. Args: <code>(vnode)</code>
  </dd>
  
  <dt><code>remove</code></dt>
  <dd>
    An element is directly being removed from the DOM. Available on module-scope and vnodes. Args: <code>(vnode, removeCallback)</code>
  </dd>
</dl>

#### Error handling

Manually through `try/catch` around root `patch` call. No component level `try/catch` natively supported.

#### Update methods

Requires re-rendering the app from the top.

#### Context API

Manually through global variables.

#### Direct DOM Access

Access through `vnode.elm` property in lifecycle hooks.

#### Hydration

- [`tovnode` function](https://www.npmjs.com/package/snabbdom#snabbdomtovnode) to convert DOM to VNode

#### Other APIs

- Animations with style delay/remove/destroy api
- component memoization with `thunk`

### Svelte 3

Reversed engineered looking compiler output of various examples on Svelte website: (e.g. 7guis-crud, Lifecycle examples, )

#### Internal Component shape

_Svelte doesn't have a VNode shape_

- `c`: create (build elements)
- `l`: link (hydrate)
- `h`: hook up attributes (used in hydrate mode to encapsulate setting attributes on nodes after `create` and `link` stages)
- `m`: mount (connect created elements to DOM)
- `p`: patch (update)
- `d`: destroy (unmount)
- `i`: in (for transitioning in components)
- `o`: out (for transitioning out components)

#### Mounting lifecycles

<dl>
  <dt><code>onMount</code></dt>
  <dd>
    Schedules a callback to run as soon as the component has been mounted to the DOM. Can return a function to be called on unmount
  </dd>
</dl>

#### Updating lifecycles

<dl>
  <dt><code>beforeUpdate</code></dt>
  <dd>
    Schedules a callback to run immediately before the component is updated after any state change.
  </dd>
  
  <dt><code>afterUpdate</code></dt>
  <dd>
    Schedules a callback to run immediately after the component has been updated.
  </dd>
</dl>

#### Destroy lifecycles

<dl>
  <dt><code>onDestroy</code></dt>
  <dd>
    Schedules a callback to run once the component is unmounted.
  </dd>
</dl>

#### Error handling

`try/catch` in reactive code? Not sure about catching errors in child components

#### Update methods

- Compiler is reactive to local variable changes
- `$:` labelled statements are reactive

#### Context API

- `setContext`/`getContext`

#### Direct DOM Access

Manual DOM APIs?

#### Hydration

- [`hydrate` option](https://svelte.dev/docs#Creating_a_component)

#### Other APIs

- `tick`: Returns a promise that resolves once any pending state changes have been applied, or in the next microtask if there are none.
- Animations
- State stores
- Style encapsulation
- Animation & transitions
- Hydration support

### Inferno 7.4

_Component lifecycles and VNode shape: https://git.io/Jv5xr_

#### VNode shape

- type: any;
- props: any;
- className: string | null | undefined;
- key: null | number | string;
- ref: any;
- children: InfernoNode;
- dom: Element | null;
- flags: VNodeFlags;
- childFlags: ChildFlags;
- isValidated?: boolean;

#### Mounting lifecycles

- constructor
- getDerivedStateFromProps
- componentWillMount
- render
- componentDidMount

#### Updating lifecycles

- getDerivedStateFromProps
- componentWillReceiveProps
- shouldComponentUpdate
- componentWillUpdate
- render
- getSnapshotBeforeUpdate
- componentDidUpdate

#### Destroy lifecycles

- componentWillUnmount

#### Error handling

- None?

#### Update methods

- setState/forceUpdate

#### Context API

- getChildContext

#### Direct DOM Access

- React-style refs: `createRef` & Ref callbacks

#### Hydration

- [`inferno-hydrate` package](https://www.npmjs.com/package/inferno-hydrate)

#### Other APIs

- VNode perf flags:
  - `$HasVNodeChildren?: boolean`
  - `$HasNonKeyedChildren?: boolean;`
  - `$HasKeyedChildren?: boolean;`
  - `$ChildFlag?: number;`
- Functional lifecycles (defined as props?)
  - `onComponentDidMount?: (domNode: Element | null, nextProps: P) => void;`
  - `onComponentWillMount?(): void;`
  - `onComponentShouldUpdate?(lastProps: P, nextProps: P): boolean;`
  - `onComponentWillUpdate?(lastProps: P, nextProps: P): void;`
  - `onComponentDidUpdate?(lastProps: P, nextProps: P): void;`
  `- onComponentWillUnmount?(domNode: Element, nextProps: P): void;`
- Options interface
  - `componentComparator: (lastVNode: VNode, nextVNode: VNode) => boolean`
    - This option can be used during **development** to create custom component comparator method. This option will be called on every Component update. It gets two parameters: lastVNode and nextVNode. When it returns `true` lastVNode will be replaced with nextVNode. If anything else than `true` is returned it falls to normal behavior.
  - `createVNode: (vNode: VNode) => void`
    - Called when a new VNode is created
  - `renderComplete: (rootInput: VNode, parentDOM: Element) => void`;
    - Called after initial render
  - `reactStyles?: boolean`

### React 16

Two-phase render: (1) render, (2) commit phases.

#### VNode shape

[ReactElement](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/6c625a1f0e727089814d03bcc75d6bddf1e82776/types/react/index.d.ts#L146):

- `type`
- `props`
- `key`
- `ref`

[ReactFiber](https://github.com/facebook/react/blob/a600408b284e92ed3d801d7f4e37d10b5da06b10/packages/react-reconciler/src/ReactFiber.js#L129):

<dl>
  <dt><code>tag</code></dt>
  <dd>
    FunctionComponent, ClassComponent, HostComponent, etc.
  </dd>
  
  <dt><code>key</code></dt>
  <dd>
    Unique identifier of this child
  </dd>

  <dt><code>elementType</code></dt>
  <dd>
    The value of element.type which is used to preserve the identity during reconciliation of this child.
  </dd>

  <dt><code>type</code></dt>
  <dd>
    The resolved function/class/ associated with this fiber.
  </dd>

  <dt><code>stateNode</code></dt>
  <dd>
    The local state associated with this fiber.
  </dd>

  <dt><code>return</code></dt>
  <dd>
    The Fiber to return to after finishing processing this one. This is effectively the parent, but there can be multiple parents (two) so this is only the parent of the thing we're currently processing. It is conceptually the same as the return address of a stack frame.
  </dd>

  <dt><code>child, sibling, index</code></dt>
  <dd>
    Singly Linked List Tree Structure.
  </dd>

  <dt><code>ref</code></dt>
  <dd>
    The ref last used to attach this node.
  </dd>

  <dt><code>pendingProps</code></dt>
  <dd>
    Input is the data coming into process this fiber. Arguments. Props.
  </dd>

  <dt><code>memoizedProps</code></dt>
  <dd>
    Input is the data coming into process this fiber. Arguments. Props. The props used to create the output.
  </dd>

  <dt><code>updateQueue</code></dt>
  <dd>
    A queue of state updates and callbacks
  </dd>

  <dt><code>memoizedState</code></dt>
  <dd>
    The state used to create the output
  </dd>

  <dt><code>dependencies</code></dt>
  <dd>
    Dependencies (contexts, events) for this fiber, if it has any
  </dd>

  <dt><code>mode</code></dt>
  <dd>
    Bitfield that describes properties about the fiber and its subtree. E.g. the ConcurrentMode flag indicates whether the subtree should be async-by-default. When a fiber is created, it inherits the mode of its parent. Additional flags can be set at creation time, but after that the value should remain unchanged throughout the fiber's lifetime, particularly before its child fibers are created.
  </dd>

  <dt><code>effectTag</code></dt>
  <dd>
    Effect
  </dd>

  <dt><code>nextEffect</code></dt>
  <dd>
    Singly linked list fast path to the next fiber with side-effects.
  </dd>

  <dt><code>firstEffect/lastEffect</code></dt>
  <dd>
    The first and last fiber with side-effect within this subtree. This allows us to reuse a slice of the linked list when we reuse the work done within this fiber.
  </dd>

  <dt><code>expirationTime</code></dt>
  <dd>
    Represents a time in the future by which this work should be completed. Does not include work found in its subtree.
  </dd>

  <dt><code>childExpirationTime</code></dt>
  <dd>
    This is used to quickly determine if a subtree has no pending changes.
  </dd>

  <dt><code>alternate</code></dt>
  <dd>
    This is a pooled version of a Fiber. Every fiber that gets updated will eventually have a pair. There are cases when we can clean up pairs to save memory if we need to.
  </dd>
</dl>

#### Mounting Lifcycles

- constructor
- getDerivedStateFromProps
- render
- componentDidMount
- `useLayoutEffect`
- `useEffect`

#### Update Lifecyles

- getDerivedStateFromProps
- shouldComponentUpdate/React.memo
- render
- getSnapshotBeforeUpdate
- componentDidUpdate
- `useLayoutEffect`
- `useEffect`

#### Destroy Lifecycles

- componentWillUnmount
- `useLayoutEffect` cleanup
- `useEffect` cleanup

#### Error Handling

- getDerivedStateFromError
- componentDidCatch

#### Update methods

- setState()
- forceUpdate()
- `useState`
- `useReducer`

#### Global context

- `contextType`
- `useContext`

#### Direct DOM Access

- `createRef`
- callback refs
- `forwardRef`
- `useRef`

#### Hydration

- [`ReactDOM.hydrate`](https://reactjs.org/docs/react-dom.html#hydrate)

#### Other APIs

- SSR & hydration
- Lazy loading through `Suspense`, `lazy`
- Delay rendering update with `useTransition`

### Angular 9

Mostly taken from https://angular.io/guide/lifecycle-hooks

#### Mounting lifecycles

<dl>
  <dt><code>ngOnInit</code></dt>
  <dd>
    Initialize the directive/component after Angular first displays the data-bound properties and sets the directive/component's input properties.
  </dd>

  <dt><code>ngAfterContentInit</code></dt>
  <dd>
    Respond after Angular projects external content into the component's view / the view that a directive is in.
  </dd>

  <dt><code>ngAfterViewInit</code></dt>
  <dd>
    Respond after Angular initializes the component's views and child views / the view that a directive is in.
  </dd>
</dl>

#### Updating lifecycles

_Technically called on mount too_

_Other Angular sub-systems may have their own lifecycle hooks apart from these component hooks._

<dl>
  <dt><code>ngOnChanges</code></dt>
  <dd>Respond when Angular (re)sets data-bound input properties.</dd>
  
  <dt><code>doCheck</code></dt>
  <dd>Detect and act upon changes that Angular can't or won't detect on its own.</dd>
  
  <dt><code>ngAfterContentChecked</code></dt>
  <dd>Respond after Angular checks the content projected into the directive/component.</dd>
  
  <dt><code>ngAfterViewChecked</code></dt>
  <dd>Respond after Angular checks the component's views and child views / the view that a directive is in.</dd>
</dl>

#### Destroy lifecycles

<dl>
  <dt><code>ngOnDestroy</code></dt>
  <dd>Cleanup just before Angular destroys the directive/component.</dd>
</dl>

#### Error handling

Appears there might be a way to do Global Error handling? Might be manual

#### Update methods

Relies on property change detection (is it reactive?)

#### Global context

Uses dependency injection pattern for this I think?

#### Direct DOM Access

- [Template reference variables](https://angular.io/guide/template-syntax#template-reference-variables-var)

#### Hydration

- [Server-side rendering with Angular Universal](https://angular.io/guide/universal#server-side-rendering-ssr-with-angular-universal)

#### Other APIs

- Lazily loading ngModules
- Animation & transitions
- Routing
- HttpClient
- Observables
- Style encapsulation

### Vue 2

Mostly taken from https://angular.io/guide/lifecycle-hooks

#### Mounting lifecycles

<dl>
  <dt><code>beforeCreate</code></dt>
  <dd>
    Called synchronously immediately after the instance has been initialized, before data observation and event/watcher setup.
  </dd>

  <dt><code>created</code></dt>
  <dd>
    Called synchronously after the instance is created. At this stage, the instance has finished processing the options which means the following have been set up: data observation, computed properties, methods, watch/event callbacks. However, the mounting phase has not been started, and the $el property will not be available yet.
  </dd>

  <dt><code>beforeMount</code></dt>
  <dd>
    Called right before the mounting begins: the render function is about to be called for the first time.
  </dd>
  
  <dt><code>mounted</code></dt>
  <dd>
    Called after the instance has been mounted, where el is replaced by the newly created vm.$el. If the root instance is mounted to an in-document element, vm.$el will also be in-document when mounted is called.
  </dd>
</dl>

#### Updating lifecycles

<dl>
  <dt><code>beforeUpdate</code></dt>
  <dd>
    Called when data changes, before the DOM is patched.
  </dd>
  
  <dt><code>updated</code></dt>
  <dd>
    Called after a data change causes the virtual DOM to be re-rendered and patched.
  </dd>
  
  <dt><code>activated</code></dt>
  <dd>
    Called when a kept-alive component is activated.
  </dd>
  
  <dt><code>deactivated</code></dt>
  <dd>
    Called when a kept-alive component is deactivated.
  </dd>
</dl>

#### Destroy lifecycles

<dl>
  <dt><code>beforeDestroy</code></dt>
  <dd>
    Called right before a Vue instance is destroyed. At this stage the instance is still fully functional.
  </dd>
  
  <dt><code>destroyed</code></dt>
  <dd>
    Called after a Vue instance has been destroyed. When this hook is called, all directives of the Vue instance have been unbound, all event listeners have been removed, and all child Vue instances have also been destroyed.
  </dd>
</dl>

#### Error handling

<dl>
  <dt><code>errorCaptured</code></dt>
  <dd>
    Called when an error from any descendent component is captured.
  </dd>
</dl>

#### Update methods

- [Reactive `data` object](https://vuejs.org/v2/api/#data)

#### Global context

- The [provide/inject](https://vuejs.org/v2/api/#provide-inject) feature mimics a global context

#### Direct DOM Access

- [Refs](https://vuejs.org/v2/api/#ref)

#### Hydration

- `app.$mount('#app', true)`: [SSR & hydration](https://ssr.vuejs.org/guide/hydration.html)

#### Other APIs

- `vm.$nextTick`
- Style encapsulation
- Mixins
- Templates & render functions
- Observables
