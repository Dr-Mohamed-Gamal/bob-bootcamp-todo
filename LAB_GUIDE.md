# Lab Guide — Build a Todo App with Bob

**Duration:** 45 minutes | **Difficulty:** Beginner to Intermediate

---

## What you'll build

A clean Todo app inside a `todo-app/` folder with three files:
- `index.html` — input field and task list
- `styles.css` — dark theme and completed-task styling
- `app.js` — TodoApp class with full state management

---

## Lab timeline

```
Lab timeline (55 min)
├── Lab 01: Plan the structure       — Plan mode     (5 min)
├── Lab 02: Build HTML & CSS         — Code mode     (10 min)
├── Lab 03: Build the TodoApp class  — Code mode     (10 min)
├── Lab 04: Understand edge cases    — Ask mode      (10 min)
├── Lab 05: Create & run unit tests  — Code mode     (10 min)
└── Lab 06: Push to GitHub           — Advanced mode (10 min)
```

---

## Lab 01 — Plan the structure (Plan mode, 5 min)

**Switch Bob to Plan mode.**

Before writing any code, ask Bob to design the project. Bob will ask
some clarifying questions — this is intentional. For example:

- *"What features would you like in your Todo app?"* → Choose **Basic features: Add todos, mark as complete, delete todos**
- *"Do you want todos to persist when the page is refreshed?"* → Choose **No persistence needed — todos can be lost on refresh**
- *"What visual style would you prefer for the Todo app?"* → Choose **Modern & Minimal: Clean design with subtle shadows, modern colors, smooth animations**

**Prompt for Bob:**
```
I want to build a Todo app with HTML, CSS, and vanilla JavaScript.
Help me plan:
1. The file structure
2. The HTML layout
3. What state the TodoApp class needs to track
4. What methods the class should have
Ask me clarifying questions first.
```

**Expected output from Bob:**
- Folder: `todo-app/` containing `index.html`, `styles.css`, `app.js`
- Input field + Add button at the top
- Todo list where each item has a checkbox and delete button
- TodoApp class with: `todos` (array of objects), `nextId` (counter)
- Todo object shape: `{ id, text, completed }`
- Methods: `addTodo(text)`, `toggleComplete(id)`, `deleteTodo(id)`, `render()`, `init()`
- Data flow: User action → method call → state update → `render()`

> **Bob differentiator:** Plan mode asks clarifying questions before planning.
> It lets you drive the process — Bob adapts to your requirements, not the other way around.

---

## Lab 02 — Build HTML & CSS (Code mode, 10 min)

**Switch Bob to Code mode.**

Enable auto-approvals so Bob creates both files without confirmation dialogs.

**Prompt for Bob:**
```
Create todo-app/index.html and todo-app/styles.css for a dark-theme Todo app.
Requirements:
- Input field with an "Add" button at the top
- Todo list (ul#todoList) where each item has: checkbox, task text, delete button
- Dark theme with a centred card layout
- Completed tasks should have strikethrough text and reduced opacity
- Clean hover effects on buttons
Enable auto-approvals.
```

**Key HTML structure Bob produces:**
```html
<div class="container">
  <h1>My Todo App</h1>
  <div class="input-section">
    <input type="text" id="todoInput" placeholder="Enter a new todo...">
    <button id="addBtn">Add</button>
  </div>
  <ul id="todoList"></ul>
</div>
```

**Key CSS pattern Bob uses:**
```css
.todo-item.completed .todo-text {
  text-decoration: line-through;
  opacity: 0.5;
  /* Visual feedback that the task is done —
     strikethrough is more scannable than colour alone */
}
```

> **Bob differentiator:** In Code mode, Bob creates actual files in your project —
> not just snippets to copy-paste. Auto-approvals let it generate multiple files rapidly.

---

## Lab 03 — Build the TodoApp class (Code mode, 10 min)

**Stay in Code mode.**

Ask Bob to use literate coding — comments that explain *why*, not just *what*.

**Prompt for Bob:**
```
Create todo-app/app.js with a TodoApp class using literate coding.
Methods needed: addTodo(text), toggleComplete(id), deleteTodo(id), render(), init().

Use detailed comments explaining:
- Why each todo needs a unique ID (not just an array index)
- Why we re-render the whole list instead of patching individual items
- How the data flow works: user action → method → state update → render()
```

