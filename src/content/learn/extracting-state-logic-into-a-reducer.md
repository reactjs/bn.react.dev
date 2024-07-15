---
title: State Logic কে একটি Reducer এ স্থানান্তর করা
---

<Intro>

একাধিক event handler এ ছড়িয়ে থাকা একাধিক state update ওয়ালা কম্পোনেন্টগুলো দুঃসহ হয়ে যেতে পারে। এসব ক্ষেত্রে, আপনি সকল state update logic কে আপনার কম্পোনেন্টের বাইরে একটিমাত্র function এ একত্রিত করতে পারেন, যাকে বলা হয় _reducer।_

</Intro>

<YouWillLearn>

- reducer function বলতে কী বুঝায় 
- কিভাবে `useState` কে গুছিয়ে `useReducer` এ পরিণত করা যায়
- কখন reducer ব্যবহার করতে হয়
- কীভাবে একে ভালভাবে লিখতে হয়

</YouWillLearn>

## State logic কে একটি reducer এ একত্র করুন {/*consolidate-state-logic-with-a-reducer*/}

ধীরে ধীরে যখন আপনার কম্পোনেন্টগুলোর জটিলতা বাড়তে থাকে, তখন এক নজর দেখে এটা বোঝা কঠিন হয়ে যেতে পারে যে কতোনা উপায়ে একটা কম্পোনেন্টের state আপডেট হতে পারে। উদাহরণস্বরূপ, নিচের `TaskApp` কম্পোনেন্টটি `tasks` নামক array কে state হিসেবে ধারণ করে, আর কোনো task কে add, edit, remove করার জন্য তিনটি ভিন্ন ভিন্ন event handler এর ব্যবহার করে:

<Sandpack>

```js App.js
import { useState } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

export default function TaskApp() {
  const [tasks, setTasks] = useState(initialTasks);

  function handleAddTask(text) {
    setTasks([
      ...tasks,
      {
        id: nextId++,
        text: text,
        done: false,
      },
    ]);
  }

  function handleChangeTask(task) {
    setTasks(
      tasks.map((t) => {
        if (t.id === task.id) {
          return task;
        } else {
          return t;
        }
      })
    );
  }

  function handleDeleteTask(taskId) {
    setTasks(tasks.filter((t) => t.id !== taskId));
  }

  return (
    <>
      <h1>Prague itinerary</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: 'Visit Kafka Museum', done: true},
  {id: 1, text: 'Watch a puppet show', done: false},
  {id: 2, text: 'Lennon Wall pic', done: false},
];
```

```js AddTask.js hidden
import { useState } from 'react';

export default function AddTask({onAddTask}) {
  const [text, setText] = useState('');
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={(e) => setText(e.target.value)}
      />
      <button
        onClick={() => {
          setText('');
          onAddTask(text);
        }}>
        Add
      </button>
    </>
  );
}
```

```js TaskList.js hidden
import { useState } from 'react';

export default function TaskList({tasks, onChangeTask, onDeleteTask}) {
  return (
    <ul>
      {tasks.map((task) => (
        <li key={task.id}>
          <Task task={task} onChange={onChangeTask} onDelete={onDeleteTask} />
        </li>
      ))}
    </ul>
  );
}

function Task({task, onChange, onDelete}) {
  const [isEditing, setIsEditing] = useState(false);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={(e) => {
            onChange({
              ...task,
              text: e.target.value,
            });
          }}
        />
        <button onClick={() => setIsEditing(false)}>Save</button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>Edit</button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={(e) => {
          onChange({
            ...task,
            done: e.target.checked,
          });
        }}
      />
      {taskContent}
      <button onClick={() => onDelete(task.id)}>Delete</button>
    </label>
  );
}
```

```css
button {
  margin: 5px;
}
li {
  list-style-type: none;
}
ul,
li {
  margin: 0;
  padding: 0;
}
```

</Sandpack>

এর প্রতিটি event handler state কে আপডেট করার জন্য `setTasks` কে call করে। ধীরে ধীরে যখন এ কম্পোনেন্টটি আকারে বাড়তে থাকবে, তখন সাথে সাথে এর ভিতরকার state logic ও বাড়তে থাকবে এবং জটিলতর হতে থাকবে। এই জটিলতা কমাতে এবং আপনার সব state logic একটি সহজে-পাওয়া-যায় এমন জায়গায় রাখতে, আপনি ঐসব state logic কে আপনার কম্পোনেন্টের বাইরে একটি function এ স্থানান্তর করতে পারেন, যে function টিকে বলা হয় **"reducer".**

Reducer হলো state হ্যান্ডেল করার একটি বিকল্প পদ্ধতি। আপনি `useState` থেকে `useReducer` এ তিনটি ধাপে স্থানান্তর করতে পারেন:

1. state কে set করার বদলে action কে **dispatch করতে শুরু করুন**।
2. একটি reducer function **লিখুন**।
3. reducer টিকে আপনার কম্পোনেন্ট থেকে **ইউজ করুন**।

### ধাপ ১: State কে set করার বদলে action কে dispatch করতে শুরু করুন {/*step-1-move-from-setting-state-to-dispatching-actions*/}

State কে set করার মাধ্যমে আপনার event handler গুলো বর্তমানে নির্ধারণ করছে যে কী করতে হবে:

```js
function handleAddTask(text) {
  setTasks([
    ...tasks,
    {
      id: nextId++,
      text: text,
      done: false,
    },
  ]);
}

function handleChangeTask(task) {
  setTasks(
    tasks.map((t) => {
      if (t.id === task.id) {
        return task;
      } else {
        return t;
      }
    })
  );
}

function handleDeleteTask(taskId) {
  setTasks(tasks.filter((t) => t.id !== taskId));
}
```

এখন সব state সেট করার logic দূর করে দিন। এখন আপনার কাছে যা বাকি থাকবে তা হলো:

- ইউজার যখন "Add" প্রেস করে তখন call করা হয় `handleAddTask(text)`।
- ইউজার যখন "Save" প্রেস করে কিংবা কোনো task কে toggle (বা edit) করে তখন call করা হয় `handleChangeTask(task)`।
- ইউজার যখন "Delete" প্রেস করে তখন call করা হয় `handleDeleteTask(taskId)`। 

Reducer দিয়ে state ম্যানেজ করা, state সেট করা থেকে কিছুটা ভিন্ন জিনিস। React কে state সেট করার মাধ্যমে "কী করতে হবে" না বলে, আপনি আপনার event handler গুলো থেকে "action" গুলোকে dispatch করার মাধ্যমে ঠিক করে দেন "ইউজার এইমাত্র কী করলো"। (আর state update logic অন্য আরেক জায়গায় থাকবে!) তাই একটি event handler এর মাধ্যমে "`tasks` সেট করার" পরিবর্তে, আপনি "একটি task add/change/delete করার" action(কাজ) dispatch করবেন। আর এই পদ্ধতিটি ইউজারের আকাঙ্ক্ষাকে বেশি বর্ণনা করে। 

```js
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
    task: task,
  });
}

function handleDeleteTask(taskId) {
  dispatch({
    type: 'deleted',
    id: taskId,
  });
}
```

আপনি `dispatch` এর কাছে যে object টি pass করেন, তাকে একটি "action" বলে:

```js {3-7}
function handleDeleteTask(taskId) {
  dispatch(
    // "action" object:
    {
      type: 'deleted',
      id: taskId,
    }
  );
}
```

এটি একটি সাধারণ JavaScript object। এর মধ্যে কী রাখতে হবে সেটা আপনার উপর, তবে স্বাভাবিকভাবে এর মধ্যে _কী ঘটলো(what happened)_ সে ব্যপারে ন্যূনতম ইনফর্মেশন থাকতে হবে। (আর আপনি `dispatch` ফাংশনটিকে পরবর্তী একটি ধাপে যুক্ত করবেন।)

