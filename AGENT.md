# AGENT Instructions — TypeScript Course File Enrichment

## 1. Scope of Work

### Target Files
Only enrich files that satisfy **all** of these conditions:
- Located under `TypeScript_Domination_Full_Course/`
- Path is **between** `00h00m00s_Intro/Intro.md` and `01h17m59s_Intersection_Types/Intersection_Types.md` (inclusive)
- **Already non-empty** (skip files that are empty or contain only whitespace)
- **Never** touch files outside this range (e.g. Classes, Functions, Generics, Modules, Type Assertions, Type Guards, Upcoming_Stuff)


## 2. Content Requirements (per file)

Each enriched file **must** contain:

### 2.1 Category Overview
- Brief high-level introduction explaining what the topic covers and why it matters in TypeScript.

### 2.2 Detailed Examples (10+ per file)
- Minimum **10 distinct code examples** per file.
- Use real-world or instructive scenarios; avoid trivial `foo/bar` examples.
- Each code block must be fenced with language identifier (`typescript` or `bash`).
- Add brief preceding explanation for each example.

### 2.3 Tricky Things to Remember
- List of subtle or hard-to-grasp aspects of the topic.
- Include **edge-case scenarios** with code demonstrating each tricky point.
- Aim for 3-5 items per file.

### 2.4 Analogies
- 1-3 real-world analogies to make abstract concepts concrete.

### 2.5 Interview Questions (10 per file)
- Exactly **10 interview questions** with **full answers and explanations**.
- Questions should range from basic to advanced.
- Provide the answer immediately after the question. Show code where applicable.
- Explain *why* something works or fails.

### 2.6 Structure Constraints
- Well-structured markdown: headings, subheadings, tables, code fences, lists.
- **No emojis** anywhere in the file.
- **No code comments** added to examples (the code itself should be self-explanatory).

---

## 3. Commit Rules

### 3.1 Grouping
Files must be committed **in the same groups** as the original git history:

| Group Name | Original Commit Message |
|-----------|------------------------|
| **basic data type** | `Current checkpoint : basic data type` |
| **type inference** | `Current checkpoint : type inference` |
| **interfaces upcoming** | `Current checkpoint : interfaces upcoming` |
| **OOP upcoming** | `Current checkpoint : OOP upcoming` |

### 3.2 Commit Messages
- **First enrichment round**: Use format `Expanded: <group name> group with Q&A and examples`
  - e.g., `Expanded: basic data type group with Q&A and examples`
- **Subsequent rounds**: Use a short, descriptive prefix explaining what changed
  - e.g., `Fixed: <description>`, `Updated: <description>`
- Only add files that were actually changed in that round.
- Do NOT commit untargeted files (files outside the specified range).

### 3.3 Commit Hygiene
- Inspect `git status`, `git diff --cached`, `git add -A` before committing.
- Use `--no-verify` to skip hooks if they reject (only if the rejection is unrelated to content quality).
- CRLF line-ending warnings from git on Windows are benign and can be ignored.

---

## 4. Procedural Rules

### 4.1 Read Before Write
Always read the current file contents before editing.

### 4.2 No New Files
**Do not create new files** unless explicitly requested by the user. Always edit existing files.

### 4.3 No Documentation Files
Do not create *.md, README, or documentation files unless directly instructed.

### 4.4 No Emojis
Never use emojis in any output or file content.

### 4.5 No Code Comments
Do not add comments (`//`, `/* */`) inside code examples.

### 4.6 Verification
After writing all files for a commit group, run:
```
git status
git diff --cached --stat
```
Verify the correct files are staged before committing.

### 4.7 One Group at a Time
Edit and commit one group fully before moving to the next. Do not batch across groups.

---

## 5. File Editing Best Practices

- Use `edit` tool (exact string replacement) for small surgical changes.
- Use `write` tool to overwrite a file when rewriting its entire content.
- Always pass absolute paths (e.g., `G:\code\Typescript\TypeScript_Domination_Full_Course\...`).
- Use forward slashes or double-backslashes in paths as appropriate for the tool.
