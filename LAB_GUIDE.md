# Lab Guide — Build a Todo App with Bob

**Duration:** 45 minutes | **Difficulty:** Beginner to Intermediate

---

## What you'll build

A fully functional dark-theme Todo app with:
- HTML structure with task input, list, and filter controls
- CSS with clean card layout and completed-task styling
- JavaScript TodoApp class with full state management
- Filter by All / Active / Completed
- Local storage persistence (tasks survive page refresh)
- GitHub repository via Bob's Advanced mode + MCP

---

## Lab timeline

```
Lab timeline (45 min)
├── Lab 01: Plan the structure       — Plan mode     (5 min)
├── Lab 02: Build HTML & CSS         — Code mode     (10 min)
├── Lab 03: Build the TodoApp class  — Code mode     (10 min)
├── Lab 04: Understand edge cases    — Ask mode      (10 min)
└── Lab 05: Push to GitHub           — Advanced mode (10 min)
```

---

## Lab 01 — Plan the structure (Plan mode, 5 min)

**Switch Bob to Plan mode.**

Before writing any code, ask Bob to design the project. Bob will ask
clarifying questions — this is intentional. Answer with: simple layout,
vanilla JS, no frameworks, local storage yes, no due dates.

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
- Three files: index.html, styles.css, todo.js
- Input field + Add button at the top
- Todo list with checkboxes and delete buttons
- Filter bar: All | Active | Completed
- TodoApp class with: todos (array), currentFilter
- Methods: addTodo, deleteTodo, toggleComplete, setFilter, render, saveToStorage, loadFromStorage

> **Bob differentiator:** Plan mode asks clarifying questions before planning.
> It lets you drive the process — Bob adapts to your requirements, not the other way around.

---

## Lab 02 — Build HTML & CSS (Code mode, 10 min)

**Switch Bob to Code mode.**

Enable auto-approvals so Bob creates both files without confirmation dialogs.

**Prompt for Bob:**
```
Create index.html and styles.css for a dark-theme Todo app.
Requirements:
- Input field with an "Add" button at the top
- Todo list where each item has: checkbox, task text, delete button
- Filter bar at the bottom: All | Active | Completed
- Counter showing number of active tasks remaining
- Dark theme: #1e1e1e background, #2d2d2d card, white text
- Completed tasks should have strikethrough text and reduced opacity
- Clean hover effects on buttons
Enable auto-approvals.
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
Create todo.js with a TodoApp class using literate coding.
Methods needed: addTodo(text), deleteTodo(id), toggleComplete(id),
setFilter(filter), getFilteredTodos(), render(),
saveToStorage(), loadFromStorage().

Use detailed comments explaining:
- Why each todo needs a unique ID (not just an index)
- Why we save to localStorage after every state change
- How filter state drives what gets rendered
- Why we re-render the whole list instead of patching individual items
```

**The unique ID pattern** (most important design decision):
```javascript
addTodo(text) {
    const todo = {
        id: Date.now(),
        // Using timestamp as ID avoids collisions when adding todos
        // quickly. An index would shift when items are deleted,
        // breaking references to specific todos.
        text: text.trim(),
        completed: false
    };
    this.todos.push(todo);
    this.saveToStorage();
    this.render();
}
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
Explain three things:
1. What happens if the user tries to add an empty todo?
2. Why can localStorage cause issues with large datasets or private browsing?
3. What other edge cases does a todo app need to handle and why?
```

**Then switch back to Code mode and ask:**
```
Apply these fixes to todo.js:
- Block empty or whitespace-only todos in addTodo()
- Handle localStorage being unavailable (private browsing / storage full)
- Prevent adding a todo by pressing Enter (not just the Add button)
- Show a "No tasks here" message when the filtered list is empty
```

**The empty input guard:**
```javascript
addTodo(text) {
    if (!text.trim()) return;
    // Silently ignore empty submissions rather than showing an alert —
    // pressing Enter on an empty field is a common accidental action
    // and an error message would be disruptive.
    ...
}
```

> **Bob differentiator:** Ask mode is intentionally read-only — it encourages
> understanding before fixing. You'll write better code when you know why bugs happen.

---

## Lab 05 — Push to GitHub (Advanced mode, 10 min)

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
"Initialize a git repo"     → git init
"Create .gitignore"         → writes .gitignore file
"Commit all files"          → git add . && git commit -m "..."
"Create GitHub repo"        → GitHub API creates repository
"Push the code"             → git push origin main
```

> **Bob differentiator:** Advanced mode is the only mode with MCP server access.
> It includes all Code mode capabilities plus integration with external tools like
> GitHub, JIRA, databases, and any internal APIs your team exposes via MCP.

---

## Verification checklist

Open `index.html` in your browser and run through these tests:

**Adding tasks**

| Action | Expected result |
|--------|----------------|
| Type a task and click Add | Task appears in the list |
| Press Enter on the input | Task appears in the list |
| Click Add with empty input | Nothing happens |
| Add 3 tasks | Counter shows "3 tasks left" |

**Completing tasks**

| Action | Expected result |
|--------|----------------|
| Click the checkbox on a task | Text gets strikethrough, opacity drops |
| Click checkbox again | Task returns to active |
| Complete 1 of 3 tasks | Counter shows "2 tasks left" |

**Filtering**

| Filter | Expected result |
|--------|----------------|
| All | Shows every task |
| Active | Shows only uncompleted tasks |
| Completed | Shows only completed tasks |

**Persistence**

| Action | Expected result |
|--------|----------------|
| Add tasks, then refresh the page | Tasks are still there |
| Complete a task, then refresh | Completed state is preserved |

---

## Congratulations!

You've used all four Bob modes to build and ship a complete Todo app.

### What you practised

| Bob mode | Used for |
|----------|----------|
| Plan     | Architecture and design decisions before coding |
| Code     | Generating files with auto-approvals and literate coding |
| Ask      | Understanding edge cases before fixing them |
| Advanced | GitHub integration via MCP server |

### Next steps
- Add due dates to tasks (use Plan mode to design, Code mode to build)
- Add drag-and-drop to reorder tasks
- Add categories or tags to group tasks
- Try Lab 2: Security Analysis →

---

*Bob Bootcamp — MEA Watsonx Bootcamp — IBM Client Engineering*