<Note>

একটি object যেকোনো আকৃতির হতে পারে।

তবে নিয়ম হচ্ছে, এই object কে `type` হিসেবে একটি string পাস করা যে type টি ব্যাখ্যা করে "কি ঘটলো" তা, আর যেকোনো অতিরিক্ত ইনফর্রমেশন অন্যান্য ফিল্ড গুলোতে পাস করে দেয়াই সাধারণ প্রচলন। `type` একটি কম্পোনেন্টের জন্য নির্ধারিত, তাই এই উদাহরণে `'added'` অথবা `'added_task'` নাম দিলেই চলবে। এমন একটি নাম দেয়ার চেষ্টা করুন যে নামটি বলে দেয় কী ঘটলো!

```js
dispatch({
  // specific to component
  type: 'what_happened',
  // other fields go here
});
```

</Note>

### ধাপ ২: একটি reducer function লিখুন {/*step-2-write-a-reducer-function*/}

একটি reducer function হলো যেখানে আপনি আপনার state লজিক রাখবেন। এটি দুটি argument নেয়, বর্তমান state এবং action অবজেক্ট, অতঃপর এটি পরবর্তী state কে return করেঃ

```js
function yourReducer(state, action) {
  // return next state for React to set
}
```

আপনি reducer থেকে যা return করবেন, React সেটিকে state হিসেবে সেট করে দিবে। 

এই উদাহরণে, state সেট করার লজিককে event handlers থেকে একটি reducer function এ সরাতে, আপনার:

1. বর্তমান state (`tasks`) কে প্রথম argument হিসেবে declare করতে হবে।
2. `action` অবজেক্টকে দ্বিতীয় argument হিসেবে declare করতে হবে।
3. reducer থেকে _পরবর্তী_ state কে return করতে হবে। (যেটিকে React পরবর্তী state হিসেবে সেট করবে)

সব state সেট করার লজিক reducer function এ সরানোর পর এমন দেখাবেঃ

```js
function tasksReducer(tasks, action) {
  if (action.type === 'added') {
    return [
      ...tasks,
      {
        id: action.id,
        text: action.text,
        done: false,
      },
    ];
  } else if (action.type === 'changed') {
    return tasks.map((t) => {
      if (t.id === action.task.id) {
        return action.task;
      } else {
        return t;
      }
    });
  } else if (action.type === 'deleted') {
    return tasks.filter((t) => t.id !== action.id);
  } else {
    throw Error('Unknown action: ' + action.type);
  }
}
```

যেহেতু reducer function টি state (`tasks`) কে একটি argument হিসেবে নিচ্ছে, আপনি একে **আপনার কম্পোনেন্টের বাইরে declare করতে পারবেন।** এটা indentation level কমিয়ে আনে এবং আপনার কোডকে পড়তে সহজ করে।

<Note>

উপরের কোডে if/else স্টেটমেন্ট ব্যবহৃত হয়েছে, কিন্তু reducer এর ভিতর [switch স্টেটমেন্ট](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/switch) ব্যাবহার করাটা প্রচলিত। ফলাফল একই থাকবে, কিন্তু switch স্টেটমেন্ট একনজরে পরাটা আরো সহজতর।

আমরা ডকুমেন্টেশনের বাকী অংশ জুড়ে এই প্রচলন অনুসারেই চালিয়ে যাবো:

```js
function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    case 'changed': {
      return tasks.map((t) => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

আমরা প্রত্যেক `case` ব্লককে `{` এবং `}` বক্র ব্র্যাকেটে আবদ্ধ করতে রিকমেন্ড করি যাতে ভিন্ন ভিন্ন `case` এর মধ্যে declare করা variable একে অপরের সাথে সাংঘর্ষিক না হয়। আর, একটি `case` সাধারণত একটি `return` দিয়ে শেষ হবে। যদি আপনি `return` করতে ভুলে যান, তাহলে (ঐ `case` এর) কোডটি "ভেদ করে" পরবর্তী `case` এ গিয়ে পড়বে, যেটা ত্রুটি ঘটাতে পারে!

যদি আপনি switch স্টেটমেন্ট এর ব্যাপারে এখনো কমফোর্টেবল না হয়ে থাকেন, if/else ব্যাবহার করায় কোনো সমস্যা নেই।

</Note>

<DeepDive>

#### Reducer কে কেনো এভাবে call করা হয়? {/*why-are-reducers-called-this-way*/}

যদিও reducer আপনার কম্পোনেন্টের ভিতরে কোডের পরিমাণ কমাতে পারে, কিন্তু reducer নাম দেয়ার পিছনে আসল রহস্য হচ্ছে [`reduce()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) অপারেশন, যেটি আপনি array এর উপর প্রয়োগ করতে পারেন।

`reduce()` অপারেশনটি আপনাকে একটি array এর একাধিক ভ্যালুকে "একত্র করে" একটি ভ্যালুতে নিয়ে আনার ক্ষমতা দেয়:

```
const arr = [1, 2, 3, 4, 5];
const sum = arr.reduce(
  (result, number) => result + number
); // 1 + 2 + 3 + 4 + 5
```

`reduce` কে আপনি যে ফাংশনটি পাস করেন তাকে বলা হয় "reducer"। এটা গ্রহণ করে _এখন অবধি রেজাল্ট_ এবং _বর্তমান item,_ তারপর এটা return করে _পরবর্তী রেজাল্ট।_  React reducer ও এর অনুরূপ: গ্রহণ করে _এখন অবধি state_ এবং _action_, এবং return করে _পরবর্তী state।_ এমন করে, সময়ের সাথে সেটি action সমূহকে কে state হিসেবে একত্র করে।

এমনকি আপনি `reduce()` মেথডটি দিয়েও একটি `initialState` এবং একটি `actions` এর array থেকে সর্বশেষ state বের করতে পারবেন, তার জন্য মেথডটিকে আপনার reducer ফাংশনটি পাস করতে হবে:

<Sandpack>

```js index.js active
import tasksReducer from './tasksReducer.js';

let initialState = [];
let actions = [
  {type: 'added', id: 1, text: 'Visit Kafka Museum'},
  {type: 'added', id: 2, text: 'Watch a puppet show'},
  {type: 'deleted', id: 1},
  {type: 'added', id: 3, text: 'Lennon Wall pic'},
];

let finalState = actions.reduce(tasksReducer, initialState);

const output = document.getElementById('output');
output.textContent = JSON.stringify(finalState, null, 2);
```

