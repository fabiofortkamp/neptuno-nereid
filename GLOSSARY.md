# Nereid

Nereid is a local-first task manager: OmniFocus's model of folders, projects
and defer/due dates, plus cross-project dependencies and repeating tasks that
can be checked or finished.

## Language

### Containers

**Folder**:
A container for projects and other folders.

**Project**:
A container for tasks, either *sequential* (one task at a time, in order) or
*parallel* (all tasks at once).
_Avoid_: List

**Inbox**:
The place for tasks not yet assigned to a project.

### Dates

**Defer date**:
The moment before which a task is not available.
_Avoid_: Start date

**Due date**:
A task's deadline. Past it, the task is *overdue*: a flag, not a state.

### Availability

**Available**:
A task that can be worked on now.
_Avoid_: Ready, actionable

**Blocked**:
A task that would be available except for an unfinished prerequisite.
_Avoid_: Waiting (as a state)

### Dependencies

**Dependency**:
A relation where task B *waits on* task A: B is blocked until A is completed
or dropped.

**Prerequisite**:
A task another task waits on.
_Avoid_: Blocker, parent

**Waiting on**:
The list of a task's prerequisites, as shown to the user.

### Repeats

**Check**:
Completing one occurrence of a repeating task; the task stays active and its
dates advance.
_Avoid_: Complete (for one occurrence)

**Finish**:
Completing a repeating task for good; it stops repeating and unblocks its
dependents.
_Avoid_: Complete (for a repeating task), end

**Occurrence due**:
The due date of each occurrence of a repeating task; advances on Check.

**Series due**:
The deadline for Finishing a repeating task; never advances.
