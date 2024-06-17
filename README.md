# 데이터베이스를 활용한 TODO앱 만들기

## 과제 미션

### 서버 설정

#### 데이터베이스 생성

```sql
-- 데이터 베이스 생성
CREATE DATABASE todo_app;

-- todos 테이블 생성
USE todo_app;

CREATE TABLE todos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    completed BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
)
```

#### 환경 변수 설정

```js
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=설정한 비밀번호를 입력하세요.
DB_NAME=데이터베이스 이름을 입력하세요.
DB_PORT=3306
```

#### 서버 실행

```sh
cd server
npm install
node index.js
```

`http://localhost:8080`에서 API 엔드포인트를 확인

### 클라이언트 요구사항

0. base URL

```js
// todos.routes.js 기본 엔드포인트
app.use('/api/todos', router);
```

1. 데이터베이스의 TodoItem 렌더링

```js
// TodoContainer.jsx
useEffect(() => {
  const fetchTodos = async () => {
    try {
      const response = await axios.get('/api/todos');
      setTodos(response.data);
    } catch (error) {
      console.log(error);
    }
  };
  fetchTodos();
}, []);
```

2. 새로운 TodoItem 생성

```js
// TodoContainer.jsx
const createTodo = async () => {
  try {
    const response = await axios.post('/api/todos', {
      title: '새할일',
      completed: false,
    });
    const newTodoItem = await axios.get('/api/todos');
    setTodos(newTodoItem.data);
    console.log(newTodoItem.data);
  } catch (error) {
    console.log(error);
  }
};
```

3. TodoItem 제목 변경

```js
// TodoItem.jsx
// 반복되는 렌더링 함수 별도로 생성하여 재사용
const render = async () => {
  const getTodos = await axios.get('/api/todos');
  setTodos(getTodos.data);
};

// TodoItem 제목 변경 함수
const startEditing = (todo) => {
  setEditingTodo(todo.id);
  setNewTitle(todo.title || '');
};
const saveEdit = async (id) => {
  console.log(newTitle);
  console.log(todo.completed);
  try {
    const response = await axios.put(`/api/todos/${id}`, { title: newTitle, completed: todo.completed });
    setEditingTodo(null);
    render();
  } catch (error) {
    console.log(error);
  }
};
```

4. TodoItem 완료 여부 변경

```js
// TodoItem.jsx
const updateTodo = async (id, completed) => {
  const response = await axios.put(`/api/todos/${id}`, {
    title: todo.title,
    completed: completed ? 1 : 0,
  });
  render();
};
```

5. TodoItem 삭제

```js
// TodoItem.jsx
const deleteTodo = async (id) => {
  const response = await axios.delete(`/api/todos/${id}`);
  render();
};
```

#### 데이터베이스의 TodoItem 렌더링

#### 새로운 TodoItem 생성

#### TodoItem 제목 변경

#### TodoItem 완료 여부 변경

#### TodoItem 삭제

최종 폴더 구조 예시

```
project-root
│
├── server
│   ├── index.js               # 서버의 진입점 파일, Express 서버를 설정하고 실행
│   ├── db.js                  # 데이터베이스 연결 설정 파일
│   ├── .env                   # 환경 변수 설정 파일
│   ├── routes
│   │   └── todos.routes.js    # Todo 관련 API 라우트 정의
│   └── controllers
│       └── todos.controller.js# Todo 관련 API 핸들러 함수 정의
│
├── src
│   ├── components
│   │   ├── TodoContainer.jsx  # Todo 리스트를 보여주는 컨테이너 컴포넌트
│   │   ├── TodoItem.jsx       # 개별 Todo 아이템을 보여주는 컴포넌트
│   │   ├── ButtonContainer.jsx# 버튼을 포함하는 컨테이너 컴포넌트
│   │   ├── TodoContainer.css  # TodoContainer 스타일 파일
│   │   ├── TodoItem.css       # TodoItem 스타일 파일
│   │   └── ButtonContainer.css# ButtonContainer 스타일 파일
│   ├── App.jsx                # 애플리케이션의 루트 컴포넌트
│   ├── main.jsx               # React 애플리케이션 진입점 파일
│   └── index.css              # 글로벌 스타일 파일
│
├── package.json               # 프로젝트 메타데이터 및 의존성 목록
└── vite.config.js             # Vite 설정 파일
```