```js tasksReducer.js
export default function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    case 'changed': {
      return tasks.map((t) => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

```html public/index.html
<pre id="output"></pre>
```

</Sandpack>

আপনার নিজের এমনটা করার প্রয়োজন না হওয়ারই সম্ভাবনা বেশি, তবে এটা React যেভাবে করে দেয় তার মতোই!

</DeepDive>

### ধাপ ৩: আপনার কম্পোনেন্টে reducer টি ব্যাবহার করুন {/*step-3-use-the-reducer-from-your-component*/}

সবশেষে, আপনার `tasksReducer` টিকে আপনার কম্পোনেন্টের সাথে সংযুক্ত করে দিতে হবে। React থেকে `useReducer` হুকটি import করুন:

```js
import { useReducer } from 'react';
```

অতঃপর আপনি `useState` কে সরিয়ে দিতে পারেন:

```js
const [tasks, setTasks] = useState(initialTasks);
```

`useReducer` দিয়ে, ঠিক এভাবে:

```js
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
```

`useReducer` হুকটি অনেকটা `useState` মতো—আপনার অবশ্যই একে একটি initial state (স্টেটের প্রাথমিক ভ্যালু) পাস করতে হবে আর এটি return করে state এর ভ্যালু এবং state কে সেট করার একটি পদ্ধতি (এক্ষেত্রে, dispatch ফাংশন)। কিন্তু এটি (`useState` থেকে) একটু আলাদা।

`useReducer` হুকটি দুটি argument নেয়:

1. একটি reducer function
2. একটি initial state

আর এটি return করে:

1. একটি state ভ্যালু
2. একটি dispatch function (ইউজার actions কে reducer এর নিকট "dispatch বা প্রেরণ" করার জন্য)

এখন এটিকে পুরোপুরি সেট আপ করা হয়ে গেছে। এখানে, reducer টিকে component file এর নিচের দিকে declare করা হয়েছে:

<Sandpack>

```js App.js
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

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
      task: task,
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId,
    });
  }

  return (
    <>
      <h1>Prague itinerary</h1>
      <AddTask onAddTask={handleAddTask} />
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
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    case 'changed': {
      return tasks.map((t) => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: 'Visit Kafka Museum', done: true},
  {id: 1, text: 'Watch a puppet show', done: false},
  {id: 2, text: 'Lennon Wall pic', done: false},
];
```

```js AddTask.js hidden
import { useState } from 'react';

export default function AddTask({onAddTask}) {
  const [text, setText] = useState('');
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={(e) => setText(e.target.value)}
      />
      <button
        onClick={() => {
          setText('');
          onAddTask(text);
        }}>
        Add
      </button>
    </>
  );
}
```

```js TaskList.js hidden
import { useState } from 'react';

export default function TaskList({tasks, onChangeTask, onDeleteTask}) {
  return (
    <ul>
      {tasks.map((task) => (
        <li key={task.id}>
          <Task task={task} onChange={onChangeTask} onDelete={onDeleteTask} />
        </li>
      ))}
    </ul>
  );
}

