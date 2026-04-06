# Bob Bootcamp — Build a Todo App

**Duration:** 45 minutes | **Difficulty:** Beginner to Intermediate | **Lab:** 1

---

## What you'll build

A clean Todo app inside a `todo-app/` folder with three files:

- `index.html` — the input field and task list
- `styles.css` — the dark theme, card layout, and completed-task styling
- `app.js` — all the logic (add, complete, delete, render)

---

## Learning objectives

By the end of this lab, you will:

- Understand Bob's four modes (Plan, Code, Ask, Advanced)
- Use auto-approvals for rapid development
- Practice literate coding techniques
- Integrate GitHub using MCP servers
- Build a Todo app using a clean class-based state machine

---

## Prerequisites

Before starting, ensure you have:

- [ ] Git installed and configured
- [ ] Bob installed and running
- [ ] GitHub account (for MCP integration)
- [ ] A browser to test the app

---

## Understanding Bob's Modes

Bob has three distinct modes, each optimized for different tasks:

> **Bob Differentiator: Customizable Modes** — Bob's mode system is one of its key differentiators. Unlike other AI assistants, Bob allows you to create custom modes tailored to your team's specific workflows. The three built-in modes you'll use in this lab are just the beginning — you can create specialized modes for code review, documentation, architecture design, and more.

**🎯 Plan Mode** — When to use: Planning, designing, strategizing
- Create project structures
- Design API endpoints
- Plan database schemas
- Make architectural decisions

**💻 Code Mode** — When to use: Writing, modifying, refactoring code
- Implement features
- Create files
- Modify existing code
- Fix bugs

**❓ Ask Mode** — When to use: Learning, understanding, getting help
- Explain code concepts
- Get documentation
- Understand errors
- Learn best practices

**Switching Between Modes**

In Bob's interface:
1. Look for the mode selector (usually at the top)
2. Click to see available modes
3. Select the mode you need
4. Bob will adapt its behavior accordingly

**Tip:** You can switch to Ask mode at any point if something is unclear. Example questions to try:
- *"Why does each todo need a unique ID instead of an index?"*
- *"What happens when render() is called — why rebuild the whole list?"*
- *"What happens if the user adds an empty task?"*

---

## Lab timeline

```
Lab 1 (55 min)
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

Bob will ask some clarifying questions — this is intentional. For example:

- *"What features would you like in your Todo app?"* → Choose **Basic features: Add todos, mark as complete, delete todos**
- *"Do you want todos to persist when the page is refreshed?"* → Choose **No persistence needed — todos can be lost on refresh**

**Expected output from Bob:**
- Folder: `todo-app/` containing `index.html`, `styles.css`, `app.js`
- Input field + Add button at the top
- `ul#todoList` — renders todo items dynamically
- TodoApp class with: `todos` (array), `nextId` (counter)
- Todo object shape: `{ id, text, completed }`
- Methods: `addTodo(text)`, `toggleComplete(id)`, `deleteTodo(id)`, `render()`, `init()`
- Data flow: User action → method call → state update → `render()`

> **Bob differentiator:** Plan mode asks clarifying questions before planning. It lets you drive the process — Bob adapts to your requirements, not the other way around.

---

## Lab 02 — Build HTML & CSS (Code mode, 10 min)

**Switch Bob to Code mode. Enable auto-approvals.**

**What are auto-approvals?**

Normally Bob asks "can I create this file?" before every file it makes. Auto-approvals skip those confirmation dialogs so Bob can create multiple files in one go.

**How to enable:** Look for the auto-approval toggle at the bottom of Bob's interface and switch it on.

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

**Checkpoint:** Open `todo-app/index.html` in your browser. You should see the dark Todo app layout. Nothing is functional yet — that comes in Lab 03.

> **Bob differentiator:** In Code mode with auto-approvals, Bob creates real files directly in your project folder — not just code snippets for you to copy and paste manually.

---

## Lab 03 — Build the TodoApp class (Code mode, 10 min)

**Stay in Code mode.**

**Prompt for Bob:**
```
Create todo-app/app.js with a TodoApp class using literate coding.
Methods needed: addTodo(text), toggleComplete(id), deleteTodo(id), render(), init().

Use detailed comments explaining:
- Why each todo needs a unique ID (not just an array index)
- How the data flow works: user action -> method -> state update -> render()
- Why we re-render the whole list instead of patching individual items
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
    // State is always the single source of truth.
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

> **Bob differentiator:** Literate coding makes your codebase self-documenting. Great for onboarding teammates and for your own future reference.

---

## Lab 04 — Understand edge cases (Ask mode, 10 min)

**Switch Bob to Ask mode.**

Ask mode explains without touching your files. Understand the edge cases first, then switch back to Code mode to fix them.

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
  ...
}
```

> **Bob differentiator:** Ask mode is intentionally read-only — it encourages understanding before fixing. You'll write better code when you know why edge cases happen.

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

**Checkpoint:** Your code is live on GitHub.

> **Bob differentiator:** Advanced mode is the only mode with MCP server access. Bob connects to external tools like GitHub through natural language — no terminal switching required.

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

| What you did | Bob mode used |
|--------------|--------------|
| Designed the app structure before coding | Plan mode |
| Created HTML and CSS files | Code mode + auto-approvals |
| Built the TodoApp class with literate coding | Code mode |
| Understood and fixed edge cases | Ask mode + Code mode |
| Created and ran unit tests | Code mode |
| Pushed the project to GitHub | Advanced mode + MCP |

---

## Key takeaways

**Plan mode** — Always plan before you build. Bob's clarifying questions ensure it builds exactly what you need.

**Code mode + auto-approvals** — Bob creates real files in your project, not snippets. Auto-approvals let it work through multiple files without stopping.

**Literate coding** — Comments that explain *why*, not just *what*. Makes your code readable for teammates and your future self.

**Ask mode** — When you don't understand something Bob built, Ask mode explains it without touching your files.

**Advanced mode + MCP** — Bob connects to external tools like GitHub through natural language. No terminal switching required.

---

## Next steps

- Add local storage so tasks survive page refresh (Plan mode to design, Code mode to build)
- Add a filter bar: All / Active / Completed
- Add drag-and-drop to reorder tasks

---

## Troubleshooting

**Tasks do not appear when added**
- Open browser console (F12) and check for red errors
- Confirm `app.js` is linked at the bottom of `index.html`

**Checkbox does nothing**
- Check that `toggleComplete()` calls `render()` after updating `todo.completed`

**Delete button removes wrong item**
- Confirm you are passing `todo.id` (not the array index) to `deleteTodo()`

**GitHub push fails**
- Confirm you are in **Advanced mode**, not Code mode
- Check that the GitHub MCP server is connected in Bob's settings
- Confirm your GitHub personal access token has `repo` permissions

---

## Repository structure

```
todo-app/
├── index.html
├── styles.css
└── app.js
```

---

*Bob Bootcamp · MEA Watsonx Bootcamp · IBM Client Engineering*
