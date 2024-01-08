# Redux

Redux là một `thư viện quản lý trạng thái (state management)` cho ứng dụng ReactJS. Nó giúp quản lý các trạng thái (state) phức tạp và chia sẻ chúng qua nhiều thành phần của ứng dụng.

Trong Redux, `state` của toàn bộ ứng dụng được lưu trữ trong một đối tượng gọi là `"store"`. `Store `là nơi duy nhất để lưu trữ và cập nhật `state` của ứng dụng. Bất kỳ thành phần nào trong ứng dụng cũng có thể truy cập và cập nhật `state` từ `store`.

Một ứng dụng Redux bao gồm các thành phần chính sau:

- **Actions:** Định nghĩa các hành động (actions) mà người dùng có thể thực hiện. Mỗi hành động được biểu diễn dưới dạng một `plain object` với một thuộc tính `type` đại diện cho loại hành động và các thuộc tính khác chứa các thông tin liên quan. Ví dụ thường thấy:

```jsx
{
    type: 'ACTION_TYPE',
    payload: "INPUT_VALUE"
}
```

- **Reducers:** Các reducers là các hàm xử lý hành động và cập nhật `state` trong store. Mỗi reducer đại diện cho một phần của `state` và xác định cách `state` sẽ được cập nhật dựa trên hành động.

- **Store:** Store là nơi lưu trữ toàn bộ `state` của ứng dụng. Nó nhận các hành động thông qua reducers và cập nhật `state` tương ứng.

- **Connect:** Thành phần React được bao bọc bằng `<Provider>` và có thể kết nối với Redux bằng cách sử dụng HOC `connect` từ thư viện `react-redux`. Điều này cho phép các thành phần truy cập `state` từ store và theo dõi các thay đổi trong `state`. Hiện nay, `react-redux` cung cấp hooks `useSelector` và `useDispatch` giúp thay thế `connect` để giao tiếp với `store`

# Cách thức hoạt động của Redux