function Task({task, onChange, onDelete}) {
  const [isEditing, setIsEditing] = useState(false);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={(e) => {
            onChange({
              ...task,
              text: e.target.value,
            });
          }}
        />
        <button onClick={() => setIsEditing(false)}>Save</button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>Edit</button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={(e) => {
          onChange({
            ...task,
            done: e.target.checked,
          });
        }}
      />
      {taskContent}
      <button onClick={() => onDelete(task.id)}>Delete</button>
    </label>
  );
}
```

```css
button {
  margin: 5px;
}
li {
  list-style-type: none;
}
ul,
li {
  margin: 0;
  padding: 0;
}
```

</Sandpack>

যদি চান, তাহলে আপনি reducer টিকে ভিন্ন আরেকটি ফাইলেও নিতে পারেন:

<Sandpack>

```js App.js
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';
import tasksReducer from './tasksReducer.js';

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

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
      task: task,
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId,
    });
  }

  return (
    <>
      <h1>Prague itinerary</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: 'Visit Kafka Museum', done: true},
  {id: 1, text: 'Watch a puppet show', done: false},
  {id: 2, text: 'Lennon Wall pic', done: false},
];
```

```js tasksReducer.js
export default function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        {
          id: action.id,
          text: action.text,
          done: false,
        },
      ];
    }
    case 'changed': {
      return tasks.map((t) => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

```js AddTask.js hidden
import { useState } from 'react';

export default function AddTask({onAddTask}) {
  const [text, setText] = useState('');
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={(e) => setText(e.target.value)}
      />
      <button
        onClick={() => {
          setText('');
          onAddTask(text);
        }}>
        Add
      </button>
    </>
  );
}
```

```js TaskList.js hidden
import { useState } from 'react';

export default function TaskList({tasks, onChangeTask, onDeleteTask}) {
  return (
    <ul>
      {tasks.map((task) => (
        <li key={task.id}>
          <Task task={task} onChange={onChangeTask} onDelete={onDeleteTask} />
        </li>
      ))}
    </ul>
  );
}

function Task({task, onChange, onDelete}) {
  const [isEditing, setIsEditing] = useState(false);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={(e) => {
            onChange({
              ...task,
              text: e.target.value,
            });
          }}
        />
        <button onClick={() => setIsEditing(false)}>Save</button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>Edit</button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={(e) => {
          onChange({
            ...task,
            done: e.target.checked,
          });
        }}
      />
      {taskContent}
      <button onClick={() => onDelete(task.id)}>Delete</button>
    </label>
  );
}
```

```css
button {
  margin: 5px;
}
li {
  list-style-type: none;
}
ul,
li {
  margin: 0;
  padding: 0;
}
```

</Sandpack>

যখন আপনি এমন করে separation of concern বজায় রাখবেন, কম্পোনেন্ট লজিক পড়াটা তখন সহজতর হবে। এখন event handler গুলো actions কে dispatch (প্রেরণ) করার মাধ্যমে শুধু _কি ঘটলো_ সেটা নির্ধারণ করে, আর তার জবাবে reducer function টি নির্ধারণ করে _কিভাবে state টি update হয়_।

## `useState` এবং `useReducer` এর তুলনা {/*comparing-usestate-and-usereducer*/}

Reducer এর যে একদম কোনো খারাপ দিক নেই এমনটি না! আপনি নিচের কয়েকটি উপায়ে উভয়ের মাঝে তুলনা করতে পারেনঃ

- **কোডের দৈর্ঘ্য (Code size):** সাধারণত, `useState` এর বেলায় আপনার শুরুতে কম কোড লেখা লাগে। আর `useReducer` এর বেলায়, আপনাকে একটি reducer function লেখা _এবং_ actions কে dispatch করা উভয়টিই করতে হয়। তবে, `useReducer` কোডের দৈর্ঘ্য কমাতে সহায়তা করতে পারে যদি কয়েকটি event handler একইভাবে state কে modify করে থাকে।
- **পড়ার সহজতা (Readability):** `useState` পড়তে খুব সহজ যখন state update গুলো simple হয়। যখন তা জটিল হয়, তখন `useState` গুলো আপনার কম্পোনেটের কোডকে হিজিবিজি করে তোলে ও কোডে চোখ বুলানোটা কঠিনতর করে তোলে। এক্ষেত্রে, `useReducer` আপনাকে লজিক আপডেট _কিভাবে হলো (how)_ এবং event handler গুলোতে _কি ঘটলো (what happened_) পরিষ্কারভাবে আলাদা আলাদা রাখতে দেয়।
- **বাগ দূর করা (Debugging):** যখন আপনার `useState` সংক্রান্ত কোনো bug থাকে, তখন _কোথায়_ এবং _কেনো_ স্টেটটিকে ভুলভাবে সেট করা হয়েছিলো এটা নির্ণয় করা কঠিন হয়ে উঠতে পারে। `useReducer` এর ক্ষেত্রে, আপনি প্রত্যেক স্টেট আপডেট এবং _কেনো_ (কোন `action` এর কারণে) তা ঘটলো সেটা দেখার জন্য reducer টিতে একটি console log যুক্ত করে দিতে পারেন। যদি প্রতিটি `action` সঠিক হয়ে থাকে, তখন আপনি বুঝে যাবেন যে ভুলটি আসলে reducer logic এর ভিতরে রয়েছে। তবে, আপনাকে এক্ষেত্রে `useState` এর থেকে বেশি কোড ঘাঁটাঘাঁটি করতে হবে।
- **টেস্ট করা (Testing):** Reducer হলো একটি pure function যা আপনার কম্পোনেন্টের উপর নির্ভর করে না। এর মানে আপনি একে আলাদা ভাবে export করে test করতে পারবেন। যদিও স্বাভাবিকভাবে কম্পোনেন্টস কে আরো realistic environment এ টেস্ট করা উত্তম, তবে জটিল state update logic এর ক্ষেত্রে "নির্দিষ্ট initial state এবং action এর জন্য আপনার reducer নির্দিষ্ট state রিটার্ন করে" এ ব্যাপারে নিশ্চিত থাকা উপকারে আসতে পারে।
- **ব্যাক্তিগত পছন্দ (Personal preference):** কেউ reducer পছন্দ করে, কেউ করেনা। এটা কোনো সমস্যা না। এটা একটা রুচির বিষয়। আপনি সর্বদাই `useState` এবং `useReducer` এর মাঝে অদল বদল করতে পারবেনঃ তারা উভয়ই সমান!

যদি আপনি কোনো কম্পোনেন্টে ভুলভাল স্টেট আপডেটের কারণে bug এর সম্মুখীন হন এবং এর কোডের কাঠামো আরো সুন্দর করতে চান, সেক্ষেত্রে আমরা একটি reducer ব্যাবহার করা রেকমেন্ড করি। আপনার সব কিছুর জন্য reducer ব্যাবহার করতে হবে এমন কোনো কথা নেই: আপনি বিনা বাধায় মিলিয়ে মিশিয়ে ব্যাবহার করতে পারেন! এমনকি আপনি একই কম্পোনেন্টে `useState` এবং `useReducer` ব্যাবহার করতে পারেন।

## যেভাবে ভালো reducer লেখবেন {/*writing-reducers-well*/}

Reducer লেখার সময় এই দুটি টিপস মনে রাখবেনঃ

- **Reducer কে অবশই pure হতে হবে।** [state updater ফাংশনের](/learn/queueing-a-series-of-state-updates) মতো, reducer সমূহ রেন্ডারের সময় run করে! (Action সমূহকে পরবর্তী রেন্ডার পর্যন্ত সারিবদ্ধ ভাবে দাঁড় করিয়ে রাখা হয়।) এর মানে, reducer সমূহ [অবশ্যই pure হতে হবে](/learn/keeping-components-pure)—একই input একই output দিবে। সেগুলো যেন কোনো request সেন্ড, timeout ঠিক করা, অথবা কোনো সাইড ইফেক্ট (এমন অপারেশন যেটা কম্পোনেন্টের বাইরের কোনো কিছুর উপর প্রভাব ফেলে) পারফর্ম না করে। সেগুলো যেন [objects](/learn/updating-objects-in-state) এবং [arrays](/learn/updating-arrays-in-state) mutations ছাড়াই আপডেট করে।
- **প্রতিটি action একটি মাত্র user interaction এর বর্ণনা হবে, যদি তার কারণে ডেটাতে একাধিক পরিবর্তন হয় তবুও।** উদাহরণস্বরূপ, যদি একজন ইউজার একটি ফর্মে "Reset" প্রেস করে যে ফর্মের ৫ টি ফিল্ড আছে যেগুলো একটি reducer দ্বারা নিয়ন্ত্রিত, তখন একটি `reset_form` action কে dispatch করাটা পাঁচটি পৃথক `set_field` action dispatch করার থেকে যৌক্তিক। আপনি যদি একটি reducer এ প্রতিটি action log করেন, ঐ log গুলো আপনার জন্যও যথেষ্ট বোধগম্য হওয়ার কথা যাতে কি কি ইন্টার‍্যাকশন বা কি কি রেসপন্স কোনটার পরে কোনটা হয়েছে তা আন্দাজ করতে পারেন। এটা ডিবাগিং এর সময় সাহায্য করে!

## Immer দিয়ে সংক্ষেপে reducers লেখা {/*writing-concise-reducers-with-immer*/}

স্বাভাবিক স্টেটে [objects](/learn/updating-objects-in-state#write-concise-update-logic-with-immer) এবং [arrays](/learn/updating-arrays-in-state#write-concise-update-logic-with-immer) আপডেট করার মতই, আপনি reducer সমূহকে আরো সংক্ষেপ করতে আপনি Immer লাইব্রেরীটি ব্যাবহার করতে পারেন। এখানে, [`useImmerReducer`](https://github.com/immerjs/use-immer#useimmerreducer) আপনাকে `push` অথবা `arr[i] =` অ্যাসাইনমেন্ট দিয়ে স্টেট আপডেট করতে দিচ্ছে:

<Sandpack>

```js App.js
import { useImmerReducer } from 'use-immer';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

function tasksReducer(draft, action) {
  switch (action.type) {
    case 'added': {
      draft.push({
        id: action.id,
        text: action.text,
        done: false,
      });
      break;
    }
    case 'changed': {
      const index = draft.findIndex((t) => t.id === action.task.id);
      draft[index] = action.task;
      break;
    }
    case 'deleted': {
      return draft.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

export default function TaskApp() {
  const [tasks, dispatch] = useImmerReducer(tasksReducer, initialTasks);

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
      task: task,
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId,
    });
  }

  return (
    <>
      <h1>Prague itinerary</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: 'Visit Kafka Museum', done: true},
  {id: 1, text: 'Watch a puppet show', done: false},
  {id: 2, text: 'Lennon Wall pic', done: false},
];
```

```js AddTask.js hidden
import { useState } from 'react';

export default function AddTask({onAddTask}) {
  const [text, setText] = useState('');
  return (
    <>
      <input
        placeholder="Add task"
        value={text}
        onChange={(e) => setText(e.target.value)}
      />
      <button
        onClick={() => {
          setText('');
          onAddTask(text);
        }}>
        Add
      </button>
    </>
  );
}
```

```js TaskList.js hidden
import { useState } from 'react';

export default function TaskList({tasks, onChangeTask, onDeleteTask}) {
  return (
    <ul>
      {tasks.map((task) => (
        <li key={task.id}>
          <Task task={task} onChange={onChangeTask} onDelete={onDeleteTask} />
        </li>
      ))}
    </ul>
  );
}

function Task({task, onChange, onDelete}) {
  const [isEditing, setIsEditing] = useState(false);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={(e) => {
            onChange({
              ...task,
              text: e.target.value,
            });
          }}
        />
        <button onClick={() => setIsEditing(false)}>Save</button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>Edit</button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={(e) => {
          onChange({
            ...task,
            done: e.target.checked,
          });
        }}
      />
      {taskContent}
      <button onClick={() => onDelete(task.id)}>Delete</button>
    </label>
  );
}
```

```css
button {
  margin: 5px;
}
li {
  list-style-type: none;
}
ul,
li {
  margin: 0;
  padding: 0;
}
```

```json package.json
{
  "dependencies": {
    "immer": "1.7.3",
    "react": "latest",
    "react-dom": "latest",
    "react-scripts": "latest",
    "use-immer": "0.5.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

</Sandpack>

Reducers কে অবশ্যই pure হতে হবে, যেন সেগুলো স্টেটকে mutate না করে। তবে Immer আপনাকে এখানে একটি স্পেশাল `draft` অবজেক্ট দিচ্ছে যেটিকে মিউটেট করা সম্পূর্ণ নিরাপদ। চোখের আড়ালে, Immer আপনার স্টেটের একটি কপি তৈরি করে নিবে যার মধ্যে `draft` এর মধ্যে আপনি যত কিছু পরিবর্তন করেছেন, সব বিদ্যমান থাকবে। এজন্যে `useImmerReducer` দ্বারা নিয়ন্ত্রিত reducers তাদের প্রথম আর্গুমেন্ট মিউটেট করতে পারে এবং তাদের স্টেট রিটার্ন করতে হয়না।

<Recap>

- `useState` কে `useReducer` এ পরিবর্তন করতে:
  1. ইভেন্ট হ্যান্ডলারসমূহ থেকে actions ডিসপ্যাচ করুন।
  2. একটি reducer function যেটি প্রদত্ত স্টেটের জন্য পরবর্তী স্টেট রিটার্ন করে এবং action সমূহ লিখুন।
  3. `useState` এর জায়গায় `useReducer` ব্যবহার করুন।
- Reducers এর জন্য আপনার একটু বাড়তি কোড লিখতে হয়, কিন্তু এরা ডিবাগিং এবং টেস্টিং এ সহায়ক।
- Reducers অবশ্যই pure হতে হবে।
- প্রতিটি action একটি মাত্র user interaction এর বর্ণনা হবে।
- Immer ব্যবহার করুন যদি আপনি reducers কে mutating স্টাইলে লিখতে চান।

</Recap>

<Challenges>

#### ইভেন্ট হ্যান্ডলারস থেকে actions কে dispatch করুন {/*dispatch-actions-from-event-handlers*/}

এখানে, `ContactList.js` এবং `Chat.js` এর ইভেন্ট হ্যান্ডলারগুলোতে `// TODO` কমেন্ট করা আছে। এজন্যেই ইনপুটটিতে টাইপ করলে কিছু হচ্ছে না, এবং পাশের বাটন গুলোতে ক্লিক করলে মেসেজের প্রাপক বদলাচ্ছেনা।

এই দুইটি `// TODO` এর জায়গায় নিজ নিজ action গুলো `dispatch` করার কোড লিখুন। action গুলোর কাঙ্ক্ষিত আকৃতি এবং টাইপ জানার জন্য, `messengerReducer.js` এর মধ্যের reducer টি দেখুন। Reducer টি অলরেডি লিখে দেয়া হয়েছে, তাই সেটিতে আপনার কোনো পরিবর্তন আনতে হবেনা। আপনার শুধু `ContactList.js` এবং `Chat.js` এ action গুলো dispatch করতে হবে।

<Hint>

`dispatch` ফাংশনটি অলরেডি উভয় কম্পোনেন্টের মধ্যে বিদ্যমান কারণ সেটিকে প্রপ হিসেবে পাঠিয়ে দেয়া হয়েছিলো। তাই আপনার শুধু `dispatch` এর মধ্যে উপযুক্ত অবজেক্ট দিয়ে কল করতে হবে।

Action অবজেক্টটির আকৃতি চেক করার জন্য, আপনি reducer টির কোড দেখতে পারেন এবং বুঝতে পারেন কোন কোন `action` ফিল্ড সেটি পেতে পারে। যেমন, reducer এ `changed_selection` case টি এমন দেখা যাচ্ছেঃ

```js
case 'changed_selection': {
  return {
    ...state,
    selectedId: action.contactId
  };
}
```

এর মানে হচ্ছে আপনার action অবজেক্ট এ `type: 'changed_selection'` থাকতে হবে। আপনি আরও দেখতে পাচ্ছেন যে `action.contactId` এখানে ব্যাবহৃত হচ্ছে, তাই আপনার action অবজেক্ট এ একটি `contactId` প্রপার্টি থাকতে হবে।

</Hint>

<Sandpack>

```js App.js
import { useReducer } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';
import { initialState, messengerReducer } from './messengerReducer';

export default function Messenger() {
  const [state, dispatch] = useReducer(messengerReducer, initialState);
  const message = state.message;
  const contact = contacts.find((c) => c.id === state.selectedId);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={state.selectedId}
        dispatch={dispatch}
      />
      <Chat
        key={contact.id}
        message={message}
        contact={contact}
        dispatch={dispatch}
      />
    </div>
  );
}

const contacts = [
  {id: 0, name: 'Taylor', email: 'taylor@mail.com'},
  {id: 1, name: 'Alice', email: 'alice@mail.com'},
  {id: 2, name: 'Bob', email: 'bob@mail.com'},
];
```

```js messengerReducer.js
export const initialState = {
  selectedId: 0,
  message: 'Hello',
};

export function messengerReducer(state, action) {
  switch (action.type) {
    case 'changed_selection': {
      return {
        ...state,
        selectedId: action.contactId,
        message: '',
      };
    }
    case 'edited_message': {
      return {
        ...state,
        message: action.message,
      };
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

```js ContactList.js
export default function ContactList({contacts, selectedId, dispatch}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map((contact) => (
          <li key={contact.id}>
            <button
              onClick={() => {
                // TODO: dispatch changed_selection
              }}>
              {selectedId === contact.id ? <b>{contact.name}</b> : contact.name}
            </button>
          </li>
        ))}
      </ul>
    </section>
  );
}
```

```js Chat.js
import { useState } from 'react';

export default function Chat({contact, message, dispatch}) {
  return (
    <section className="chat">
      <textarea
        value={message}
        placeholder={'Chat to ' + contact.name}
        onChange={(e) => {
          // TODO: dispatch edited_message
          // (Read the input value from e.target.value)
        }}
      />
      <br />
      <button>Send to {contact.email}</button>
    </section>
  );
}
```

```css
.chat,
.contact-list {
  float: left;
  margin-bottom: 20px;
}
ul,
li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

<Solution>

Reducer এর কোড দেখে, আপনি বুঝতে পারেন যে action গুলো দেখতে এমন হতে হবেঃ

```js
// When the user presses "Alice"
dispatch({
  type: 'changed_selection',
  contactId: 1,
});

// When user types "Hello!"
dispatch({
  type: 'edited_message',
  message: 'Hello!',
});
```

এই হলো আগের উদাহরণের সমাধান। এখানের কোডে উপযুক্ত action গুলো dispatch করা হয়েছে।

<Sandpack>

```js App.js
import { useReducer } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';
import { initialState, messengerReducer } from './messengerReducer';

export default function Messenger() {
  const [state, dispatch] = useReducer(messengerReducer, initialState);
  const message = state.message;
  const contact = contacts.find((c) => c.id === state.selectedId);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={state.selectedId}
        dispatch={dispatch}
      />
      <Chat
        key={contact.id}
        message={message}
        contact={contact}
        dispatch={dispatch}
      />
    </div>
  );
}

const contacts = [
  {id: 0, name: 'Taylor', email: 'taylor@mail.com'},
  {id: 1, name: 'Alice', email: 'alice@mail.com'},
  {id: 2, name: 'Bob', email: 'bob@mail.com'},
];
```

```js messengerReducer.js
export const initialState = {
  selectedId: 0,
  message: 'Hello',
};

export function messengerReducer(state, action) {
  switch (action.type) {
    case 'changed_selection': {
      return {
        ...state,
        selectedId: action.contactId,
        message: '',
      };
    }
    case 'edited_message': {
      return {
        ...state,
        message: action.message,
      };
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

```js ContactList.js
export default function ContactList({contacts, selectedId, dispatch}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map((contact) => (
          <li key={contact.id}>
            <button
              onClick={() => {
                dispatch({
                  type: 'changed_selection',
                  contactId: contact.id,
                });
              }}>
              {selectedId === contact.id ? <b>{contact.name}</b> : contact.name}
            </button>
          </li>
        ))}
      </ul>
    </section>
  );
}
```

```js Chat.js
import { useState } from 'react';

