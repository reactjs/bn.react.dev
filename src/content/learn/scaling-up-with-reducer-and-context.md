---
title: Scaling Up with Reducer and Context
---

<Intro>

Reducers একটি কম্পোনেন্টের state আপডেট লজিক সংক্ষেপণ করতে সাহায্য করে। Context আপনাকে অন্যান্য কম্পোনেন্টের গভীরে তথ্য পাঠানোর সুযোগ দেয়। আপনি reducers এবং context দুটি একসাথে সংমিলিত করে একটি জটিল স্ক্রীনের state ব্যবস্থাপনা করতে পারেন।

</Intro>

<YouWillLearn>

* কিভাবে reducer কে context এর সাথে সংযুক্ত করতে হয়।
* কিভাবে state এবং dispatch কে props এর মাধ্যমে পাঠানো থেকে বিরত থাকা যায়।
* কিভাবে context এবং state এর যুক্তিকে ভিন্ন ফাইলে রাখা যায়।

</YouWillLearn>

## Context এর সাথে reducer এর সংযুক্তি {/*combining-a-reducer-with-context*/}

[Reducers এর সাথে পরিচিতি](/learn/extracting-state-logic-into-a-reducer) এই উদাহরণে, state কে reducer ব্যবস্থাপনা করেছে। Reducer ফাংশনটি সকল state হালানাগাদ যুক্তিসমূহ ধারন করে এবং একে ফাইলের একদম শেষে ডিক্লেয়ার করা হয়।

<Sandpack>

```js App.js
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId
    });
  }

  return (
    <>
      <h1>Day off in Kyoto</h1>
      <AddTask
        onAddTask={handleAddTask}
      />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

let nextId = 3;
const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js AddTask.js
import { useState } from 'react';

export default function AddTask({ onAddTask }) {
  const [text, setText] = useState('');
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        onAddTask(text);
      }}>Add</button>
    </>
  )
}
```

```js TaskList.js
import { useState } from 'react';

export default function TaskList({
  tasks,
  onChangeTask,
  onDeleteTask
}) {
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task
            task={task}
            onChange={onChangeTask}
            onDelete={onDeleteTask}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ task, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            onChange({
              ...task,
              text: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={e => {
          onChange({
            ...task,
            done: e.target.checked
          });
        }}
      />
      {taskContent}
      <button onClick={() => onDelete(task.id)}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
```

</Sandpack>

একটি Reducer ইভেন্ট হ্যান্ডলারগুলি ছোট এবং সংক্ষিপ্ত রাখতে সাহায্য করে । তবে, আপনার অ্যাপ্লিকেশন বড় হতে শুরু করলে, আপনি আরও একটি সমস্যায় পড়ে যেতে পারেন । **বর্তমানে, `tasks` state এবং `dispatch` ফাংশনটি শুধুমাত্র শীর্ষ-স্তরের `TaskApp` কম্পোনেন্টে পাওয়া যাচ্ছে।** অন্য কম্পোনেন্টকে টাস্কের তালিকা পড়তে অথবা তা পরিবর্তন করতে দিতে হলে, আপনাকে বর্তমান state এবং তা পরিবর্তন করার ইভেন্ট হ্যান্ডলারগুলি explicit ভাবে props হিসেবে [পাঠাতে](/learn/passing-props-to-a-component) হবে।

উদাহরণস্বরূপ, `TaskApp` টাস্কের তালিকা এবং ইভেন্ট হ্যান্ডলারগুলি `TaskList` এ পাঠিয়ে দেয়:

```js
<TaskList
  tasks={tasks}
  onChangeTask={handleChangeTask}
  onDeleteTask={handleDeleteTask}
/>
```

এবং `TaskList` ইভেন্ট হ্যান্ডলারগুলোকে `Task` এ পাঠিয়ে দেয়ঃ

```js
<Task
  task={task}
  onChange={onChangeTask}
  onDelete={onDeleteTask}
/>
```

একটি ছোট উদাহরণে এটি ভালো কাজ করে, কিন্তু যদি এর মাঝে আপনার দশ বা শতাধিক কম্পোনেন্ট থাকে, তাহলে সকল state এবং ফাংশনকে পাঠানো অনেক বিরক্তিকর হতে পারে।

