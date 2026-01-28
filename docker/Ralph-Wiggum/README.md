# Ralph Wiggum Loop

## Description

The Ralph Wiggum workflow is defined by its looped, stateless nature. It treats the AI agent not as a conversational partner, but as a pure function: $f(State, Task) \rightarrow Patch$.

In this architecture, the "memory" of the project is not stored in the neural network's activation states, but in the Git History and the File System. The agent wakes up, reads the current file state, executes one discrete task (e.g., "Fix compilation error in UserService.java"), validates the work via the compiler, commits the change, and effectively "dies." The next iteration spawns a fresh agent. This ensures that the 100th refactoring task receives the exact same high-fidelity attention as the first.
