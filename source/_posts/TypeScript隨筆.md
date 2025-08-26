---
title: TypeScript隨筆
date: 2025-03-28 10:28:32
tags:
---

又名解讀chatgpt給我的那些程式碼

目前打算將各種文件分成這樣
```bash
src/
├── App.tsx         // 主應用結構
├── components/
│   ├── TodoList.tsx    // 左側 Todo 列表組件
│   ├── TodoSearch.tsx  // 搜索組件
│   ├── TodoForm.tsx    // 新增 Todo 表單
│   └── TodoDetails.tsx // 右側 Todo 詳細信息組件
├── types/
│   └── Todo.ts         // Todo 類型定義
└── hooks/
    └── useTodos.ts     // 管理 Todo 邏輯的自定義 Hook
```

```typescript
const App: React.FC = () => {
  // 使用 useState 鉤子定義三個狀態
  const [todos, setTodos] = useState<Todo[]>([]);  // Todo 項目列表
  const [selectedTodo, setSelectedTodo] = useState<Todo | null>(null);  // 當前選中的 Todo 項目
  const [search, setSearch] = useState('');  // 搜索框的文字

  // 使用 useEffect 鉤子在組件掛載時從後端獲取 Todo 數據
  useEffect(() => {
    // 向本地後端 API 發送 GET 請求
    fetch('http://localhost:8000/api/tasks/')
      .then((response) => response.json())  // 將響應轉換為 JSON
      .then((data) => setTodos(data));  // 使用獲取的數據更新 todos 狀態
  }, []); // 空依賴數組意味著只在組件首次渲染時執行
```


sreach功能


初版實現方式:
```typescript
//給input加標籤

<input type="text" id="search" class="search" placeholder="ToDoを検索...">


//DOM元素
const searchInput = document.getElementById("search");


//監聽
searchInput.addEventListener("input", function() {
    const searchText = searchInput.value.toLowerCase();
    let hasResults = false;
    
    todoItems.forEach(item => {
        const title = item.querySelector(".todo-title").textContent.toLowerCase();
        if (title.includes(searchText)) {
            item.style.display = "flex";
            hasResults = true;
        } else {
            item.style.display = "none";
        }
    });
    // 顯示或隱藏"無結果"消息
    noResults.style.display = hasResults ? "none" : "block";
});

```


二版實現




诶不是 可以這樣嗎


後端定義
```py
    @action(detail=True, methods=['patch'], url_path=r'completed(?:/(?P<subtask_pk>\d+))?')
    def completed(self, request, pk, subtask_pk=None):
        task = self.get_object()
        if subtask_pk:
            try:
                subtask = task.subtasks.get(pk=subtask_pk)
            except SubTask.DoesNotExist:
                return Response({'error': 'SubTask not found'}, status=404)
            return self.mark_as_completed(subtask)
        else:
            return self.mark_as_completed(task)
```
前端請求
```tsx
  const handleToggleComplete = (id: number) => {
    const updatedTodos = todos.map((todo) =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    );
    setTodos(updatedTodos);

    fetch(`http://localhost:8000/api/tasks/${id}/`, {
      method: 'PATCH',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ completed: !todos.find(todo => todo.id === id)?.completed }),
    });
  } 
  
```



```bash
[28/Mar/2025 08:12:54] "PATCH /api/tasks/1/ HTTP/1.1" 200 206
```

結論:我是小丑