export default function Chat({contact, message, dispatch}) {
  return (
    <section className="chat">
      <textarea
        value={message}
        placeholder={'Chat to ' + contact.name}
        onChange={(e) => {
          dispatch({
            type: 'edited_message',
            message: e.target.value,
          });
        }}
      />
      <br />
      <button>Send to {contact.email}</button>
    </section>
  );
}
```

```css
.chat,
.contact-list {
  float: left;
  margin-bottom: 20px;
}
ul,
li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

</Solution>

#### Message সেন্ড করার সাথে সাথে ইনপুট ক্লিয়ার করে দিন {/*clear-the-input-on-sending-a-message*/}

এখানে, "Send" প্রেস করলে কিছু হচ্ছেনা। "Send" বাটনে একটি ইভেন্ট হ্যান্ডলার অ্যাড করুন যার মধ্যেঃ

1. একটি `alert` দেখান যাতে প্রাপকের ইমেইল এবং মেসেজ থাকবে।
2. মেসেজ ইনপুট ক্লিয়ার করে দিন।

<Sandpack>

```js App.js
import { useReducer } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';
import { initialState, messengerReducer } from './messengerReducer';

export default function Messenger() {
  const [state, dispatch] = useReducer(messengerReducer, initialState);
  const message = state.message;
  const contact = contacts.find((c) => c.id === state.selectedId);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={state.selectedId}
        dispatch={dispatch}
      />
      <Chat
        key={contact.id}
        message={message}
        contact={contact}
        dispatch={dispatch}
      />
    </div>
  );
}

const contacts = [
  {id: 0, name: 'Taylor', email: 'taylor@mail.com'},
  {id: 1, name: 'Alice', email: 'alice@mail.com'},
  {id: 2, name: 'Bob', email: 'bob@mail.com'},
];
```

