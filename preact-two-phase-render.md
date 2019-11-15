# Preact two phase render

## Definition

## Prior Work

* [@sventschui's Two Phase render PR](https://github.com/preactjs/preact/pull/1906)
* [@swyx React Advanced talk](https://www.youtube.com/watch?v=dFO4m7Y-yhs) ([code](https://codesandbox.io/s/reactadvanced-final-uwrx0))
* [@pomber "Build your own React" blog](https://pomb.us/build-your-own-react/)

## General diff architecture

Stages:

* render
* commit

Component lifecycle:

* Create
* Update
* Delete

React's SideEffect Tags:

[source](https://git.io/JerbZ)

* Placement
* Update
* PlacementAndUpdate
* Deletion
* ContentReset
* Callback
* DidCapture
* Ref
* Snapshot
* Passive
* Hydrating
* HydratingAndUpdate

React's HookEffect Tags:

[source](https://git.io/Jerbm)

* NoEffect
* UnmountSnapshot
* UnmountMutation
* MountMutation
* UnmountLayout
* MountLayout
* MountPassive
* UnmountPassive

React's [commitRoot](https://git.io/JerbB) is where they commit work from effect queue


### Pseudo-code 

```js
function renderComponent(c) {
  const domQueue = [];
  const commitQueue = [];

  const oldVNode = assign({}, c._newVNode);
  diff(c._newVNode, oldVNode, domQueue, commitQueue);
  commitRoot(domQueue, commitQueue);
}

function diff(newVNode, oldVNode, domQueue, commitQueue) {
  if (typeof newVNode.type == "function") {
    renderComponent(newVNode, oldVNode);
    diffChildren(newVNode, oldVNode);
  } else {
    diffElementNodes(newVNode, oldVNode);
    diffChildren(newVNode, oldVNode);
  }

  if (newVNode.domEffects.length) {
    domQueue.push(newVNode);
  }

  if (newVNode.renderCallbacks.length) {
    commitQueue.push(newVNode);
  }
}

function diffChildren(newVNode, oldVNode, domQueue, commitQueue) {
  // TODO
}

function commitRoot(domQueue, commitQueue) {
  for (let vnode of domQueue) {
    drainQueue(vnode.domEffects);
  }

  for (let vnode of commitQueue) {
    drainQueue(vnode.renderCallbacks);
  }
}
```

## Approaches

### Delaying commit

#### Methods to delay work

##### Closures

##### Method dispatch

##### Commit commands

### Walking VNodes twice
