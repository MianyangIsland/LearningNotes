# 自定义Hook:

通过自定义Hook,可以将组件提取到可重用的函数中.目前为止,在React中有两种流行的方式来共享组件之间的状态逻辑:render props和高阶组件.

## 提取自定义Hook:

当我们想在两个函数之间共享逻辑时，我们会把它提取到第三个函数中。而组件和 Hook 都是函数，所以也同样适用这种方式。 

```js
import { useState, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```



**自定义Hook是一个函数,其名称以"use"开头,函数内部可以调用其他的Hook**:与组件中一致,确保只在自定义Hook的顶层无条件地调用其他Hook.

与React组件不同地是,自定义Hook不需要具有特殊地标识.可以自由地决定它的参数是什么,以及它应该返回什么(如果需要的话).换句话说,它就像一个正常的函数.但是它的名称始终以use开头,这样可以一眼看出它符合Hook的规则.



## 使用自定义Hook:

我们一开始的目标是在 `FriendStatus` 和 `FriendListItem` 组件中去除重复的逻辑，即：这两个组件都想知道好友是否在线。现在我们已经把这个逻辑提取到 `useFriendStatus` 的自定义 Hook 中，然后就可以*使用它了：*

```js
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}



function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

这只是将两个函数之间一些共同的代码提取到单独的函数中,自定义Hook 是一种自然遵循Hook设计的约定,而并不是React的特性.

**自定义Hook必须以'use'开头**,不遵循的话,由于无法判单某个函数是否包含对其内部Hook的调用,React将无法自动检查你的Hook是否违反了Hook规则.

**在两个组件中使用相同的Hook不会共享state**,自定义Hook是一种重用状态逻辑的机制,所以每次使用自定义Hook时,其中的state和副作用都是完全隔离的.

**自定义Hook如何获取独立的state?** ,每次调用Hook,它都会获取独立的state.由于我们直接调用了 `useFriendStatus` ,从React的角度来看,我们的组件只是调用了useState和useEffect.

## 提示:在多个Hook之间传递信息:

由于Hook本身就是函数,因此我们可以在它们之间传递信息.