```js messengerReducer.js
export const initialState = {
  selectedId: 0,
  message: 'Hello',
};

export function messengerReducer(state, action) {
  switch (action.type) {
    case 'changed_selection': {
      return {
        ...state,
        selectedId: action.contactId,
        message: '',
      };
    }
    case 'edited_message': {
      return {
        ...state,
        message: action.message,
      };
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

```js ContactList.js
export default function ContactList({contacts, selectedId, dispatch}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map((contact) => (
          <li key={contact.id}>
            <button
              onClick={() => {
                dispatch({
                  type: 'changed_selection',
                  contactId: contact.id,
                });
              }}>
              {selectedId === contact.id ? <b>{contact.name}</b> : contact.name}
            </button>
          </li>
        ))}
      </ul>
    </section>
  );
}
```

```js Chat.js active
import { useState } from 'react';

export default function Chat({contact, message, dispatch}) {
  return (
    <section className="chat">
      <textarea
        value={message}
        placeholder={'Chat to ' + contact.name}
        onChange={(e) => {
          dispatch({
            type: 'edited_message',
            message: e.target.value,
          });
        }}
      />
      <br />
      <button>Send to {contact.email}</button>
    </section>
  );
}
```

```css
.chat,
.contact-list {
  float: left;
  margin-bottom: 20px;
}
ul,
li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

<Solution>

আপনি "Send" বাটন ইভেন্ট হ্যান্ডলারে এমনটা করার দুটি পদ্ধতি আছে। প্রথম পদ্ধতিটি হলো, একটি alert শো করার পরে, `edited_message` action dispatch করা যেখানে `message` এর ভ্যালু  হবে `''` বা ফাঁকা স্ট্রিংঃ

<Sandpack>

```js App.js
import { useReducer } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';
import { initialState, messengerReducer } from './messengerReducer';

export default function Messenger() {
  const [state, dispatch] = useReducer(messengerReducer, initialState);
  const message = state.message;
  const contact = contacts.find((c) => c.id === state.selectedId);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={state.selectedId}
        dispatch={dispatch}
      />
      <Chat
        key={contact.id}
        message={message}
        contact={contact}
        dispatch={dispatch}
      />
    </div>
  );
}

const contacts = [
  {id: 0, name: 'Taylor', email: 'taylor@mail.com'},
  {id: 1, name: 'Alice', email: 'alice@mail.com'},
  {id: 2, name: 'Bob', email: 'bob@mail.com'},
];
```

```js messengerReducer.js
export const initialState = {
  selectedId: 0,
  message: 'Hello',
};

export function messengerReducer(state, action) {
  switch (action.type) {
    case 'changed_selection': {
      return {
        ...state,
        selectedId: action.contactId,
        message: '',
      };
    }
    case 'edited_message': {
      return {
        ...state,
        message: action.message,
      };
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

```js ContactList.js
export default function ContactList({contacts, selectedId, dispatch}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map((contact) => (
          <li key={contact.id}>
            <button
              onClick={() => {
                dispatch({
                  type: 'changed_selection',
                  contactId: contact.id,
                });
              }}>
              {selectedId === contact.id ? <b>{contact.name}</b> : contact.name}
            </button>
          </li>
        ))}
      </ul>
    </section>
  );
}
```

```js Chat.js active
import { useState } from 'react';

export default function Chat({contact, message, dispatch}) {
  return (
    <section className="chat">
      <textarea
        value={message}
        placeholder={'Chat to ' + contact.name}
        onChange={(e) => {
          dispatch({
            type: 'edited_message',
            message: e.target.value,
          });
        }}
      />
      <br />
      <button
        onClick={() => {
          alert(`Sending "${message}" to ${contact.email}`);
          dispatch({
            type: 'edited_message',
            message: '',
          });
        }}>
        Send to {contact.email}
      </button>
    </section>
  );
}
```

```css
.chat,
.contact-list {
  float: left;
  margin-bottom: 20px;
}
ul,
li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

এটা কাজ করবে এবং আপনি যখন "Send" করবেন তখন ইনপুট ক্লিয়ার করে দিবে।

তবে, _ইউজার এর দৃষ্টিকোণ থেকে_, মেসেজ সেন্ড করাটা ইনপুট ফিল্ড এডিট করার থেকে ভিন্ন একটি action । সে অনুসারে, আপনি `sent_message` নামের একটি _নতুন_ action তৈরি করতে পারেন, এবং সেটিকে reducer এ আলাদাভাবে হ্যান্ডেল করতে পারেন।

<Sandpack>

```js App.js
import { useReducer } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';
import { initialState, messengerReducer } from './messengerReducer';

export default function Messenger() {
  const [state, dispatch] = useReducer(messengerReducer, initialState);
  const message = state.message;
  const contact = contacts.find((c) => c.id === state.selectedId);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={state.selectedId}
        dispatch={dispatch}
      />
      <Chat
        key={contact.id}
        message={message}
        contact={contact}
        dispatch={dispatch}
      />
    </div>
  );
}

const contacts = [
  {id: 0, name: 'Taylor', email: 'taylor@mail.com'},
  {id: 1, name: 'Alice', email: 'alice@mail.com'},
  {id: 2, name: 'Bob', email: 'bob@mail.com'},
];
```

```js messengerReducer.js active
export const initialState = {
  selectedId: 0,
  message: 'Hello',
};

export function messengerReducer(state, action) {
  switch (action.type) {
    case 'changed_selection': {
      return {
        ...state,
        selectedId: action.contactId,
        message: '',
      };
    }
    case 'edited_message': {
      return {
        ...state,
        message: action.message,
      };
    }
    case 'sent_message': {
      return {
        ...state,
        message: '',
      };
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

```js ContactList.js
export default function ContactList({contacts, selectedId, dispatch}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map((contact) => (
          <li key={contact.id}>
            <button
              onClick={() => {
                dispatch({
                  type: 'changed_selection',
                  contactId: contact.id,
                });
              }}>
              {selectedId === contact.id ? <b>{contact.name}</b> : contact.name}
            </button>
          </li>
        ))}
      </ul>
    </section>
  );
}
```

```js Chat.js active
import { useState } from 'react';

export default function Chat({contact, message, dispatch}) {
  return (
    <section className="chat">
      <textarea
        value={message}
        placeholder={'Chat to ' + contact.name}
        onChange={(e) => {
          dispatch({
            type: 'edited_message',
            message: e.target.value,
          });
        }}
      />
      <br />
      <button
        onClick={() => {
          alert(`Sending "${message}" to ${contact.email}`);
          dispatch({
            type: 'sent_message',
          });
        }}>
        Send to {contact.email}
      </button>
    </section>
  );
}
```

```css
.chat,
.contact-list {
  float: left;
  margin-bottom: 20px;
}
ul,
li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

