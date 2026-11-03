# AGENTS.md

## 1. Project Objective

Develop a conversational, multi-agent AI system that analyzes dashboard screenshots, generates meaningful insights, and communicates with users in Thai through a chat-based web application.

## 2. Mandatory Instruction Review

- At the beginning of every task or Codex execution session, read both `AGENTS.md` and `REQUIREMENTS.md` completely.
- This requirement also applies when resuming a previous Codex session.
- Do not rely solely on instructions or requirements remembered from previous conversations.
- Always use the latest versions of these files as the authoritative project instructions.
- Before beginning implementation, confirm that both files have been reviewed.
- If either file is missing, inaccessible, or contains conflicting instructions, stop and request human clarification.
- Do not begin implementation until the required instruction review is complete.

## 3. Human Authority

- The human user retains full authority over project decisions.
- Codex acts only as an AI-assisted developer.
- Codex must follow the scope and instructions explicitly provided by the human user.
- Do not independently introduce major changes to project scope, architecture, or development direction.
- Request human approval before implementing significant changes that have not been authorized.

## 4. Workspace Boundaries

- The current project working directory is the only authorized workspace.
- Do not read, inspect, create, modify, or delete files outside the project working directory.
- Do not explore parent directories, sibling projects, or unrelated filesystem locations.
- Do not access files outside the working directory through absolute paths, symbolic links, or other indirect methods.
- If a task requires access outside the authorized workspace, stop and inform the human user.
- Never attempt to bypass sandbox restrictions or request expanded filesystem permissions.

## 5. Git Operations

- All Git version-control operations are managed manually by the human user.
- Do not execute `git add`, `git commit`, `git push`, `git pull`, `git merge`, or other Git commands.
- Do not modify Git configuration, branches, or repository history.
- Do not create commits or push changes to remote repositories.
- Codex is responsible for producing and modifying project files only.
- The human user will independently review and manage version control.

## 6. Development Control

- Perform only development tasks explicitly authorized by the human user.
- Keep implementation changes limited to the requested scope.
- Make all development work transparent and easy to inspect.
- Do not proceed with additional development stages without human authorization.
- Report any unexpected behavior, technical limitations, or required changes before proceeding beyond the approved scope.

## 7. Mandatory Development Explanation

After completing every development task, Codex must provide a detailed explanation of the work performed.

The explanation must contain the following sections:

### 7.1 Task Summary

- Describe the objective of the task.
- Explain what was implemented or modified.
- State whether the task was completed successfully, partially completed, or blocked.

### 7.2 Files Created or Modified

- List every file created, modified, or deleted.
- Explain the purpose of each file.
- Describe the significant changes made to each file.

### 7.3 Implementation Explanation

- Explain how the implementation works.
- Describe the important functions, classes, modules, or components involved.
- Explain how the changes interact with existing project components.
- Identify important design decisions and their reasons.

### 7.4 Testing and Verification

- Describe the tests performed.
- Identify the test files or Jupyter Notebooks used.
- Explain the test procedures.
- Report the actual results.
- Distinguish tests that passed, failed, or were not executed.
- Never claim that a test passed unless it was actually executed and its result was verified.

### 7.5 Issues and Limitations

- Identify any errors or unresolved problems.
- Explain known limitations.
- Describe deviations from the approved implementation plan.
- Identify any requirements that could not be satisfied.

### 7.6 Human Review Required

- Identify decisions requiring human approval.
- Explain what the human user should review.
- Suggest the next development step without executing it automatically.

All explanations must be sufficiently detailed for the human user to understand, inspect, reproduce, and debug the work performed.

Do not replace detailed explanations with short completion messages such as "Done" or "Implemented successfully."

## 8. Development Traceability

- Keep changes and test results traceable to the authorized task.
- Clearly identify which requirements are addressed by the implementation.
- Make important implementation decisions understandable to the human user.
- Report any unexpected changes made during development.
- Do not hide failed tests, unsuccessful experiments, or implementation limitations.
- Preserve sufficient information for subsequent debugging and human review.