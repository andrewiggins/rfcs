Libraries to look at:

- [solid-js](https://www.npmjs.com/package/solid-js)
- [ivi](https://www.npmjs.com/package/ivi)
- [inferno](https://www.npmjs.com/package/inferno)
- [petit-dom](https://www.npmjs.com/package/petit-dom)
- [hyperhtml](https://www.npmjs.com/package/hyperhtml)
- [snabbdom](https://www.npmjs.com/package/snabbdom)

Today's options

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

## Other Component models

### (Template to copy)

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

#### Update methods

#### Context API

#### Direct DOM Access

#### Other APIs


### Svelte

Reversed engineered looking compiler output of various examples on Svelte website: (e.g. 7guis-crud, Lifecycle examples, )

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

#### Other APIs

- `tick`: Returns a promise that resolves once any pending state changes have been applied, or in the next microtask if there are none.
- Animations
- State stores
- Style encapsulation
- Animation & transitions
- Hydration support

Other Internal Lifecycles:

- `c`: create (build elements)
- `l`: link (hydrate)
- `h`: hook up attributes (used in hydrate mode to encapsulate setting attributes on nodes after `create` and `link` stages)
- `m`: mount (connect created elements to DOM)
- `p`: patch (update)
- `d`: destroy (unmount)
- `i`: in (for transitioning in components)
- `o`: out (for transitioning out components)

### React

Two-phase render: (1) render, (2) commit phases.

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

#### Other APIs

- SSR & hydration
- Lazy loading through `Suspense`, `lazy`
- Delay rendering update with `useTransition`

### Angular

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

#### Other APIs

- SSR & hydration
- Lazily loading ngModules
- Animation & transitions
- Routing
- HttpClient
- Observables
- Style encapsulation

### Vue

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

#### Other APIs

- `vm.$nextTick`
- [SSR & hydration](https://ssr.vuejs.org/guide/hydration.html)
- Style encapsulation
- Mixins
- Templates & render functions
- Observables