উভয় পদ্ধতির ফলাফল একই। তবে মনে রাখবেন, নিয়ম হচ্ছে action এর type যেন "আপনি ষ্টেটের পরিবর্তন কিভাবে চান" সেটা প্রকাশ না করে "ইউজার কি করলো" সেটা যেন প্রকাশ করে। এটা পরবর্তীতে আরো ফিচার যোগ করাটা সহজ করবে।

উভয় সমাধানের ক্ষেত্রেই, এটা গুরুত্বপূর্ণ বিষয় যে আপনি `alert` টি কোনো reducer এর ভিতর **রাখবেন না**। Reducer হতে হবে একটি pure function--এটার কাজ  যেন হয় শুধুই পরবর্তী ষ্টেট নির্ণয় করে। এটা যেন আর কিছুই না "করে", যেমন ইউজার কে কোনো বিজ্ঞপ্তি দেখানো। এসব করতে হবে ইভেন্ট হ্যান্ডলারে। (এই ধরণের ভুল থেকে বাঁচার জন্য, রিয়েক্ট Strict Mode এ আপনার reducer গুলোকে একাধিক বার কল করবে। একারণেই, আপনি যদি reducer এর মধ্যে কোনো alert কল করেন, এটা দুইবার দেখায়।)

</Solution>

#### ট্যাব বদলানোর সময় input ভ্যালু ফিরিয়ে আনুন {/*restore-input-values-when-switching-between-tabs*/}

এই উদাহরণে, প্রাপক একজন থেকে আরেকজনে পরিবর্তন করলে টেক্সট ইনপুট প্রতিবার ফাঁকা হয়ে যায়:

```js
case 'changed_selection': {
  return {
    ...state,
    selectedId: action.contactId,
    message: '' // Clears the input
  };
```

This is because you don't want to share a single message draft between several recipients. But it would be better if your app "remembered" a draft for each contact separately, restoring them when you switch contacts.

Your task is to change the way the state is structured so that you remember a separate message draft _per contact_. You would need to make a few changes to the reducer, the initial state, and the components.

<Hint>

You can structure your state like this:

```js
export const initialState = {
  selectedId: 0,
  messages: {
    0: 'Hello, Taylor', // Draft for contactId = 0
    1: 'Hello, Alice', // Draft for contactId = 1
  },
};
```

The `[key]: value` [computed property](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#computed_property_names) syntax can help you update the `messages` object:

```js
{
  ...state.messages,
  [id]: message
}
```

</Hint>

<Sandpack>

```js App.js
import { useReducer } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';
import { initialState, messengerReducer } from './messengerReducer';

export default function Messenger() {
  const [state, dispatch] = useReducer(messengerReducer, initialState);
  const message = state.message;
  const contact = contacts.find((c) => c.id === state.selectedId);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={state.selectedId}
        dispatch={dispatch}
      />
      <Chat
        key={contact.id}
        message={message}
        contact={contact}
        dispatch={dispatch}
      />
    </div>
  );
}

const contacts = [
  {id: 0, name: 'Taylor', email: 'taylor@mail.com'},
  {id: 1, name: 'Alice', email: 'alice@mail.com'},
  {id: 2, name: 'Bob', email: 'bob@mail.com'},
];
```

```js messengerReducer.js
export const initialState = {
  selectedId: 0,
  message: 'Hello',
};

export function messengerReducer(state, action) {
  switch (action.type) {
    case 'changed_selection': {
      return {
        ...state,
        selectedId: action.contactId,
        message: '',
      };
    }
    case 'edited_message': {
      return {
        ...state,
        message: action.message,
      };
    }
    case 'sent_message': {
      return {
        ...state,
        message: '',
      };
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

```js ContactList.js
export default function ContactList({contacts, selectedId, dispatch}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map((contact) => (
          <li key={contact.id}>
            <button
              onClick={() => {
                dispatch({
                  type: 'changed_selection',
                  contactId: contact.id,
                });
              }}>
              {selectedId === contact.id ? <b>{contact.name}</b> : contact.name}
            </button>
          </li>
        ))}
      </ul>
    </section>
  );
}
```

```js Chat.js
import { useState } from 'react';

export default function Chat({contact, message, dispatch}) {
  return (
    <section className="chat">
      <textarea
        value={message}
        placeholder={'Chat to ' + contact.name}
        onChange={(e) => {
          dispatch({
            type: 'edited_message',
            message: e.target.value,
          });
        }}
      />
      <br />
      <button
        onClick={() => {
          alert(`Sending "${message}" to ${contact.email}`);
          dispatch({
            type: 'sent_message',
          });
        }}>
        Send to {contact.email}
      </button>
    </section>
  );
}
```

```css
.chat,
.contact-list {
  float: left;
  margin-bottom: 20px;
}
ul,
li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

<Solution>

You'll need to update the reducer to store and update a separate message draft per contact:

```js
// When the input is edited
case 'edited_message': {
  return {
    // Keep other state like selection
    ...state,
    messages: {
      // Keep messages for other contacts
      ...state.messages,
      // But change the selected contact's message
      [state.selectedId]: action.message
    }
  };
}
```

You would also update the `Messenger` component to read the message for the currently selected contact:

```js
const message = state.messages[state.selectedId];
```

Here is the complete solution:

<Sandpack>

```js App.js
import { useReducer } from 'react';
import Chat from './Chat.js';
import ContactList from './ContactList.js';
import { initialState, messengerReducer } from './messengerReducer';

export default function Messenger() {
  const [state, dispatch] = useReducer(messengerReducer, initialState);
  const message = state.messages[state.selectedId];
  const contact = contacts.find((c) => c.id === state.selectedId);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={state.selectedId}
        dispatch={dispatch}
      />
      <Chat
        key={contact.id}
        message={message}
        contact={contact}
        dispatch={dispatch}
      />
    </div>
  );
}

const contacts = [
  {id: 0, name: 'Taylor', email: 'taylor@mail.com'},
  {id: 1, name: 'Alice', email: 'alice@mail.com'},
  {id: 2, name: 'Bob', email: 'bob@mail.com'},
];
```

```js messengerReducer.js
export const initialState = {
  selectedId: 0,
  messages: {
    0: 'Hello, Taylor',
    1: 'Hello, Alice',
    2: 'Hello, Bob',
  },
};

export function messengerReducer(state, action) {
  switch (action.type) {
    case 'changed_selection': {
      return {
        ...state,
        selectedId: action.contactId,
      };
    }
    case 'edited_message': {
      return {
        ...state,
        messages: {
          ...state.messages,
          [state.selectedId]: action.message,
        },
      };
    }
    case 'sent_message': {
      return {
        ...state,
        messages: {
          ...state.messages,
          [state.selectedId]: '',
        },
      };
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

```js ContactList.js
export default function ContactList({contacts, selectedId, dispatch}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map((contact) => (
          <li key={contact.id}>
            <button
              onClick={() => {
                dispatch({
                  type: 'changed_selection',
                  contactId: contact.id,
                });
              }}>
              {selectedId === contact.id ? <b>{contact.name}</b> : contact.name}
            </button>
          </li>
        ))}
      </ul>
    </section>
  );
}
```

```js Chat.js
import { useState } from 'react';

