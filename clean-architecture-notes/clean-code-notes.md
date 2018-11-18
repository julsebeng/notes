# Clean Code
## Table of Contents
- [ Functions ]( #functions-should... )

---

## Functions Should...
### Be Small
- The fewer lines of code in a function, the better. It's fine to have functions that only have a few lines of code if they're
meaningful.
- Applying this principle to control flow structures like `if` and `while` means that each path's block should only be a few
lines long, **ideally nothing more than a function call**.
    - The fewer control flow structures used, the better.
    - Nested structures should be avoided - no more than two levels of indention should be the goal.

### Do One Thing
- A function should do **one** thing well, and **only** one thing. It should not imply that it does only a certain task only to do
more than that task.
- "One thing" can be described as everything on the same level of abstraction needed to be done to finish a task.
    - This can be decomposed using a series of "to" phrases:
      ```
      TO do X, first validate A, get the result from B, then calculate the return value with C.
        TO do A, check if the input given is set; if it is, do AA, else do AB.
            TO do AA, ...
            TO do AB, ...
        TO do B, ...
        TO do C, ...
      ```
      - For example, `getHtml()` is a high level of abstraction, `String pagePath = PathParser.render(pagepath)` is an intermediate
      level, and `.append("\n")` would be at a low level.

### Have Descriptive Names
- A long, descriptive name is better than a short, vague one; a name should be self-documenting.
- It's okay to spend some time figuring out the most appropriate and descriptive name for a function; try different names and
read the code with each name to figure out which is most appropriate.

## Take Reasonable Arguments
- A function should have as few arguments as possible, and ideally should never have more than 3 outside of special circumstances.
- An argument should not also be a return value - references that are passed in should not be changed and used as one of the
return values of the function.
- For monadic functions (functions that take one argument), there are 3 common situations that they are useful for:
    - Asking a question about the variable: `IsValid( input_string)`
    - Modifying a variable and returning the changes: `input = TrimWhitespace( input )`
    - Responding to an event that has occurred: `PasswordAttemptFailedNTimes( attempts )`
        - These types of situations do not return a value but they take input and modify their state. *It should be made clear
        that these functions are event responders.*