এই কারণে, props এর মাধমে পাঠানোর বিকল্প হিসেবে, আপনি সমস্ত `tasks` স্টেট এবং  `dispatch` ফাংশনকে [context এর মধ্যে](/learn/passing-data-deeply-with-context) রাখতে পারেন। **এইভাবে, `TaskApp` এর নীচে যেকোনো কম্পোনেন্ট রুটে আপনি "prop drilling" এর পুনরাবৃত্তি ছাড়াই task পড়তে এবং একশনকে dispatch করতে পারবেন ।**

যেভাবে আপনি reducer এবং  context এর সংযুক্তি করতে পারেনঃ

1. Context **তৈরি** করুন।
2. state এবং dispatch কে Context এর ভেতরে **রাখুন**।
3. Context কে যেকোনো কম্পোনেন্ট রুটে **ব্যবহার** করুন।

### ধাপ ১: Context তৈরি করুন {/*step-1-create-the-context*/}

`useReducer` হুক আপনাকে বর্তমান `tasks` এবং তা আপডেট করার জন্য `dispatch` ফাংশনকে রিটার্ন করে।

```js
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
```

তাদেরকে ট্রি-এর নিচে পাঠানোর জন্য আপনি দুটি ভিন্ন context [তৈরি](/learn/passing-data-deeply-with-context#step-2-use-the-context) করবেন ।

- `TasksContext` বর্তমান tasks তালিকা প্রদান করে।
- `TasksDispatchContext` একটি ফাংশন প্রদান করে যা কম্পোনেন্টকে একশন dispatch করতে দেয়।

এদেরকে একটি আলাদা ফাইলে এক্সপোর্ট করুন যাতে আপনি পরবর্তীতে অন্য ফাইলে ইম্পোর্ট করতে পারেন:

<Sandpack>

```js App.js
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId
    });
  }

  return (
    <>
      <h1>Day off in Kyoto</h1>
      <AddTask
        onAddTask={handleAddTask}
      />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

let nextId = 3;
const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js TasksContext.js active
import { createContext } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```

```js AddTask.js
import { useState } from 'react';

export default function AddTask({ onAddTask }) {
  const [text, setText] = useState('');
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        onAddTask(text);
      }}>Add</button>
    </>
  )
}
```

```js TaskList.js
import { useState } from 'react';

export default function TaskList({
  tasks,
  onChangeTask,
  onDeleteTask
}) {
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task
            task={task}
            onChange={onChangeTask}
            onDelete={onDeleteTask}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ task, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            onChange({
              ...task,
              text: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={e => {
          onChange({
            ...task,
            done: e.target.checked
          });
        }}
      />
      {taskContent}
      <button onClick={() => onDelete(task.id)}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
```

</Sandpack>

এখানে আপনি `null` কে ডিফল্ট ভ্যালু হিসেবে দুটি context এ পাঠাচ্ছেন। আসল মানগুলি `TaskApp` এর মাধ্যমে সরাসরি প্রদান হবে।


### ধাপ ২: State এবং dispatch কে context এর ভেতরে রাখুন {/*step-2-put-state-and-dispatch-into-context*/}

এখন আপনি দুটো context কে `TaskApp` কম্পোনেন্টে ইম্পোর্ট করতে পারেন। `useReducer()` এর রিটার্ন করা `tasks` এবং `dispatch` কে গ্রহণ করুন এবং এদেরকে নিচের সম্পূর্ন ট্রিতে [প্রদান করুন](/learn/passing-data-deeply-with-context#step-3-provide-the-context):

```js {4,7-8}
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
  // ...
  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        ...
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}
```

এখন, আপনি তথ্যকে props এবং context উভয়ের মাধ্যমে পাঠাতে পারবেনঃ

<Sandpack>

```js App.js
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId
    });
  }

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        <h1>Day off in Kyoto</h1>
        <AddTask
          onAddTask={handleAddTask}
        />
        <TaskList
          tasks={tasks}
          onChangeTask={handleChangeTask}
          onDeleteTask={handleDeleteTask}
        />
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

let nextId = 3;
const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js TasksContext.js
import { createContext } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```

```js AddTask.js
import { useState } from 'react';

export default function AddTask({ onAddTask }) {
  const [text, setText] = useState('');
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        onAddTask(text);
      }}>Add</button>
    </>
  )
}
```

```js TaskList.js
import { useState } from 'react';

export default function TaskList({
  tasks,
  onChangeTask,
  onDeleteTask
}) {
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task
            task={task}
            onChange={onChangeTask}
            onDelete={onDeleteTask}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ task, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            onChange({
              ...task,
              text: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={e => {
          onChange({
            ...task,
            done: e.target.checked
          });
        }}
      />
      {taskContent}
      <button onClick={() => onDelete(task.id)}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
```

</Sandpack>

পরবর্তী ধাপে, আপনি prop পাঠানো মুছে ফেলবেন। 

### ধাপ ৩: ট্রি এর যেকোনো জায়গায় context ব্যবহার করুন {/*step-3-use-context-anywhere-in-the-tree*/}

এখন আপনাকে আর task এর তালিকা অথবা event handlers কে ট্রি এর নিচে পাঠাতে হবেনা:

```js {4-5}
<TasksContext.Provider value={tasks}>
  <TasksDispatchContext.Provider value={dispatch}>
    <h1>Day off in Kyoto</h1>
    <AddTask />
    <TaskList />
  </TasksDispatchContext.Provider>
</TasksContext.Provider>
```

এর পরিবর্তে যেকোনো কম্পোনেন্ট যার task তালিকা দরকার হবে সে তা `TaskContext` থেকে পড়তে পারবে।

```js {2}
export default function TaskList() {
  const tasks = useContext(TasksContext);
  // ...
```
Task তালিকা হালনাগাদ করার জন্য যেকোনো কম্পোনেন্ট `dispatch` ফাংশনকে context থেকে পড়তে পারেন এবং call করতে পারেন।

```js {3,9-13}
export default function AddTask() {
  const [text, setText] = useState('');
  const dispatch = useContext(TasksDispatchContext);
  // ...
  return (
    // ...
    <button onClick={() => {
      setText('');
      dispatch({
        type: 'added',
        id: nextId++,
        text: text,
      });
    }}>Add</button>
    // ...
```

**`TaskApp` কম্পোনেন্ট কোনো event handlers কে নিচে পাঠায় না এবং `TaskList` কোনো event handlers কে `Task` কম্পোনেন্টেও পাঠায় না।** প্রতিটা কম্পোনেন্ট তার প্রয়োজনীয় context কে পড়েঃ

<Sandpack>

```js App.js
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        <h1>Day off in Kyoto</h1>
        <AddTask />
        <TaskList />
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js TasksContext.js
import { createContext } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```

```js AddTask.js
import { useState, useContext } from 'react';
import { TasksDispatchContext } from './TasksContext.js';

export default function AddTask() {
  const [text, setText] = useState('');
  const dispatch = useContext(TasksDispatchContext);
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        dispatch({
          type: 'added',
          id: nextId++,
          text: text,
        }); 
      }}>Add</button>
    </>
  );
}

let nextId = 3;
```

```js TaskList.js active
import { useState, useContext } from 'react';
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskList() {
  const tasks = useContext(TasksContext);
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task task={task} />
        </li>
      ))}
    </ul>
  );
}

function Task({ task }) {
  const [isEditing, setIsEditing] = useState(false);
  const dispatch = useContext(TasksDispatchContext);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            dispatch({
              type: 'changed',
              task: {
                ...task,
                text: e.target.value
              }
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={e => {
          dispatch({
            type: 'changed',
            task: {
              ...task,
              done: e.target.checked
            }
          });
        }}
      />
      {taskContent}
      <button onClick={() => {
        dispatch({
          type: 'deleted',
          id: task.id
        });
      }}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
```

</Sandpack>

**State টি এখনো টপ-লেভেল `TaskApp` কম্পোনেন্টেই অবস্থান করছে, `useReducer` এর ব্যবস্থাপনায়।** কিন্তু এর `tasks` এবং `dispatch` এখন ট্রিয়ের নিচের প্রতিটি কম্পোনেন্ট পাওয়া যাবে ইম্পোর্টিং এবং এই context কে ব্যবহারের মাধ্যমে।


## সকল সংযোগসমূহকে একটি ফাইলে সরানো {/*moving-all-wiring-into-a-single-file*/}

আপনার এটি করার দরকার নেই, কিন্তু আপনি কম্পোনেন্টকে আরো সাজানোর জন্য reducer এবং context উভয়কেই একটি ফাইলে সরিয়ে নিতে পারেন। বর্তমানে, `TaskContext.js` এ কেবল দুটি context ডিক্লেয়ারেশন রয়েছেঃ

```js
import { createContext } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```
এই ফাইলটিতে এখন জটলা বেঁধে যাবে! আপনি reducer কে একই ফাইলে সরাবেন। এরপর আপনি একটি নতুন `TaskProvider` কম্পোনেন্ট একই ফাইলে ডিক্লেয়ার করবেন। এই কম্পোনেন্ট সকল অংশকে একীভূত করবে।

1. এটি state কে reducer দিয়ে পরিচালনা করবে।
2. এটি উভয় context কে নিচের কম্পোনেন্টে পাঠাবে।
3. এটি [`children` কে prop হিসেবে নেয়](/learn/passing-props-to-a-component#passing-jsx-as-children) যাতে আপনি এতে JSX পাঠাতে পারেন।

```js
export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}
```

**এটি আপনার `TaskApp` থেকে সকল জটিলতা এবং সংযোগকে সরিয়ে দেয়:**

<Sandpack>

```js App.js
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { TasksProvider } from './TasksContext.js';

export default function TaskApp() {
  return (
    <TasksProvider>
      <h1>Day off in Kyoto</h1>
      <AddTask />
      <TaskList />
    </TasksProvider>
  );
}
```

```js TasksContext.js
import { createContext, useReducer } from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);

export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js AddTask.js
import { useState, useContext } from 'react';
import { TasksDispatchContext } from './TasksContext.js';

export default function AddTask() {
  const [text, setText] = useState('');
  const dispatch = useContext(TasksDispatchContext);
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        dispatch({
          type: 'added',
          id: nextId++,
          text: text,
        }); 
      }}>Add</button>
    </>
  );
}

let nextId = 3;
```

```js TaskList.js
import { useState, useContext } from 'react';
import { TasksContext, TasksDispatchContext } from './TasksContext.js';

export default function TaskList() {
  const tasks = useContext(TasksContext);
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task task={task} />
        </li>
      ))}
    </ul>
  );
}

function Task({ task }) {
  const [isEditing, setIsEditing] = useState(false);
  const dispatch = useContext(TasksDispatchContext);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            dispatch({
              type: 'changed',
              task: {
                ...task,
                text: e.target.value
              }
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={e => {
          dispatch({
            type: 'changed',
            task: {
              ...task,
              done: e.target.checked
            }
          });
        }}
      />
      {taskContent}
      <button onClick={() => {
        dispatch({
          type: 'deleted',
          id: task.id
        });
      }}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
```

</Sandpack>

আপনি ফাংশন এক্সপোর্টও করতে পারেন যেটা `TasksContext.js` এর context কে _ব্যবহার_ করেন ঃ

```js
export function useTasks() {
  return useContext(TasksContext);
}

export function useTasksDispatch() {
  return useContext(TasksDispatchContext);
}
```

যখন একটি কম্পোনেন্ট এর context পড়ার প্রয়োজন হয়, এটি ফাংশনের মাধ্যমে তা করতে পারে ঃ

```js
const tasks = useTasks();
const dispatch = useTasksDispatch();
```

এটি আচরণকে কোনোভাবেই পরিবর্তন করেনা, কিন্তু এটি আপনাকে পরবর্তীতে এই ফাংশনে context কে ভাগ করতে দেয় অথবা কিছু যুক্তি যোগ করতে দেয়। **এখন সকল context এবং reducer সংযোগসমূহ `TasksContext.js` এ আছে। এটি কম্পোনেন্টকে পরিচ্ছন্ন এবং গোছানো রাখে, কোথায় থেকে ডেটা পাচ্ছে তা নয় বরং তারা কি প্রদর্শন করে তাতে মনোযোগ দেয়ঃ**

<Sandpack>

```js App.js
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import { TasksProvider } from './TasksContext.js';

export default function TaskApp() {
  return (
    <TasksProvider>
      <h1>Day off in Kyoto</h1>
      <AddTask />
      <TaskList />
    </TasksProvider>
  );
}
```

```js TasksContext.js
import { createContext, useContext, useReducer } from 'react';

const TasksContext = createContext(null);

const TasksDispatchContext = createContext(null);

export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}

export function useTasks() {
  return useContext(TasksContext);
}

export function useTasksDispatch() {
  return useContext(TasksDispatchContext);
}

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

const initialTasks = [
  { id: 0, text: 'Philosopher’s Path', done: true },
  { id: 1, text: 'Visit the temple', done: false },
  { id: 2, text: 'Drink matcha', done: false }
];
```

```js AddTask.js
import { useState } from 'react';
import { useTasksDispatch } from './TasksContext.js';

export default function AddTask() {
  const [text, setText] = useState('');
  const dispatch = useTasksDispatch();
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        dispatch({
          type: 'added',
          id: nextId++,
          text: text,
        }); 
      }}>Add</button>
    </>
  );
}

let nextId = 3;
```

```js TaskList.js active
import { useState } from 'react';
import { useTasks, useTasksDispatch } from './TasksContext.js';

export default function TaskList() {
  const tasks = useTasks();
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task task={task} />
        </li>
      ))}
    </ul>
  );
}

function Task({ task }) {
  const [isEditing, setIsEditing] = useState(false);
  const dispatch = useTasksDispatch();
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            dispatch({
              type: 'changed',
              task: {
                ...task,
                text: e.target.value
              }
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Save
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>
          Edit
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={e => {
          dispatch({
            type: 'changed',
            task: {
              ...task,
              done: e.target.checked
            }
          });
        }}
      />
      {taskContent}
      <button onClick={() => {
        dispatch({
          type: 'deleted',
          id: task.id
        });
      }}>
        Delete
      </button>
    </label>
  );
}
```

```css
button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
```

</Sandpack>

আপনি `TasksProvider` কে স্ক্রীন এর একটি অংশ হিসেবে চিন্তা করতে পারেন যে জানে কিভাবে tasks এর সাথে আচরন করতে হয়, `useTasks` এদেরকে পড়ার একটি উপায় এবং `useDispatch` তাদেরকে ট্রি এর নিচের যেকোন কম্পোনেন্ট থেকে আপডেট করার একটি উপায়।

<Note>

`useTasks` এবং `useTasksDispatch` এর মত ফাংশনগুলি *[কাস্টম হুক](/learn/reusing-logic-with-custom-hooks)* হিসেবে চিহ্নিত হয়। আপনার ফাংশনটির নাম যদি use দিয়ে শুরু হয় তবে তা একটি কাস্টম হুক হিসেবে বিবেচিত হয়। এটি আপনাকে এর মধ্যে অন্য হুক ব্যবহার করতে দেয় যেমন `useContext`.

</Note>

যখন আপনার অ্যাপ্লিকেশন বাড়তে থাকে, আপনার এরকম অনেক context-reducer জোড়া থাকতে পারে। এটি আপনার অ্যাপ্লিকেশন স্কেল করার একটি শক্তিশালী উপায় এবং আপনি যখনই ট্রির গভীরে ডেটা অ্যাক্সেস করতে চান তখন অধিক কাজ না করেই স্টেট [উঠাতে পারে](/learn/sharing-state-between-components)।

<Recap>

- আপনি Reducer সঙ্গে context যোগ করে যেকোনো কোম্পোনেন্টকে এর উপরের state পড়তে এবং আপডেট করতে দিতে পারেন।
- state এবং dispatch ফাংশনকে নীচের কোম্পোনেন্টকে প্রদান করতে:
  1. দুটি কনটেক্সট তৈরি করুন (state এবং dispatch ফাংশনের জন্য)।
  2. যে কোম্পোনেন্ট রিডিউসারটি ব্যবহার করে, তার থেকে উভয় কনটেক্সট প্রদান করুন।
  3. যে কোম্পোনেন্ট এর তাদের পড়ার দরকার সেখান থেকে যেকোনো কনটেক্সট ব্যবহার করুন।
- আপনি কম্পোনেন্টগুলিকে আরো গোছাতে পারেন এদেরকে একটি ফাইলে সরিয়ে ফেলার মাধ্যমে।
  - আপনি `TasksProvider` এর মতো কম্পোনেন্টকে এক্সপোর্ট করতে পারেন যারা context প্রদান করেন।
  - আপনি `useTasks` এবং `useTasksDispatch` এর মতো কাস্টম হুক এক্সপোর্ট করতে পারেন পড়ার জন্য।
  - আপনি অনেক context-reducer জোড়া রাখতে পারেন আপনার app এ।

</Recap>