export default function Chat({contact, message, dispatch}) {
  return (
    <section className="chat">
      <textarea
        value={message}
        placeholder={'Chat to ' + contact.name}
        onChange={(e) => {
          dispatch({
            type: 'edited_message',
            message: e.target.value,
          });
        }}
      />
      <br />
      <button
        onClick={() => {
          alert(`Sending "${message}" to ${contact.email}`);
          dispatch({
            type: 'sent_message',
          });
        }}>
        Send to {contact.email}
      </button>
    </section>
  );
}
```

```css
.chat,
.contact-list {
  float: left;
  margin-bottom: 20px;
}
ul,
li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

Notably, you didn't need to change any of the event handlers to implement this different behavior. Without a reducer, you would have to change every event handler that updates the state.

</Solution>

#### Implement `useReducer` from scratch {/*implement-usereducer-from-scratch*/}

In the earlier examples, you imported the `useReducer` Hook from React. This time, you will implement _the `useReducer` Hook itself!_ Here is a stub to get you started. It shouldn't take more than 10 lines of code.

To test your changes, try typing into the input or select a contact.

<Hint>

Here is a more detailed sketch of the implementation:

```js
export function useReducer(reducer, initialState) {
  const [state, setState] = useState(initialState);

  function dispatch(action) {
    // ???
  }

  return [state, dispatch];
}
```

Recall that a reducer function takes two arguments--the current state and the action object--and it returns the next state. What should your `dispatch` implementation do with it?

</Hint>

<Sandpack>

```js App.js
import { useReducer } from './MyReact.js';
import Chat from './Chat.js';
import ContactList from './ContactList.js';
import { initialState, messengerReducer } from './messengerReducer';

export default function Messenger() {
  const [state, dispatch] = useReducer(messengerReducer, initialState);
  const message = state.messages[state.selectedId];
  const contact = contacts.find((c) => c.id === state.selectedId);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={state.selectedId}
        dispatch={dispatch}
      />
      <Chat
        key={contact.id}
        message={message}
        contact={contact}
        dispatch={dispatch}
      />
    </div>
  );
}

const contacts = [
  {id: 0, name: 'Taylor', email: 'taylor@mail.com'},
  {id: 1, name: 'Alice', email: 'alice@mail.com'},
  {id: 2, name: 'Bob', email: 'bob@mail.com'},
];
```

```js messengerReducer.js
export const initialState = {
  selectedId: 0,
  messages: {
    0: 'Hello, Taylor',
    1: 'Hello, Alice',
    2: 'Hello, Bob',
  },
};

export function messengerReducer(state, action) {
  switch (action.type) {
    case 'changed_selection': {
      return {
        ...state,
        selectedId: action.contactId,
      };
    }
    case 'edited_message': {
      return {
        ...state,
        messages: {
          ...state.messages,
          [state.selectedId]: action.message,
        },
      };
    }
    case 'sent_message': {
      return {
        ...state,
        messages: {
          ...state.messages,
          [state.selectedId]: '',
        },
      };
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

```js MyReact.js active
import { useState } from 'react';

export function useReducer(reducer, initialState) {
  const [state, setState] = useState(initialState);

  // ???

  return [state, dispatch];
}
```

```js ContactList.js hidden
export default function ContactList({contacts, selectedId, dispatch}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map((contact) => (
          <li key={contact.id}>
            <button
              onClick={() => {
                dispatch({
                  type: 'changed_selection',
                  contactId: contact.id,
                });
              }}>
              {selectedId === contact.id ? <b>{contact.name}</b> : contact.name}
            </button>
          </li>
        ))}
      </ul>
    </section>
  );
}
```

```js Chat.js hidden
import { useState } from 'react';

export default function Chat({contact, message, dispatch}) {
  return (
    <section className="chat">
      <textarea
        value={message}
        placeholder={'Chat to ' + contact.name}
        onChange={(e) => {
          dispatch({
            type: 'edited_message',
            message: e.target.value,
          });
        }}
      />
      <br />
      <button
        onClick={() => {
          alert(`Sending "${message}" to ${contact.email}`);
          dispatch({
            type: 'sent_message',
          });
        }}>
        Send to {contact.email}
      </button>
    </section>
  );
}
```

```css
.chat,
.contact-list {
  float: left;
  margin-bottom: 20px;
}
ul,
li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

<Solution>

Dispatching an action calls a reducer with the current state and the action, and stores the result as the next state. This is what it looks like in code:

<Sandpack>

```js App.js
import { useReducer } from './MyReact.js';
import Chat from './Chat.js';
import ContactList from './ContactList.js';
import { initialState, messengerReducer } from './messengerReducer';

export default function Messenger() {
  const [state, dispatch] = useReducer(messengerReducer, initialState);
  const message = state.messages[state.selectedId];
  const contact = contacts.find((c) => c.id === state.selectedId);
  return (
    <div>
      <ContactList
        contacts={contacts}
        selectedId={state.selectedId}
        dispatch={dispatch}
      />
      <Chat
        key={contact.id}
        message={message}
        contact={contact}
        dispatch={dispatch}
      />
    </div>
  );
}

const contacts = [
  {id: 0, name: 'Taylor', email: 'taylor@mail.com'},
  {id: 1, name: 'Alice', email: 'alice@mail.com'},
  {id: 2, name: 'Bob', email: 'bob@mail.com'},
];
```

```js messengerReducer.js
export const initialState = {
  selectedId: 0,
  messages: {
    0: 'Hello, Taylor',
    1: 'Hello, Alice',
    2: 'Hello, Bob',
  },
};

export function messengerReducer(state, action) {
  switch (action.type) {
    case 'changed_selection': {
      return {
        ...state,
        selectedId: action.contactId,
      };
    }
    case 'edited_message': {
      return {
        ...state,
        messages: {
          ...state.messages,
          [state.selectedId]: action.message,
        },
      };
    }
    case 'sent_message': {
      return {
        ...state,
        messages: {
          ...state.messages,
          [state.selectedId]: '',
        },
      };
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```

```js MyReact.js active
import { useState } from 'react';

export function useReducer(reducer, initialState) {
  const [state, setState] = useState(initialState);

  function dispatch(action) {
    const nextState = reducer(state, action);
    setState(nextState);
  }

  return [state, dispatch];
}
```

```js ContactList.js hidden
export default function ContactList({contacts, selectedId, dispatch}) {
  return (
    <section className="contact-list">
      <ul>
        {contacts.map((contact) => (
          <li key={contact.id}>
            <button
              onClick={() => {
                dispatch({
                  type: 'changed_selection',
                  contactId: contact.id,
                });
              }}>
              {selectedId === contact.id ? <b>{contact.name}</b> : contact.name}
            </button>
          </li>
        ))}
      </ul>
    </section>
  );
}
```

```js Chat.js hidden
import { useState } from 'react';

export default function Chat({contact, message, dispatch}) {
  return (
    <section className="chat">
      <textarea
        value={message}
        placeholder={'Chat to ' + contact.name}
        onChange={(e) => {
          dispatch({
            type: 'edited_message',
            message: e.target.value,
          });
        }}
      />
      <br />
      <button
        onClick={() => {
          alert(`Sending "${message}" to ${contact.email}`);
          dispatch({
            type: 'sent_message',
          });
        }}>
        Send to {contact.email}
      </button>
    </section>
  );
}
```

```css
.chat,
.contact-list {
  float: left;
  margin-bottom: 20px;
}
ul,
li {
  list-style: none;
  margin: 0;
  padding: 0;
}
li button {
  width: 100px;
  padding: 10px;
  margin-right: 10px;
}
textarea {
  height: 150px;
}
```

</Sandpack>

Though it doesn't matter in most cases, a slightly more accurate implementation looks like this:

```js
function dispatch(action) {
  setState((s) => reducer(s, action));
}
```

This is because the dispatched actions are queued until the next render, [similar to the updater functions.](/learn/queueing-a-series-of-state-updates)

</Solution>

</Challenges>