**The class structure Bob produces:**
```javascript
class TodoApp {
  constructor() {
    this.todos = [];  // Array of todo objects { id, text, completed }
    this.nextId = 1;  // Counter ensures each todo has a stable unique ID
                      // Using an index would break references when items are deleted
  }

  addTodo(text) {
    const todo = { id: this.nextId++, text: text.trim(), completed: false };
    this.todos.push(todo);
    this.render();
  }

  toggleComplete(id) {
    const todo = this.todos.find(t => t.id === id);
    if (todo) todo.completed = !todo.completed;
    this.render();
  }

  deleteTodo(id) {
    this.todos = this.todos.filter(t => t.id !== id);
    this.render();
  }

  render() {
    // Re-render the full list from state on every change.
    // This is simpler and safer than patching individual DOM nodes —
    // state is always the single source of truth.
    const list = document.getElementById('todoList');
    list.innerHTML = '';
    this.todos.forEach(todo => {
      const li = document.createElement('li');
      li.className = 'todo-item' + (todo.completed ? ' completed' : '');
      li.innerHTML = `
        <input type="checkbox" ${todo.completed ? 'checked' : ''}
               onchange="app.toggleComplete(${todo.id})">
        <span class="todo-text">${todo.text}</span>
        <button onclick="app.deleteTodo(${todo.id})">Delete</button>
      `;
      list.appendChild(li);
    });
  }

  init() {
    document.getElementById('addBtn').addEventListener('click', () => {
      const input = document.getElementById('todoInput');
      if (input.value.trim()) {
        this.addTodo(input.value);
        input.value = '';
      }
    });
  }
}

const app = new TodoApp();
app.init();
```

> **Bob differentiator:** Literate coding makes your codebase self-documenting.
> Great for onboarding teammates and for your own future reference.

---

## Lab 04 — Understand edge cases (Ask mode, 10 min)

**Switch Bob to Ask mode.**

Ask mode explains without touching your files. Understand the edge cases first,
then switch back to Code mode to fix them.

**Prompt for Bob:**
```
Explain three things about the Todo app we just built:
1. What happens if the user tries to add an empty todo?
2. What happens to todo IDs if we delete items in the middle of the list?
3. What other edge cases does a todo app need to handle and why?
```

**Then switch back to Code mode and ask:**
```
Apply these fixes to todo-app/app.js:
- Block empty or whitespace-only todos in addTodo()
- Allow adding a todo by pressing Enter (not just the Add button)
- Show a "No tasks yet" message when the list is empty
```

**The empty input guard:**
```javascript
addTodo(text) {
  if (!text.trim()) return;
  // Silently ignore empty submissions rather than showing an alert —
  // pressing Add on an empty field is a common accidental action
  // and an error message would be disruptive.
  const todo = { id: this.nextId++, text: text.trim(), completed: false };
  this.todos.push(todo);
  this.render();
}
```

> **Bob differentiator:** Ask mode is intentionally read-only — it encourages
> understanding before fixing. You'll write better code when you know why bugs happen.

---

## Lab 05 — Create & Run Unit Tests (Code mode, 10 min)

**Switch Bob to Code mode.**

Now that the app logic is complete, ask Bob to generate unit tests for the TodoApp class and run them.

**Prompt for Bob:**
```
Create unit tests for the TodoApp class in todo-app/app.js.
Test all methods: addTodo, toggleComplete, deleteTodo, and render.
Cover these cases:
- addTodo adds a todo with correct id, text, and completed=false
- addTodo ignores empty or whitespace-only input
- toggleComplete flips the completed status of the correct todo
- deleteTodo removes the correct todo and leaves others unchanged
- render shows "No tasks yet" when the list is empty
Ensure at least 90% code coverage across all methods.
Run the tests and show me the results.
```

**What Bob generates:**

Bob will create a `todo-app/app.test.js` file using a simple in-browser test runner (no external dependencies needed). Example test structure:

