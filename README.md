# Bob Bootcamp — Build a Todo App

**Duration:** 45 minutes | **Difficulty:** Beginner to Intermediate | **Lab:** 1

---

## What you'll build

A fully functional dark-theme Todo app with three files:

- `index.html` — the input, list, and filter controls
- `styles.css` — the dark theme, layout, and completed-task styling
- `todo.js` — all the logic (add, complete, delete, filter, persist)

---

## Learning objectives

By the end of this lab, you will:

✅ Understand Bob's four modes (Plan, Code, Ask, Advanced)
✅ Use auto-approvals for rapid development
✅ Practice literate coding techniques
✅ Integrate GitHub using MCP servers
✅ Build a complete Todo application with local storage persistence

---

## Prerequisites

Before starting, ensure you have:

- [ ] Git installed and configured
- [ ] Bob installed and running
- [ ] GitHub account (for MCP integration)
- [ ] A browser to test the app

---

## Understanding Bob's modes

Bob has four distinct modes, each optimized for different tasks:

> 🎯 **Bob Differentiator: Customizable Modes** Bob's mode system is one of its key differentiators. Unlike other AI assistants, Bob allows you to create custom modes tailored to your team's specific workflows.

**🎯 Plan mode** — When to use: Planning, designing, strategizing
- Create project structures
- Design layouts and architecture
- Plan class design and state management
- Make decisions before writing code

**💻 Code mode** — When to use: Writing, modifying, refactoring code
- Implement features
- Create files
- Modify existing code
- Fix bugs

**❓ Ask mode** — When to use: Learning, understanding, getting help
- Explain code concepts
- Understand edge cases
- Learn best practices

**⚡ Advanced mode** — When you need external tools
- Everything Code mode does
- Plus MCP servers (GitHub, JIRA, databases, internal APIs)

**Switching between modes**

In Bob's interface:
1. Look for the mode selector at the bottom of the chat panel
2. Click to see available modes
3. Select the mode you need — Bob will adapt its behaviour accordingly

💡 **Tip:** You can switch to Ask mode at any point if something is unclear. Example questions to try:
- *"Why does each todo need a unique ID instead of an index?"*
- *"How does the filter state control what gets rendered?"*
- *"Why do we save to localStorage after every state change?"*
- *"What happens if the user adds an empty task?"*

---

## Lab timeline

```
Lab 1 (45 min)
├── Lab 01: Plan the structure       — Plan mode     (5 min)
├── Lab 02: Build HTML & CSS         — Code mode     (10 min)
├── Lab 03: Build the TodoApp class  — Code mode     (10 min)
├── Lab 04: Understand edge cases    — Ask mode      (10 min)
└── Lab 05: Push to GitHub           — Advanced mode (10 min)
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

Bob will ask clarifying questions. Answer with: simple layout, vanilla JS, no frameworks, local storage yes, no due dates.

**Expected output from Bob:**
- Three files: `index.html`, `styles.css`, `todo.js`
- Input field + Add button at the top
- Todo list with checkboxes and delete buttons
- Filter bar: All | Active | Completed
- TodoApp class with: `todos` (array), `currentFilter`
- Methods: `addTodo`, `deleteTodo`, `toggleComplete`, `setFilter`, `render`, `saveToStorage`, `loadFromStorage`

> 🎯 **Bob differentiator:** Plan mode asks clarifying questions before planning. It lets you drive the process — Bob adapts to your requirements, not the other way around.

---

## Lab 02 — Build HTML & CSS (Code mode, 10 min)

**Switch Bob to Code mode. Enable auto-approvals.**

**What are auto-approvals?**

Normally Bob asks "can I create this file?" before every file it makes. Auto-approvals skip those confirmation dialogs so Bob can create multiple files in one go.

**How to enable:** Look for the auto-approval toggle at the bottom of Bob's interface and switch it on.

⚠️ **Important:** Review the files after Bob creates them to ensure they match your requirements.

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

✅ **Checkpoint:** Open `index.html` in your browser. You should see the dark Todo app layout with input, list area, and filter bar. Nothing is functional yet — that comes in Lab 03.

> 🎯 **Bob differentiator:** In Code mode with auto-approvals, Bob creates real files directly in your project folder — not just code snippets for you to copy and paste manually.

---

## Lab 03 — Build the TodoApp class (Code mode, 10 min)

**Stay in Code mode.**

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

> 🎯 **Bob differentiator:** Literate coding makes your codebase self-documenting. Great for onboarding teammates and for your own future reference.

---

## Lab 04 — Understand edge cases (Ask mode, 10 min)

**Switch Bob to Ask mode.**

Ask mode explains without touching your files. Understand the edge cases first, then switch back to Code mode to fix them.

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
- Allow adding a todo by pressing Enter (not just the Add button)
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

> 🎯 **Bob differentiator:** Ask mode is intentionally read-only — it encourages understanding before fixing. You'll write better code when you know why edge cases happen.

---

## Lab 05 — Push to GitHub (Advanced mode, 10 min)

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
"Initialize a git repo"     → git init
"Create .gitignore"         → writes .gitignore file
"Commit all files"          → git add . && git commit -m "..."
"Create GitHub repo"        → GitHub API creates repository
"Push the code"             → git push origin main
```

✅ **Checkpoint:** Your code is live on GitHub.

> 🎯 **Bob differentiator:** Advanced mode is the only mode with MCP server access. Bob connects to external tools like GitHub through natural language — no terminal switching required.

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

## Congratulations! 🎉

| What you did | Bob mode used |
|--------------|--------------|
| Designed the app structure before coding | Plan mode |
| Created HTML and CSS files | Code mode + auto-approvals |
| Built the TodoApp class with literate coding | Code mode |
| Understood and fixed edge cases | Ask mode → Code mode |
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

- Add due dates to tasks (use Plan mode to design, Code mode to build)
- Add drag-and-drop to reorder tasks
- Add categories or tags to group tasks
- Continue to **Lab 2: Security Analysis →**

---

## Troubleshooting

**Tasks do not appear when added**
- Open browser console (F12) and check for red errors
- Confirm `todo.js` is linked at the bottom of `index.html`

**Filter buttons do nothing**
- Check that `setFilter()` calls `render()` after updating `this.currentFilter`

**Tasks lost on page refresh**
- Check browser console for localStorage errors
- Confirm `saveToStorage()` is called inside `addTodo`, `deleteTodo`, and `toggleComplete`

**GitHub push fails**
- Confirm you are in **Advanced mode**, not Code mode
- Check that the GitHub MCP server is connected in Bob's settings
- Confirm your GitHub personal access token has `repo` permissions

---

## Repository structure

```
bob-bootcamp-todo/
├── docs/
│   └── screenshots/
└── lab1/
    ├── starter/           ← Open this folder to begin
    │   ├── index.html
    │   ├── styles.css
    │   └── todo.js
    └── solution/
        └── todo-app/
            ├── index.html
            ├── styles.css
            └── todo.js
```

---

*Bob Bootcamp · MEA Watsonx Bootcamp · IBM Client Engineering*
