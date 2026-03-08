---
name: todo
description: Record and update todo items in Todo.md
---

You are managing the user's todo list in `Todo.md` at the repo root.

## Steps

1. **Read Todo.md**: Read `Todo.md` if it exists. If it doesn't, create it with this structure:
   ```markdown
   ---
   tags: []
   ---

   # Todo

   <!-- Managed by Claude. Use /todo to add, check off, or update items. -->
   ```

2. **Parse the request**: Interpret `$ARGUMENTS` as a natural instruction. Examples:
   - "buy groceries" → add a new item
   - "done: buy groceries" → check it off
   - "remove: buy groceries" → delete it
   - "list" or no arguments → show current todos
   - "clean" → remove all completed items

3. **Apply changes to Todo.md**:

   a. **Adding items**: Append as `- [ ] <item>` under the appropriate section. If no sections exist, just append to the list.

   b. **Completing items**: Change `- [ ]` to `- [x]` for matching items. Match loosely — "done: groceries" should match "- [ ] buy groceries".

   c. **Removing items**: Delete the matching line entirely.

   d. **Listing**: If the user just wants to see their todos, read and display them. No file changes needed, no commit needed.

   e. **Cleaning**: Remove all lines with `- [x]`.

4. **Commit and push** (only if Todo.md was modified):
   - Stage `Todo.md`
   - Commit with message: `Update todo list`
   - Push to main

5. **Summary**: Show the current state of the todo list after changes.
