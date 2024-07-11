# design pattern

## Observer pattern
### 일반적인 상태변화 감지
```typescript
export default function Status() {
    const [value,setValue] = useState<string>("");

    const handleChangeValue = (event: ChangeEvent<HTMLInputElement>) => {
        const {value} = event.target;

        setValue(value);
    } 

  return (
    <div>
      <input value={value} onChange={handleChangeValue} />
      <p>{value}</p>
    </div>
  );
}
```

React 사용 시 state라는 상태를 이용하게 되는데\
이는 값을 변화시켰을때 DOM에게 신호를 발생시켜 해당 컴포넌트 함수를 재실행 시킨다.

---
### 상태관리 라이브러리를 이용한 상태변화 감지
```typescript
class viewModel {
    public value : string;

    constructor(){
        this.value = "";

        makeObservable(this, {
            value: observable,
            handleChangeValue: action,
        });
    }

    handleChangeValue = (event: ChangeEvent<HTMLInputElement>) => {
        const {value} = event.target;

        this.value = value;
    } 
}

function InputData({viewModel}:{viewModel:viewModel}) {

  return (
    <div>
      <input value={viewModel.value} onChange={viewModel.handleChangeValue} />
      <p>{viewModel.value}</p>
    </div>
  );
}

export default inject("viewModel")(observer(InputData));
```
위 코드는 MobX라이브러리를 사용하여 viewModel 내 변수를 observable화 하고\
값을 핸들링하는 이벤트를 action으로 지정했다.\
컴포넌트나 View에서는 inject를 이용해서 viewModel을 가져와 해당 viewModel 내 observable 변수를 구독하게 할 수 있다.\
이렇게 되면 InputData 컴포넌트함수 자체가 observable한 변수들을 모두 구독해 변화가 감지되었을때 변화된 값으로 리랜더링을 실시한다.

## Iterator pattern
```typescript
function* dataIterator(data) {
    for (let i = 0; i < data.length; i++) {
        yield data[i];
    }
}


export default function ComponentUsingIterator() {
    const dataArray = [1, 2, 3, 4, 5];
    const iterator = dataIterator(dataArray);
    const [currentItem, setCurrentItem] = useState<number>(iterator.next().value);

    const handleClick = () => {
        const nextItem = iterator.next().value;
        setCurrentItem(nextItem);
    };

    return (
        <div>
          <p>Current Item: {currentItem}</p>
          <button onClick={handleClick}>Next</button>
        </div>
    );
}
```

제너레이터를 사용한 이터레이터패턴 모방코드이다.\
React내에서 이런 코드를 작성한다면 오버엔지니어링이라고할 수 있다.\
제너레이터의 yield 함수중단, next의 다음 반복 호출을 이용한 것인데\
프론트엔드 개발 시 정말 사용할 일이 없다.
