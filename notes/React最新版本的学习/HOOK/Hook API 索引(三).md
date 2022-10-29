## useDeferredValue:

```js
const deferredValue = useDeferredValue(value);
```

useDeferredValue接收一个值,并返回该值的新副本,新副本将遵从更紧急的更新.如果当前渲染是紧急更新的结果,就像用户输入一样,React将返回前一个值,然后在紧急渲染完成后渲染新值.这个钩子类似于用户空间钩子,它们使用缓冲或节流来延迟更新.使用useDefferredValue的好处是,一旦其他工作完成,React就会立即处理更新(而不是等待任意数量的时间),而且与startTranistion一样,延迟值可以挂起,而不会触发现有内容的意外回退.

useDeferredValue仅延迟传递给它的值.如果要防止在紧急更新期间重新呈现子组件,还必须使用React记住该组件,使用React.memo或React.useMemo()

```js
function Typeahead() {
  const query = useSearchQuery('');
  const deferredQuery = useDeferredValue(query);

  	//记忆告诉React只有在deferredQuery更改时才重新呈现，
	//当查询改变时。
  const suggestions = useMemo(() =>
    <SearchSuggestions query={deferredQuery} />,
    [deferredQuery]
  );

  return (
    <>
      <SearchInput query={query} />
      <Suspense fallback="Loading results...">
        {suggestions}
      </Suspense>
    </>
  );
}
```

记住这些子元素告诉React,它只需要在deferredQuery更改时重新呈现它们,而在查询时更改不需要.这个警告并不是useDeferredValue所独有的它与你在使用减震或节流的类似钩子中使用的模式相同.

## useTransition:

```js
const [isPending, startTransition] = useTransition();
```

返回准换暂挂状态的有效状态值,以及启动该暂挂状态的函数.

startTransition允许你在提供的回调中将更新标记为转换:

```js
startTransition(() => {
  setCount(count + 1);
})
```

isPending表示当一个转换激活时显示一个挂起状态.

```js
function App() {
  const [isPending, startTransition] = useTransition();
  const [count, setCount] = useState(0);
  
  function handleClick() {
    startTransition(() => {
      setCount(c => c + 1);
    })
  }

  return (
    <div>
      {isPending && <Spinner />}
      <button onClick={handleClick}>{count}</button>
    </div>
  );
}
```

Warning:

转换中的更新会转向更紧急的更新,比如单机.转换中的更新不会显示重新挂起内容的回退.这允许用户在呈现更新时继续与当前内容交互.

## useId:

```js
const id = useId();
```

useId是一个钩子,用于生成跨服务器和客户端稳定的唯一id,同时避免水合不匹配.

Warning:

useId不用于在列表中生成密钥(key),密钥应该从数据中生成.

举一个基本的例子,将id直接传递给需要它的元素.

```js
function Checkbox() {
  const id = useId();
  return (
    <>
      <label htmlFor={id}>Do you like React?</label>
      <input id={id} type="checkbox" name="react"/>
    </>
  );
};
```

对于同一个组件中的多个id,使用相同的id添加后缀:

```js
function NameFields() {
  const id = useId();
  return (
    <div>
      <label htmlFor={id + '-firstName'}>First Name</label>
      <div>
        <input id={id + '-firstName'} type="text" />
      </div>
      <label htmlFor={id + '-lastName'}>Last Name</label>
      <div>
        <input id={id + '-lastName'} type="text" />
      </div>
    </div>
  );
}
```

useId生成一个包含:令牌的字符串,这有助于确保令牌是唯一的,但在css选择器或querySelectorAll等API中不支持.

useId支持一个identifierPrefix来防止多根应用中的冲突。要进行配置，请参见hydrateRoot和ReactDOMServer的选项。 