```javascript
// Simple test runner
function test(description, fn) {
  try {
    fn();
    console.log(`PASS: ${description}`);
  } catch (e) {
    console.error(`FAIL: ${description} — ${e.message}`);
  }
}

function assert(condition, message) {
  if (!condition) throw new Error(message || 'Assertion failed');
}

// Tests
test('addTodo creates a todo with correct fields', () => {
  const app = new TodoApp();
  app.addTodo('Buy milk');
  assert(app.todos.length === 1, 'Should have 1 todo');
  assert(app.todos[0].text === 'Buy milk', 'Text should match');
  assert(app.todos[0].completed === false, 'Should start incomplete');
});

test('addTodo ignores empty input', () => {
  const app = new TodoApp();
  app.addTodo('   ');
  assert(app.todos.length === 0, 'Should not add whitespace-only todo');
});

test('toggleComplete flips completed status', () => {
  const app = new TodoApp();
  app.addTodo('Test task');
  const id = app.todos[0].id;
  app.toggleComplete(id);
  assert(app.todos[0].completed === true, 'Should be completed');
  app.toggleComplete(id);
  assert(app.todos[0].completed === false, 'Should be uncompleted again');
});

test('deleteTodo removes the correct item', () => {
  const app = new TodoApp();
  app.addTodo('Task 1');
  app.addTodo('Task 2');
  const id = app.todos[0].id;
  app.deleteTodo(id);
  assert(app.todos.length === 1, 'Should have 1 todo left');
  assert(app.todos[0].text === 'Task 2', 'Remaining todo should be Task 2');
});
```

**To run the tests:**
1. Open browser DevTools (F12) → Console tab
2. Open `todo-app/index.html` — test results appear automatically
3. All lines should show `PASS:`

**Checkpoint:** All tests pass in the browser console with no `FAIL:` lines.

> **Bob differentiator:** Bob generates tests that match the exact code it wrote — no manual wiring needed. The tests serve as living documentation of expected behavior.

---

## Lab 06 — Push to GitHub (Advanced mode, 10 min)

**Switch Bob to Advanced mode.**

Advanced mode unlocks MCP server access. The GitHub MCP server lets Bob
create repositories, commit, and push — all through natural language.

**Prompt for Bob:**
```
Use the GitHub MCP server to:
1. Initialize a git repository in the current directory
2. Create a .gitignore file for HTML/CSS/JS projects
3. Commit all files with message "Initial todo app implementation"
4. Create a new GitHub repository called "bob-todo-app"
5. Push the code and give me the repository URL
```

**What Bob does behind the scenes:**
```
"Initialize a git repo"     -> git init
"Create .gitignore"         -> writes .gitignore file
"Commit all files"          -> git add . && git commit -m "..."
"Create GitHub repo"        -> GitHub API creates repository
"Push the code"             -> git push origin main
```

> **Bob differentiator:** Advanced mode is the only mode with MCP server access.
> It includes all Code mode capabilities plus integration with external tools like
> GitHub, JIRA, databases, and any internal APIs your team exposes via MCP.

---

## Verification checklist

Open `todo-app/index.html` in your browser and run through these tests:

**Adding tasks**

| Action | Expected result |
|--------|----------------|
| Type a task and click Add | Task appears in the list |
| Press Enter on the input | Task appears in the list |
| Click Add with empty input | Nothing happens |

**Completing tasks**

| Action | Expected result |
|--------|----------------|
| Click the checkbox on a task | Text gets strikethrough, opacity drops |
| Click checkbox again | Task returns to active |

**Deleting tasks**

| Action | Expected result |
|--------|----------------|
| Click Delete on a task | Task is removed from the list |
| Delete all tasks | "No tasks yet" message appears |

---

## Congratulations!

You've used all four Bob modes to build and ship a complete Todo app.

### What you practised

| Bob mode | Used for |
|----------|----------|
| Plan     | Architecture and design decisions before coding |
| Code     | Generating files with auto-approvals and literate coding |
| Ask      | Understanding edge cases before fixing them |
| Code     | Creating and running unit tests |
| Advanced | GitHub integration via MCP server |

### Next steps
- Add local storage so tasks survive page refresh (Plan mode to design, Code mode to build)
- Add filter bar: All / Active / Completed
- Add drag-and-drop to reorder tasks

---

*Bob Bootcamp — MEA Watsonx Bootcamp — IBM Client Engineering*
