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
