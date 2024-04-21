---
description: Some basic usage
---

# pdb

If we have access to source code, then we can add `breakpoint()` into the code. `breakpoint()` starts a pdb session from the line right after current breakpoint() line.

```bash
# Print the variables, e.g. x
(Pdb) p x
# Can print any variables in valid python experssion. e.g. tuple
(Pdb) p (x, lst)

# See call stack (outer to inner). or `where`
(Pdb) w
# Change the python frame we are looking at
(Pdb) u
# Scroll down current framework. s.t. can see the current frame. or `down`
(Pdb) d

# See current pdb pointer position (show +- 11 lines)
(Pdb) l
# Then type one more l will scroll down 11 lines
(Pdb) l
# Back to the current line
(Pdb) l .
# Show all lines of current function
(Pdb) ll

# Execute one more line of code
(Pdb) n
# Step into. or `step`
(Pdb) s
# Show function return value
(Pdb) retval
# Execute until the line is larger than current line (to jump out of loop)
(Pdb) until
# Until command can pass argument. e.g execute until line no. >= 10
(Pdb) until 10 
# Execute until that before the function returns
(Pdb) r

# Keep running til next breakpoint
(Pdb) c
# Exit debuggine session
(Pdb) q
```

Users can run any python expression in pdb session

```bash
(Pdb) lst = [1, 2, 3]
# Then the lst will show [1, 2, 3]
(Pdb) p lst 
```

If we dont have the access to source code, for example, when we wan to look into certain python library the program depends. Then we can add a breakpoint, and step into the library call entry point to see what happens inside.&#x20;

While another approach is that, run the program with pdb

```bash
python -m pdb example.py
```

The program will enter debugging session, we can specify the breakpoint via pdb

```bash
# Set a breakpoint in line 5
(Pdb) b 5
# Show all breakpoints
(Pdb) break
# Delete a breakpoint
(Pdb) clear <breakpoint no.>
```
