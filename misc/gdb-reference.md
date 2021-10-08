### Debugging Multi-Threaded Applications
#### Setup
1. Start GDB up *without* attaching to a process. `non-stop` can't be enabled once a process is being debugged.
2. execute the commands:
```
set pagination off
set target-async on
set non-stop on
```
3. Attach to the process via `attach <PID>`

#### Commands
- `continue -a`: continue all threads
- `continue&`: continue current thread but keep the GDB prompt active.
	- `next&` and `step&` are also supported.
- `thread apply <thread numbers> <command>`: apply a certain command to a range of threads, by ID.
	- Ex. `thead apply 1 2 3 bt`