![!](https://github.com/lechauhuuthien/reactjs-docs/blob/master/img/redux-flow.gif?raw=true)

Flow hoạt động của Redux bao gồm các bước sau:

- **Khởi tạo Store:** Đầu tiên, chúng ta khởi tạo `store` bằng cách sử dụng hàm `createStore` từ Redux và truyền vào `rootReducer` (kết hợp các reducers) và `applyMiddleware` để kích hoạt `middleware`.

- **Dispatch Action:** Khi một hành động `(action)` được gọi từ thành phần hoặc bất kỳ vị trí nào trong ứng dụng, chúng ta sử dụng phương thức `dispatch` của `store` để gửi hành động này tới Redux.

- **Middleware:** `Middleware` là một lớp trung gian nằm giữa `dispatch(action)` và `reducers`, sẽ chặn action thực thi, sau đó mới truyền vào reducers. Nó cho phép chúng ta thực hiện các công việc thêm như xử lý `side effects`, `gọi API`, `logging`, `routing`, và `quản lý trạng thái bất đồng bộ`. Middleware có thể can thiệp vào quá trình xử lý hành động trước khi nó đến reducers hoặc sau khi nó đã đi qua reducers.

- **Reducers:** Sau khi hành động đi qua middleware, nó đến reducers. `Reducers` là các hàm `pure function`, nhận vào `state` hiện tại và `actions`, và trả về `state` mới dựa trên `action`.

- **Cập nhật Store:** Khi reducers trả về `state` mới, Redux cập nhật store với `state` mới này.

- **Kết nối với React Components:** Khi store được cập nhật, Redux thông báo cho các thành phần đã kết nối mà `state` đã thay đổi. Các thành phần sau đó sẽ được render lại với `state` mới từ store.

- **Thay đổi trong thành phần:** Khi thành phần React kích hoạt hành động (action) hoặc có sự tương tác người dùng khác, quá trình trên được lặp lại, bắt đầu từ việc gửi hành động qua middleware cho đến cập nhật store và kết thúc bằng việc render lại thành phần với `state` mới.

# Ví dụ 1: Ứng dụng counter sử dụng connect

1. **Cài đặt Redux:** Bắt đầu bằng việc tạo một thư mục mới và chạy lệnh sau để cài đặt các gói cần thiết:

   > yarn add redux react-redux

2. **Tạo các reducers:** Trong thư mục src, tạo một thư mục reducers. Trong thư mục này, tạo một file counterReducer.js và định nghĩa reducer cho tính năng đếm:

```jsx
const counterReducer = (state = 0, action) => {
  switch (action.type) {
    case "INCREMENT":
      return state + action.payload;
    case "DECREMENT":
      return state - action.payload;
    default:
      return state;
  }
};

export default counterReducer;
```

3. **Tạo Store:** Trong thư mục src, tạo một file store.js và tạo store cho ứng dụng:

```jsx
import { combineReducers, createStore } from "redux";
import counterReducer from "./reducers/counterReducer";

const reducers = combineReducers({
  counter: counterReducer,
});

const store = createStore(counterReducer);

export default store;
```

4. **Kết nối với React:** Trong file `src/index.js` hoặc `src/main.js`, sửa đổi code như sau để kết nối ứng dụng với Redux:

```jsx
import React from "react";
import ReactDOM from "react-dom";
import { Provider } from "react-redux";
import store from "./store";
import App from "./App";

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

5. **Dispatch actions:** Trong thư mục src, tạo một file actions.js và định nghĩa các hành động cho tính năng đếm:

```jsx
export const increment = (payload) => {
  return {
    type: "INCREMENT",
    payload: payload || 1,
  };
};

export const decrement = (payload) => {
  return {
    type: "DECREMENT",
    payload: payload || 1,
  };
};
```

6. **Kết nối React Components:** Trong file src/App.js, sử dụng decorator `connect` để kết nối component với Redux:

```jsx
import React from "react";
import { connect } from "react-redux";
import { increment, decrement } from "./actions";

const App = (props) => {
  return (
    <div>
      <h1>Counter: {props.counter.counter}</h1>
      <button onClick={() => props.increment(10)}>Increment</button>
      <button onClick={() => props.decrement()}>Decrement</button>
    </div>
  );
};

const mapStateToProps = (state) => {
  return {
    counter: state,
  };
};

export default connect(mapStateToProps, { increment, decrement })(App);
```

7. **Sử dụng state và action:** Với cấu hình trên, bạn có thể sử dụng `state` counter và gọi các `actions` increment và decrement trong component App.

# Ví dụ 2: Ứng dụng counter sử dụng useSelector & useDispatch thay thế connect

Tương tự như các bước 1, 2, 3, 4, 5 của Ví dụ 1, không thay đổi. Ví dụ 2 này chỉ thay đổi các kết nối store để quan sát state và dispatch actions thông qua useSelect và useDispatch. Vậy, bước 6 và 7 thay đổi như sau:

6. **Kết nối React Components:** Trong file src/App.js, sử dụng `useSelector` và `useDispatch` để kết nối component với Redux:

```jsx
import React from "react";
import { useSelector, useDispatch } from "react-redux";
import { increment, decrement } from "./actions";

const App = () => {
  const counter = useSelector((state) => state);
  const dispatch = useDispatch();

  return (
    <div>
      <h1>Counter: {counter}</h1>
      <button onClick={() => dispatch(increment(10))}>Increment</button>
      <button onClick={() => dispatch(decrement())}>Decrement</button>
    </div>
  );
};

export default App;
```

7. **Sử dụng trạng thái và hành động:** Với cấu hình trên, có thể sử dụng `state` counter thông qua `useSelector` và gửi các `actions` increment và decrement thông qua `useDispatch` trong component App.

# DevTool

Để gắn Redux DevTools vào ứng dụng Redux của bạn, bạn cần làm hai bước:

1. **Cài đặt Redux DevTools Extension:** Đầu tiên, cài đặt Redux DevTools Extension cho trình duyệt đang sử dụng. Truy cập vào trang web của tiện ích và tải extension cho trình duyệt (Ví dụ: Redux DevTools cho Chrome).

2. **Cấu hình store trong ứng dụng:** Sau khi đã cài đặt extension, cần cấu hình cửa hàng Redux của mình để kết nối với Redux DevTools. Điều này thường được thực hiện trong file store.js hoặc tương tự.

```jsx
import { createStore } from "redux";
import rootReducer from "./reducers";

const store = createStore(
  rootReducer,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
);

export default store;
```

Trong đoạn code trên đã sử dụng `window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()` để kết nối với Redux DevTools Extension. Điều này cho phép extensions kết nối vào store của Redux và theo dõi states và actions của ứng dụng.

# Middleware

- `Middleware` là một lớp trung gian nằm giữa `dispatch(action)` và `reducers`, sẽ chặn action thực thi, sau đó mới truyền vào reducers. Nó cho phép chúng ta thực hiện các công việc như xử lý `side effects`, `gọi API`, `logging`, `routing`, và `quản lý trạng thái bất đồng bộ`.

- Middleware sử dụng kỹ thuật currying theo cú pháp sau:

```jsx
// store: là redux store
// next: là một function, Middleware cần gọi next(action) để cho phép action thực thi, nếu không thì action sẽ bị chặn lại ở Middleware không cho đi vào reducers
// action: action object vừa được dispatch

const middleware = (store) => (next) => (action) => {
  // middleware logic
};
```

- Có thể cài đặt nhiều middleware theo thứ tự, nếu middleware nào không gọi `next function` thì action đó bị chặn và không chạy middleware kế tiếp

- `Thunk` là một middleware dùng cho việc thực thi side effects do team Redux phát triển:
  - Thunk sẽ chặn những `action` có type là `function`, Thunk giúp truyền store.dispatch vào `action` và trigger thực thi `action`. Khi `action` xử lý side effect xong và có giá trị cần thiết => lúc này `dispatch(action)` khác có type là `plain object` để update state
  - Có thể truyền callbacks(onSuccess, onError, ...) vào để xử lý các trường hợp của bất đồng bộ

## Ví dụ: Dispatch function action call API get Dog image

1. **Set up middleware trong store**:

```jsx
import { applyMiddleware, combineReducers, createStore } from "redux";
import counterReducer from "./reducers/counterReducer";
import dogReducer from "./reducers/dogReducer";

const reducers = combineReducers({
  counter: counterReducer,
  dog: dogReducer,
});

const thunkMiddleware = (store) => (next) => (action) => {
  if (typeof action === "function") {
    action(store.dispatch);
    return;
  }
  next(action);
};

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;

const store = createStore(
  reducers,
  undefined,
  composeEnhancers(applyMiddleware(thunkMiddleware))
);

export default store;
```

- Khai báo một biến `composeEnhancers` giá trị ban đầu là window.**REDUX_DEVTOOLS_EXTENSION_COMPOSE**. Biến này sẽ được gán giá trị của window.**REDUX_DEVTOOLS_EXTENSION_COMPOSE** nếu extension đã được cài đặt trên trình duyệt, hoặc sẽ là hàm `compose` mặc định từ Redux nếu extension không tồn tại. Điều này giúp chúng ta xác định xem liệu Redux DevTools Extension có sẵn hay không.

- Trong phần khởi tạo Redux store, sử dụng hàm `createStore` và truyền các tham số như sau:
  - **reducers:** Các reducers của ứng dụng.
  - **undefined:** Giá trị ban đầu của state.
  - **composeEnhancers(applyMiddleware(thunkMiddleware)):** Sử dụng `composeEnhancers` để kết hợp `middleware` và `Redux DevTools Extension` (nếu có).

2. **Tạo dogReducer.js**:

```jsx
const dogReducer = (state = "", action) => {
  switch (action.type) {
    case "UPDATE_DOG":
      return action.payload;
    default:
      return state;
  }
};

export default dogReducer;
```

3. **Tạo dogAction.js**:

```jsx
export const updateDog = (payload) => {
  return {
    type: "UPDATE_DOG",
    payload,
  };
};

export const fetchRandomDog = () => {
  return async (dispatch) => {
    try {
      const response = await fetch("https://dog.ceo/api/breeds/image/random");
      const data = await response.json();
      if (data?.status === "success") {
        dispatch(updateDog(data));
      } else {
        throw "error";
      }
    } catch (error) {
      dispatch(updateDog(null));
    }
  };
};
```

4. **Dispatch Action**

```jsx
function App() {
  const dog = useSelector((state) => state.dog);
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(fetchRandomDog());
  }, []);

  console.log("dog", dog);

  return (
    <div>
      {dog?.message ? (
        <img src={dog.message} atl="" />
      ) : (
        <p>Không tìm thấy ảnh cún nào!</p>
      )}
    </div>
  );
}
```
